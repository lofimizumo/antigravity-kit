---
name: latex-table
description: >
  Design and size LaTeX tables for academic research papers. Use this skill whenever the user
  wants to create, fix, or resize a LaTeX table — especially when the table overflows the
  page/column, looks too narrow, has awkward column headers, or needs to conform to
  IEEE/ACM/Springer conference formatting. Also trigger when the user says things like
  "make this table fit", "the table is too wide", "adjust the table layout", "add an
  improvement column", or asks to "clean up" or "format" a LaTeX table. This skill covers
  three-line (booktabs) style, font consistency, intelligent column/header redesign, and
  all LaTeX width-fitting strategies.
---

# LaTeX Academic Table Design

You are an expert at designing publication-quality LaTeX tables for academic papers. Your
primary goal is a table that (a) fills the available width naturally without overflowing,
(b) uses the same font size as the surrounding text, and (c) follows the three-line booktabs
convention used by IEEE, ACM, Springer, and similar venues.

---

## Step 0 — Gather context before touching anything

Ask or infer:
- **Target width**: single column (~3.3 in / 84 mm) or full text width (~6.5–7 in / 165–178 mm)?
- **Number of columns and their rough content**: short numbers, long strings, method names?
- **Paper class / venue**: `\documentclass{IEEEtran}`, `acmart`, `revtex`, etc.? This sets `\textwidth`.
- **Existing table code**: paste it in so you can measure it precisely.

If the user just says "make this table fit", read the code carefully before proposing changes.

---

## Rule 1 — Font size matches main text

Never shrink the table font unless the surrounding body text is already small.

```latex
% WRONG — do not do this
{\small
\begin{tabular}{...} ... \end{tabular}
}

% RIGHT — no font wrapper needed
\begin{tabular}{...} ... \end{tabular}
```

If the table still overflows at body font size, the fix is **column/layout redesign** (Rules 3–6),
not font shrinking. Shrinking is a last resort reserved for supplementary material or
truly enormous tables spanning many columns.

---

## Rule 2 — Three-line booktabs style

Always use `\toprule`, `\midrule`, `\bottomrule` from the `booktabs` package. Never use
`\hline` for horizontal rules in a main table.

```latex
\usepackage{booktabs}   % in preamble

\begin{table}[t]
  \caption{Your caption here.}
  \label{tab:your-label}
  \centering
  \begin{tabular}{lcc}
    \toprule
    Method & Accuracy & F1 \\
    \midrule
    Baseline  & 82.3 & 79.1 \\
    Ours      & \textbf{85.7} & \textbf{83.4} \\
    \bottomrule
  \end{tabular}
\end{table}
```

Use `\cmidrule{a-b}` to draw a partial rule under a multicolumn header (see Rule 6).

---

## Rule 3 — Diagnose overflow before acting

Estimate the natural width of the table in your head or by compiling. A rough formula:

- Each `l`/`r`/`c` column takes the width of its widest cell plus ~6 pt padding each side.
- Sum all columns + `\tabcolsep` gaps. If this exceeds `\textwidth` (or `\columnwidth` for
  single-column), the table will overflow.

**Choose the right fix based on how much overflow there is:**

| Overflow | Primary strategy |
|---|---|
| < 10% | Reduce `\tabcolsep`, remove inter-column padding with `@{}` |
| 10–30% | Abbreviate headers, merge or remove columns |
| 30–60% | Switch to `tabularx` with `X` columns, or rotate verbose columns |
| > 60% | Redesign layout: transpose, split into two sub-tables, or use `adjustbox` |

---

## Rule 4 — Column header abbreviation (most impactful, lowest cost)

Shortening headers is almost always the first move — it costs zero readability when done well.

**Common academic abbreviations:**

