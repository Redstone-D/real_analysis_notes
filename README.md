# Real Analysis

LaTeX source and published PDFs for AMA3707 Real Analysis.

## Compile

Open `lecture01.tex` or `tutorial01.tex` in VS Code with LaTeX Workshop and
press **Build LaTeX project**. Your global LaTeX Workshop configuration sends
the finished PDF directly to `latex_target/`; temporary LaTeX files stay in
the ignored `.build/` directory.

Compile `lecture01.tex` once before compiling `tutorial01.tex`, because the
tutorial imports its numbered definition references from the lecture build.

The equivalent terminal commands are:

```sh
latexmk -pdf -outdir=latex_target lecture01.tex
latexmk -pdf -outdir=latex_target tutorial01.tex
```

## Published files

Only `latex_target/` is deployed by the notes server. Keep publishable PDFs
directly beside `manifest.json`, use zero-padded filenames, and commit the
generated PDFs together with their source changes. The notes server must use
its flat-file tree reader so root-level PDFs appear on the course page.
