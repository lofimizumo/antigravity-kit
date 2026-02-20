---
name: paper-writing
description: Research paper authoring skill. Produces publication-ready academic papers in ACM double-column LaTeX (acmart), ~12 pages, with rigorous appendix proofs, structured sections (Introduction, Related Work, Methodology, Empirical Results (placeholder), Discussion, Conclusion), placeholder figures with captions, and TODO experiment lists. Use when converting a research idea or derivation into a complete draft paper.
allowed-tools: Read, Write, Edit, Glob
version: 1.0
priority: HIGH
---

# Paper Writing — Research Publication Skill

> **Purpose:** Transform research ideas, derivations, and theoretical results into a complete, submission-ready academic paper draft in ACM `acmart` double-column LaTeX format.

---

## Core Principles

| Principle | Rule |
|-----------|------|
| **Completeness** | A 12-page draft is the minimum viable output. Do not stop at 5 pages. |
| **Rigour First** | Every claim is either proven in-text or deferred to the Appendix with a proof. |
| **Honest Placeholders** | Experiments are not fabricated. Leave `\placeholder{}` blocks with TODO lists. |
| **Caption-Driven Figures** | Every figure has a complete, informative caption even before the figure exists. |
| **Reader-First Structure** | Each section begins with a one-sentence statement of what the reader will learn. |
| **LaTeX Precision** | Use correct ACM commands, math environments, and bibliography conventions. |

---

## Paper Structure (Default, Flexible)

The following is the default structure for a **~12-page ACM paper**. Deviate only when the research requires it. Always explain deviations.

```
1. Introduction             (~1.5 pages)
2. Related Work             (~1.5 pages)
3. Preliminaries            (~0.75 pages, optional if notation is standard)
4. Methodology / Framework  (~3 pages)
5. Theoretical Analysis     (~1 page, if applicable, else merge into §4)
6. Empirical Results        (~1.5 pages, PLACEHOLDER)
7. Discussion               (~0.75 pages)
8. Conclusion               (~0.5 pages)
--- References              (~0.5–1 page)
--- Appendix                (~2+ pages, for full proofs)
```

> **Length target:** 10–12 pages for main body (excluding references and appendix).

---

## Section-by-Section Writing Guide

### §1 Introduction (~1.5 pages)

**Goal:** Convince the reader the problem matters, current methods fail, and your approach succeeds.

**Mandatory paragraph structure:**
1. **Hook** (~3 sentences): Broad context. Why does this area matter to humanity/science?
2. **Problem Statement** (~5 sentences): The specific gap. What is formally unsolved?
3. **Limitations of Prior Art** (~5 sentences): Why do existing methods fall short? Be precise.
4. **Our Approach** (~4 sentences): High-level idea. What key insight drives the solution?
5. **Contributions** bullet list (4–6 items):
   - `\item We propose [X], a [type] that achieves [property].`
   - `\item We prove [Theorem N] showing [formal guarantee].`
   - `\item We evaluate [X] against [baselines] on [metrics].`
6. **Paper Organization** (1 short paragraph): `§2 reviews related work. §3 presents ...`

---

### §2 Related Work (~1.5 pages)

**Goal:** Position the paper w.r.t. existing literature honestly and precisely.

**Structure:**
- Organize into **thematic subsections** (2–4 subsections), not a flat list.
- For each cited group of works, state: what they do, where they succeed, where they fail.
- End with a **Comparison Table** (optional but recommended):

```latex
\begin{table}[h]
\caption{Comparison of related approaches on key properties.}
\begin{tabular}{lcccc}
\toprule
Method & Property A & Property B & Complexity & Proven? \\
\midrule
[Prior 1] & Yes & No & $O(n^2)$ & \checkmark \\
[Prior 2] & Partial & Yes & $O(n \log n)$ & $\times$ \\
\textbf{Ours} & \textbf{Yes} & \textbf{Yes} & $\mathbf{O(n)}$ & \checkmark \\
\bottomrule
\end{tabular}
\end{table}
```

---

### §3 Preliminaries (~0.75 pages, optional)

**Goal:** Define all notation and foundational concepts used in the paper.

Use a consistent notation block:

```latex
\paragraph{Notation.}
Let $\mathcal{H}$ denote a finite-dimensional Hilbert space ...
We denote by $\rho \in \mathcal{D}(\mathcal{H})$ a density operator ...
```

Include only what is **not standard knowledge** for the target venue (e.g., POVM basics for a quantum networking venue, but not for a quantum information journal).

---

### §4 Methodology / Framework (~3 pages)

**Goal:** The intellectual core of the paper. This is where novelty lives.

**Structure:**
1. **Overview subsection**: Describe the high-level architecture or approach.
   - Include a system diagram (placeholder figure) with complete caption.