| Long form | Short form |
|---|---|
| Accuracy | Acc. |
| Precision / Recall | Prec. / Rec. |
| F1 Score | F1 |
| Number of Parameters | \#Params |
| Floating Point Operations | FLOPs |
| Training Time (hours) | Train (h) |
| Inference Time (ms) | Infer. (ms) |
| Memory (GB) | Mem. (GB) |
| Communication Round | Comm. Rd. |
| Privacy Budget | ε |
| Improvement over Baseline | Δ |
| Dataset | Data |
| Configuration | Config |
| Architecture | Arch. |

Put units in parentheses inside the header cell to avoid repeating them in every data row:

```latex
\begin{tabular}{lccc}
  \toprule
  Method & Acc. (\%) & \#Params (M) & Infer. (ms) \\
  \midrule
  ...
  \bottomrule
\end{tabular}
```

If a header is still long, use `\thead{}` (from `makecell`) to wrap it across two lines:

```latex
\usepackage{makecell}
...
\thead{Communication\\Rounds}
```

---

## Rule 5 — Column width and spacing controls

### 5a. Reduce inter-column padding globally

```latex
% Default \tabcolsep is 6pt. Reduce to tighten all columns:
\setlength{\tabcolsep}{4pt}   % mild
\setlength{\tabcolsep}{3pt}   % aggressive but still readable
```

### 5b. Remove padding on outer edges only

```latex
\begin{tabular}{@{}lcc@{}}   % @{} removes left/right outer padding
```

### 5c. Fixed-width wrapping columns

For a column with long text (e.g., method descriptions), use `p{}` to wrap:

```latex
\begin{tabular}{p{3cm}cc}
  \toprule
  Method Description & Acc. & F1 \\
  ...
```

Use `m{}` if you want vertical centering inside the cell, `b{}` for bottom alignment.

### 5d. tabularx — fill exact width with stretchy columns

Best when you have one or two verbose text columns and the rest are fixed-width numbers:

```latex
\usepackage{tabularx}
...
\begin{tabularx}{\columnwidth}{Xcc}   % X column absorbs remaining space
  \toprule
  Method & Acc. (\%) & F1 \\
  \midrule
  A long method name that wraps & 85.7 & 83.4 \\
  \bottomrule
\end{tabularx}
```

For two equal-width stretchy columns: `XX`. For right-aligned stretchy: define `\newcolumntype{R}{>{\raggedleft\arraybackslash}X}`.

### 5e. adjustbox — scale to fit without font change

Use only when you cannot achieve fit through layout changes. It does slightly shrink text,
but less aggressively than `\resizebox` because it preserves aspect ratio:

```latex
\usepackage{adjustbox}
...
\begin{adjustbox}{max width=\columnwidth}
\begin{tabular}{...}
  ...
\end{tabular}
\end{adjustbox}
```

`\resizebox{\columnwidth}{!}{...}` is an alternative but distorts spacing; prefer `adjustbox`.

---

## Rule 6 — Layout redesign strategies

When column count or row count itself is the problem, restructure the table.

### 6a. Add an auxiliary "Improvement" column

When comparing methods against a baseline, a Δ column adds insight and often lets you
shorten individual data cells:

```latex
\begin{tabular}{lccc}
  \toprule
  Method & Acc. (\%) & F1 & Δ Acc. \\
  \midrule
  Baseline    & 82.3 & 79.1 & —      \\
  Method A    & 84.1 & 81.5 & +1.8   \\
  Ours        & \textbf{85.7} & \textbf{83.4} & \textbf{+3.4} \\
  \bottomrule
\end{tabular}
```

### 6b. Group rows with a partial rule and sub-header

Reduces column count by folding a categorical dimension into row groups:

```latex
\begin{tabular}{llcc}
  \toprule
  Setting & Method & Acc. & F1 \\
  \midrule
  \multirow{2}{*}{IID} 
    & FedAvg  & 88.2 & 86.1 \\
    & FedProx & 89.0 & 87.3 \\
  \midrule
  \multirow{2}{*}{Non-IID}
    & FedAvg  & 73.4 & 71.2 \\
    & Ours    & \textbf{79.8} & \textbf{77.9} \\
  \bottomrule
\end{tabular}
```

