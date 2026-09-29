# Direct PDF Reference Design (v2)

## Goals

- Compile with pdfLaTeX alone; no Python or reference index.
- Keep the library split into reusable `notes-common.sty` and project-specific
  `notes-project.sty`.
- Construct deployed PDF links entirely from an explicit reference.
- Use stable semantic IDs so inserting an earlier item does not break links.
- Use `section.item` numbering because every PDF contains one lecture or
  tutorial.

## Files and numbering

Lecture, tutorial, and demonstration files use these names:

```text
lec01.tex  -> latex_target/lec01.pdf
tut01.tex  -> latex_target/tut01.pdf
example.tex -> latex_target/example.pdf
```

Ordinary `\section` headings control the first number. Definitions, remarks,
and examples have independent counters under each section:

```text
1 Numbers
  Definition 1.1
  Definition 1.2
  Remark 1.1

2 Fields
  Definition 2.1
```

Each environment creates its typed label and a stable PDF destination:

```tex
\begin{dfn}[Open Set]{open-set}
...
\end{dfn}
```

This produces the LaTeX label `def:open-set` and the PDF destination
`notes.def.open-set`. The semantic ID is optional when a plain-text title is
present:

```tex
\begin{dfn}[Open Sets] % automatically uses def:open-sets
```

Automatic IDs lowercase the title and replace non-alphanumeric runs with a
dash. Explicit IDs are recommended when a title contains LaTeX markup or may
be renamed.

## Document selection

`\xrefuse` selects one default document for short references:

| Syntax | Selected document |
|---|---|
| `\xrefuse{lec01}` | Current project |
| `\xrefuse{topology::lec01}` | Another project on the same server |
| `\xrefuse{https://other.example::topology::lec01}` | Another server |

If no document is selected, a short reference uses the current PDF. If more
than one document is selected, short references are rejected as ambiguous;
use a qualified reference instead.

## Reference scopes

| Syntax | Scope |
|---|---|
| `\xref{def:integer}` | Current or selected document |
| `\xref[closed operations]{def:closed-operation}` | Custom clickable words |
| `\xref{lec01::def:integer}` | Current project |
| `\xref{topology::lec01::def:open-set}` | Current server |
| `\xref{https://other.example::topology::lec01::def:open-set}` | Explicit server |

The optional square-bracket argument changes only the visible words; target
resolution is unchanged. It works at every scope:

```tex
\xref[closed operations]{def:closed-operation}
\xref[open sets]{topology::lec03::def:open-set}
```

For example:

```tex
\xref{topology::lec03::def:open-set}
```

is rendered as `definition open set` and points to:

```text
https://notes.rua.rs/topology/lec03.pdf#nameddest=notes.def.open-set
```

Supported kinds are `def`, `thm`, `axm`, `prop`, `lem`, `cor`, `rem`, `eg`,
`ex`, and `conv`. Comma-separated references and the unlinked `\xref*` form are
also supported.

## Optional subject notation modules

A course may add a project-specific package such as
`real-analysis-notations.sty` or `qm-notations.sty`. This is optional and does
not change the two required library files. Load it from `notes-project.sty`
after `notes-common`.

Notation macros may place `\xref` around only the stable symbol or operator
head. Arguments and subscripts remain outside the link, avoiding nested
hyperlinks. Reference targets may be stored in overridable macros because
`\xref` expands its mandatory argument before parsing it.

Use a project-qualified default target when another subject may reuse the
module:

```tex
\providecommand*{\supremumRef}{real-analysis::lec01::def:supremum}
\renewcommand*{\sup}{%
  \mathop{\xref[\mathrm{sup}]{\supremumRef}}\nolimits
}
```

A dependent course should vendor or otherwise track the subject package in its
own repository and then load it normally. Do not rely on `../` paths into a
sibling checkout; those paths are not portable to another machine or build
environment. The fully qualified default keeps the link pointing back to the
owning subject. Override the target before package loading only when the
definition lives elsewhere.

## Number-display tradeoff

A local semantic reference is resolved by `cleveref`, so it displays its
actual number, such as `definition 2.1`. A reference into another PDF cannot
know that PDF's current number without metadata, so it displays the semantic
name instead. Its stable link remains correct after renumbering. The archived
JSON-based v1 remains available locally under ignored `v1/` if automatic
cross-PDF number display is needed later.
