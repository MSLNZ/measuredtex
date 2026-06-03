# measuredtex

LaTeX package for semantic annotation of measurement models.

`measuredtex` provides lightweight semantic markup for measurement-model documents. It lets authors write ordinary LaTeX equations and term definitions while also emitting simple sidecar files that can be consumed by downstream tooling.

The package is intended for documents where mathematical models, model equations, term definitions, and textual concepts need to be made available in a structured form without replacing normal LaTeX authoring.

## Features

- Wraps standard `amsmath` display environments for measurement-model equations.
- Records model metadata such as name, role, source, and label.
- Writes model and mathematical-term records to a sidecar file named `<jobname>.models`.
- Provides commands for defining mathematical terms associated with labelled equations.
- Provides list and table-oriented forms for rendering term definitions.
- Provides `\mdconcept{<id>}{<text>}` for collecting textual concepts.
- Writes textual concept records to a sidecar file named `<jobname>.terms`.
- Provides small formatting macros for semantically decorated mathematical terms.

## Requirements

`measuredtex` requires LaTeX2e and the `amsmath` package.

## Basic use

Load the package in the usual way:

```tex
\usepackage{measuredtex}
```

### Model equations

Use `mdeq` for a single model equation:

```tex
\begin{mdeq}[name=Ohm's law, role=model, label=eq:ohm]
  V = IR
\end{mdeq}
```

Use `mdalign` for aligned model equations:

```tex
\begin{mdalign}[name=Linear model, role=model, label=eq:linear]
  y &= ax + b \\
  u_y^2 &= x^2 u_a^2 + a^2 u_x^2 + u_b^2
\end{mdalign}
```

The optional metadata argument accepts the keys:

- `name`
- `role`
- `source`
- `label`

If `label` is supplied, it is also applied as the LaTeX label for the displayed equation.

## Mathematical term definitions

Term definitions are associated with a labelled model equation.

To record a term definition without typesetting visible output:

```tex
\mddefn{eq:ohm}{V}{The voltage across the resistor}
```

To record and typeset an inline definition:

```tex
\mddefntext{eq:ohm}{V}{:}{the voltage across the resistor}
```

To record and typeset a table-row fragment:

```tex
\begin{tabular}{ll}
  \mddefntab{eq:ohm}{V}{The voltage across the resistor} \\
  \mddefntab{eq:ohm}{I}{The current through the resistor} \\
  \mddefntab{eq:ohm}{R}{The resistance} \\
\end{tabular}
```

To record and typeset a description list:

```tex
\begin{mddescription}
  \mditem{eq:ohm}{V}{The voltage across the resistor}
  \mditem{eq:ohm}{I}{The current through the resistor}
  \mditem{eq:ohm}{R}{The resistance}
\end{mddescription}
```

If the referenced equation label has not been defined, the package issues a warning.

## Textual concepts

Use `\mdconcept{<id>}{<text>}` to mark ordinary textual concepts:

```tex
The \mdconcept{reference-oscillator}{reference oscillator}
provides the timing reference.
```

The command typesets the text normally and records the concept ID and text for output to `<jobname>.terms`.

If the same concept ID is used again with the same text, it is reused silently. If the same concept ID is used with different text, the package issues an inconsistency warning.

## Semantic term formatting

The package also provides small formatting macros for decorated mathematical terms:

```tex
\nspace{SI}
\assoc{input}
\tv{offset}
\qual{cal}
\qual{cal}(pre)
\mdterm[SI]{V}[rms](input)
```

These commands are formatting macros only. They do not write sidecar records.

## Sidecar files

### `<jobname>.models`

Model environments and mathematical term definitions write records to `<jobname>.models`.

A model record has the form:

```txt
===== MODEL =====
name: <name>
role: <role>
source: <source>
label: <label>
equation: <equation-body>
```

Only supplied metadata fields are written. The `equation` field is always written for model environments.

A mathematical-term record has the form:

```txt
ref: <equation-label>
term: <term>
definition: <definition>
```

### `<jobname>.terms`

Textual concepts collected with `\mdconcept` are written to `<jobname>.terms` at the end of the document:

```txt
<id>: <text>
```

Concepts are written once, in first-use order.

## Building the package documentation

The source is maintained as a documented LaTeX source file (`.dtx`). A typical documentation build is:

```sh
pdflatex measuredtex.dtx
makeindex -s gind.ist -o measuredtex.ind measuredtex.idx
makeindex -s gglo.ist -o measuredtex.gls measuredtex.glo
pdflatex measuredtex.dtx
pdflatex measuredtex.dtx
```

Depending on your local TeX setup, `latexmk` may also be used.

## Status

This package is at an early stage. Interfaces and sidecar formats may evolve.

## Licence

MIT Licence.