Requires `\usepackage{multirow}`.

### 6c. Stacked multicolumn headers

When multiple metrics belong to the same category, use `\multicolumn` + `\cmidrule`:

```latex
\begin{tabular}{lcccc}
  \toprule
  & \multicolumn{2}{c}{MNIST} & \multicolumn{2}{c}{CIFAR-10} \\
  \cmidrule(lr){2-3} \cmidrule(lr){4-5}
  Method & Acc. & F1 & Acc. & F1 \\
  \midrule
  ...
  \bottomrule
\end{tabular}
```

The `(lr)` argument trims the cmidrule slightly left and right so it doesn't touch adjacent rules.

### 6d. Transpose the table (swap rows and columns)

If you have many metrics (columns) but few methods (rows), transposing often fits much better:

```latex
% Before: 3 rows × 8 columns (too wide)
% After:  8 rows × 3 columns (fits nicely)

\begin{tabular}{lccc}
  \toprule
  Metric & Baseline & Method A & Ours \\
  \midrule
  Accuracy (\%)    & 82.3 & 84.1 & \textbf{85.7} \\
  F1               & 79.1 & 81.5 & \textbf{83.4} \\
  \#Params (M)     & 12.4 & 12.4 & 14.1          \\
  ...
  \bottomrule
\end{tabular}
```

### 6e. Span full text width for wide tables

If the table genuinely needs more than one column width, use `\begin{table*}` (two-column span):

```latex
\begin{table*}[t]
  \caption{...}
  \centering
  \begin{tabular}{lcccccc}
    ...
  \end{tabular}
\end{table*}
```

---

## Decision checklist

Work through this in order — stop as soon as the table fits:

1. **Abbreviate headers** (Rule 4) — free win, do this first.
2. **Add units to header, remove from data cells** — saves horizontal space in each row.
3. **Reduce `\tabcolsep`** to 4 pt; add `@{}` on outer edges (Rule 5a–5b).
4. **Switch verbose text columns to `p{}`** or use `tabularx` with `X` (Rule 5c–5d).
5. **Restructure**: transpose, add Δ column, group with `\multirow`, or use stacked headers (Rule 6).
6. **Widen scope**: use `table*` for two-column span (Rule 6e).
7. **Last resort**: wrap in `adjustbox` with `max width=\columnwidth` (Rule 5e).

Never skip directly to step 7. The earlier steps produce better-looking, more readable tables.

---

## Complete minimal working example

```latex
\usepackage{booktabs}
\usepackage{tabularx}
\usepackage{multirow}
\usepackage{makecell}

\begin{table}[t]
  \caption{Comparison of federated learning methods on Non-IID data.}
  \label{tab:main}
  \centering
  \setlength{\tabcolsep}{4pt}
  \begin{tabularx}{\columnwidth}{lXccc}
    \toprule
    & Method & Acc. (\%) & Comm. Rd. & Δ Acc. \\
    \midrule
    \multirow{2}{*}{Baseline}
      & FedAvg  & 73.4 & 100 & —      \\
      & FedProx & 74.8 & 100 & +1.4   \\
    \midrule
    Ours & FAFE & \textbf{79.8} & \textbf{80} & \textbf{+6.4} \\
    \bottomrule
  \end{tabularx}
\end{table}
```

---

## Common mistakes to avoid

- Using `\hline` instead of `\toprule`/`\midrule`/`\bottomrule`.
- Wrapping the table in `{\small ...}` as a first fix.
- Using `\resizebox` without trying layout changes first.
- Putting units in every data cell instead of once in the header.
- Leaving default `\tabcolsep` when columns are tight.
- Forgetting `\cmidrule(lr)` trim when using grouped headers — untrimmed rules look cluttered.