2. **Key Definitions**: Formal definitions numbered `\begin{definition}...\end{definition}`.
3. **Main Algorithm or Protocol**: Use `\begin{algorithm}...\end{algorithm}`.
4. **Key Theorems**: State theorems formally. Proof may be inline (short) or `[Proof in Appendix~\ref{app:proof}]`.
5. **Design Rationale**: Briefly justify each major design choice.

**LaTeX Environments to Use:**

```latex
\begin{definition}[Name]
...
\end{definition}

\begin{theorem}[Name]
\label{thm:main}
...
\end{theorem}
\begin{proof}
... or \textit{Full proof in Appendix~\ref{app:proof-main}.}
\end{proof}

\begin{lemma}
\label{lem:key}
...
\end{lemma}

\begin{corollary}
\label{cor:bound}
...
\end{corollary}

\begin{proposition}
...
\end{proposition}
```

---

### §5 Theoretical Analysis (~1 page)

**Goal:** Derive complexity, tightness, or correctness guarantees.

Include:
- **Time / Space / Round Complexity** with formal proof sketch.
- **Correctness Theorem** citing Theorem from §4.
- **Bounds**: Lower bound argument if applicable (`\begin{theorem}[Lower Bound]`).
- **Asymptotic regime**: State behaviour as $n \to \infty$ or $\epsilon \to 0$.

> Merge into §4 if the paper is methodology-light and theory-heavy.

---

### §6 Empirical Results (~1.5 pages — **PLACEHOLDER**)

**Goal:** Reserve space and provide a complete TODO for future experiments.

**⚠️ RULE: Do NOT fabricate results. The section MUST be a structured placeholder.**

Format:

```latex
\section{Empirical Results}
\label{sec:eval}

% ============================================================
% PLACEHOLDER — Experiments not yet conducted
% ============================================================
% TODO: Complete the following experiments before submission.
%
% Experiment 1: [Name]
%   - Setup: [brief description]
%   - Baselines: [method A, method B]
%   - Metrics: [fidelity, throughput, latency, ...]
%   - Expected outcome: [hypothesis]
%
% Experiment 2: ...
% ============================================================

We evaluate \sysname{} against state-of-the-art baselines across
[N] experimental settings. Full results will be reported upon
completion of the experimental campaign.

\subsection{Experimental Setup}
\paragraph{Simulation environment.}
[TODO: Describe simulator, hardware model, noise model.]

\paragraph{Baselines.}
[TODO: List baselines and citations.]

\paragraph{Metrics.}
[TODO: Define metrics formally.]

\subsection{Main Results}

\begin{figure}[h]
\centering
% \includegraphics[width=\linewidth]{figures/main_result.pdf}
[PLACEHOLDER FIGURE]
\caption{[TODO: Caption describing the expected plot, axes labels, and what the figure will demonstrate, e.g., throughput vs. number of nodes for each baseline.]}
\label{fig:main}
\end{figure}

[TODO: Table of quantitative results here.]

\subsection{Ablation Study}
[TODO: Study the contribution of each major component.]
```

---

### §7 Discussion (~0.75 pages)

**Goal:** Contextualise results, explore implications, and acknowledge limitations honestly.

**Structure:**
1. **Interpretation**: What do the results mean beyond the numbers?
2. **Broader Implications**: Impact on related subfields.
3. **Limitations**: State clearly. Do not hide them in footnotes.
   - What assumptions were made that limit generality?
   - What edge cases are not handled?
4. **Future Work**: 3–5 concrete open problems or extensions.

---

### §8 Conclusion (~0.5 pages)

**Goal:** Crisp, declarative summary. No new content.

**Structure:**
1. One-sentence problem statement.
2. One-sentence summary of approach.
3. One-sentence formal guarantee achieved.
4. One-sentence broader impact.
5. CTA: "We release code and artefacts at [URL]." (or as appropriate)

---

## Appendix: Full Proofs (MANDATORY)

Every theorem/lemma whose proof is deferred in the main body **must** appear here in complete form.

```latex
\appendix

\section{Proofs of Main Results}
\label{app:proofs}

\subsection{Proof of Theorem~\ref{thm:main}}
\label{app:proof-main}

\begin{proof}
% Full proof here. Do NOT abbreviate.
% Structure: preliminary steps → key inequality → conclusion.
\end{proof}

\subsection{Proof of Lemma~\ref{lem:key}}
\label{app:proof-lemma}

\begin{proof}
...
\end{proof}
```

**Proof Writing Standards:**

| Rule | Requirement |
|------|-------------|
| **Every step justified** | No "clearly" or "obviously" without citation or calculation |
| **Case analysis** | All cases covered, edge cases treated separately |
| **Induction** | Base case + inductive step both explicit |
| **Contradiction** | State assumption, derive ⊥, conclude |
| **Constructive** | If claiming existence, exhibit the object explicitly |

---

## Figure Conventions

Every figure needs a caption that can stand alone without the main text.

