# Real Analysis

LaTeX source and published PDFs for AMA3707 Real Analysis.

## Compile

Open `lec01.tex` or `example.tex` in VS Code with LaTeX Workshop and press
**Build LaTeX project**. Finished PDFs go directly to `latex_target/`, while
temporary LaTeX files stay in the ignored `.build/` directory.

Equivalent terminal commands:

```sh
latexmk -pdf lec01.tex
latexmk -pdf example.tex
```

## File convention

| Material | Source and PDF names |
|---|---|
| Lecture | `lec01.tex`, `lec01.pdf` |
| Tutorial | `tut01.tex`, `tut01.pdf` |
| Linkage example | `example.tex`, `example.pdf` |

Use two-digit, zero-padded filenames. Every file contains one lecture or
tutorial. Ordinary sections organize its content, such as `1 Numbers` and
`2 Fields`.

Definitions, theorems, propositions, lemmas, corollaries, remarks, examples,
exercises, and conventions have independent counters within each section.
Consequently, section 2 may contain Definition 2.1, Proposition 2.1, and
Exercise 2.1.

## Two-layer library

Every document loads:

```tex
\usepackage{notes-project}
```

| File | Purpose |
|---|---|
| `notes-common.sty` | Reusable formatting, section-based environments, and direct PDF-reference macros. |
| `notes-project.sty` | Real Analysis server URL and project name. |

There is no Python build helper, JSON reference index, or remote AUX file.

## References

Give an item a stable semantic ID directly on its environment:

```tex
\begin{dfn}[Integers]{integer}
...
\end{dfn}
```

This automatically creates `def:integer`. The ID may be omitted; a plain-text
title is then converted to lowercase kebab-case:

```tex
\begin{dfn}[Rational Numbers] % def:rational-numbers
```

Use explicit IDs when you want a shorter name or the title contains LaTeX
markup.

| Short environment | Full name | Reference kind |
|---|---|---|
| `dfn` | `definition` | `def` |
| `thm` | `theorem` | `thm` |
| `prop` | `proposition` | `prop` |
| `lem` | `lemma` | `lem` |
| `cor` | `corollary` | `cor` |
| `rem` | `remark` | `rem` |
| `ex` | `example` | `ex` |
| `exc` | `exercise` | `exc` |
| `conv` | `convention` | `conv` |

For tutorial questions, use your own section-based exercise number and print
the book location at the start of the exercise body. A short local helper keeps
repeated source information convenient:

```tex
\newcommand{\abbottexercise}[1]{%
  \emph{Source:} Stephen Abbott, \emph{Understanding Analysis}, Second
  Edition, Exercise #1.\par\smallskip
}

\begin{exc}{abbott-1-2-3}
\abbottexercise{1.2.3}
Question text.
\end{exc}

% Write your answer here.
```

This prints your local number, such as `Exercise 1.1`, followed by the source
citation, while creating the reference ID `exc:abbott-1-2-3`.

Proof headings depend on whether a title is supplied:

| Syntax | Printed heading |
|---|---|
| `\begin{proof}` | *Proof.* |
| `\begin{proof}[Existence of Irrational Numbers]` | *Pf. Existence of Irrational Numbers.* |

Reference IDs are shown by default as small gray monospace text after each
heading, for example `\texttt{[def:field]}`. Control this in the preamble with
`\notesShowIDs` or `\notesHideIDs`.

Use a default document for short references:

```tex
\xrefuse{lec01}
See \xref{def:integer}.
```

To make ordinary words clickable while reusing the same reference logic:

```tex
\xref[closed operations]{def:closed-operation}
```

Or provide the location explicitly:

```tex
\xref{lec01::def:integer}
\xref{topology::lec03::def:open-set}
\xref{https://other.example::topology::lec03::def:open-set}
```

These forms mean current project, current server, and explicit server,
respectively. A local reference uses its numbered `cleveref` text; a
cross-PDF reference links to its stable semantic PDF destination. See
`REFERENCE_DESIGN_V2.md` for more detail.

## Published files

Only `latex_target/` is deployed. It contains the flat PDF set, such as
`lec01.pdf`, `tut01.pdf`, and `example.pdf`.

## Deployment

Pushing a change beneath `latex_target/` to `master` triggers the GitHub
Actions deployment workflow. Configure a repository Actions secret named
`NOTES_DEPLOY_KEY`; its value must match `NOTES_DEPLOY_KEY` in the notes
server's environment.
