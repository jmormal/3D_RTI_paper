# 3D-SCPTOP paper

The manuscript is `Renderer.tex`. It holds the preamble and the
bibliography, and pulls in:

- `main.tex`, the body, which inputs the `sec_*`, `tab_*`, `Alg_*` and
  `Tikz*` files;
- `necessarypackages.tex` and `tikzimports.tex`;
- `references.bib` and `method.bib`.

Build it with the system TeX Live:

    PATH=/usr/bin:$PATH latexmk -pdf Renderer.tex

The PATH prefix is there because a TinyTeX in `~/.local/bin` shadows the
system install and lacks packages.

- `Renderer.pdf` is the paper.
- `Renderer-diff.pdf` marks the changes since `100c362`, the version on
  branch `method-update-3d` before the final 3D results.
- Text in purple (`\Pending`) waits on the Julia re-run of the re-pack
  table.
- `CHANGES-final3d.md` lists every change and the source of each number.
- `REVIEW.md` and `REVIEW-final3d.md` are the referee passes.

`old/` holds the earlier manuscript (its own `Renderer.tex`, 59 pages) and
the stand-alone notes. It builds on its own from inside `old/`.

Until 2026-11-04 the current manuscript lived in `rewrite/` as `paper.tex`.
The file and line references in `CHANGES-final3d.md`, `REVIEW*.md` and
`inflight-sites.md` that say `rewrite/` mean the same files, now at the root.