**Caption Template:**
```
\caption{\textbf{[Figure Title].} [2–3 sentences describing:
(1) what the figure shows,
(2) what the axes or components represent,
(3) the key takeaway or trend.]}
```

**Placeholder Figure Block:**
```latex
\begin{figure}[htbp]
\centering
\fbox{\rule{0pt}{3cm}\rule{\linewidth}{0pt}}  % placeholder box
\caption{\textbf{[System Architecture / Result Overview / etc.]}
[Full caption text as described above.]}
\label{fig:placeholder}
\end{figure}
```

---

## LaTeX File Structure (acmart)

```latex
\documentclass[sigconf,review,anonymous]{acmart}  % or sigplan, etc.

% --- Packages ---
\usepackage{amsmath,amssymb,amsthm}
\usepackage{algorithm,algpseudocode}
\usepackage{booktabs}
\usepackage{graphicx}
\usepackage{hyperref}
\usepackage{cleveref}   % \cref{} for smart references
\usepackage{todonotes}  % \todo{} for placeholder tracking

% --- Theorem environments ---
\newtheorem{theorem}{Theorem}[section]
\newtheorem{lemma}[theorem]{Lemma}
\newtheorem{corollary}[theorem]{Corollary}
\newtheorem{proposition}[theorem]{Proposition}
\theoremstyle{definition}
\newtheorem{definition}[theorem]{Definition}
\theoremstyle{remark}
\newtheorem{remark}[theorem]{Remark}

% --- Custom macros ---
\newcommand{\sysname}[1]{\textsc{#1}}
\newcommand{\E}{\mathbb{E}}
\newcommand{\prob}{\mathbb{P}}
\newcommand{\ket}[1]{|#1\rangle}
\newcommand{\bra}[1]{\langle #1|}
\newcommand{\braket}[2]{\langle #1 | #2 \rangle}
\newcommand{\ketbra}[2]{|#1\rangle\langle #2|}
\newcommand{\norm}[1]{\left\| #1 \right\|}
\newcommand{\tr}{\mathrm{tr}}
\newcommand{\F}{\mathcal{F}}

\begin{document}

\title{[Paper Title]}
\subtitle{[Subtitle, optional]}

\author{...}
\email{...}
\affiliation{...}

\begin{abstract}
[150–200 words. Problem, gap, method, result, impact.]
\end{abstract}

\keywords{[keyword1, keyword2, keyword3, quantum computing]}

\maketitle

\input{sections/01-introduction}
\input{sections/02-related-work}
\input{sections/03-preliminaries}
\input{sections/04-methodology}
\input{sections/05-analysis}
\input{sections/06-experiments}
\input{sections/07-discussion}
\input{sections/08-conclusion}

\bibliographystyle{ACM-Reference-Format}
\bibliography{references}

\appendix
\input{sections/appendix}

\end{document}
```

---

## Bibliography Rules

- Use BibTeX with `ACM-Reference-Format` style.
- Every citation must have: author, title, venue, year.
- Prefer arXiv preprints only when no published version exists.
- Use `\cite{key}` not `[1]` in text.
- Add citations at claim-time, not at section-end.

---

## Anti-Patterns (AVOID)

| ❌ Anti-Pattern | ✅ Correct Approach |
|----------------|---------------------|
| Fabricated experimental results | Leave as placeholder with TODO list |
| "Proof" that says "it is easy to see" | Show every non-trivial step |
| Figure with no caption context | Full self-contained caption always |
| Related work as flat citation dump | Thematic groups + comparison table |
| Theorem without label | Always `\label{thm:X}` |
| Section that starts without signpost | First sentence states the section's goal |
| 5-page draft called "complete" | Minimum 10-page main body |
| Contributions that are vague | Each contribution is a falsifiable claim |

---

## Self-Check Before Declaring Draft Complete

| Check | Question |
|-------|----------|
| ✅ **Length** | Is main body ≥ 10 pages (≤ 12)? |
| ✅ **Theorems labeled** | Every theorem/lemma has `\label{}` and `\ref{}` cross-references? |
| ✅ **Proofs in appendix** | Every deferred proof is written in full in the appendix? |
| ✅ **Figures have captions** | No figure without a complete, standalone caption? |
| ✅ **Experiments are placeholders** | No fabricated numbers or fake graphs? |
| ✅ **Bibliography complete** | Every `\cite{}` has a corresponding BibTeX entry? |
| ✅ **Abstract complete** | 150–200 words, covers all four elements? |
| ✅ **Compiles cleanly** | `pdflatex` runs without errors (warnings acceptable)? |

---

## When to Use This Skill

- Generating a full paper draft from a theoretical result produced by `quantum-researcher`
- Structuring an existing set of ideas into a submission-ready format
- Writing up a new quantum algorithm, protocol, or proof
- Producing a paper skeleton with correct LaTeX structure before filling content
- Iterating on a draft by adding proofs, fixing section balance, or expanding methodology
