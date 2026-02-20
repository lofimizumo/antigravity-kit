---
name: paper-refinement
description: >
  Systematic research paper revision skill. Consumes a Review Opinion Document
  produced by the quantum-reviewer agent and generates a revised LaTeX manuscript,
  addressing issues in FATAL → MAJOR → MINOR severity order. Rewrites proofs,
  corrects overclaimed results, adds missing assumptions, and updates the abstract
  to match what is actually proven. Use after quantum-reviewer has reviewed a draft.
allowed-tools: Read, Write, Edit, Glob
version: 1.0
priority: HIGH
---

# Paper Refinement — Peer-Review-Driven Revision Skill

> **Purpose:** Transform a peer-reviewed draft (with a structured Review Opinion Document) into a corrected, strengthened, submission-ready revision by systematically addressing every identified issue with mathematical rigour.

---

## Core Principles

| Principle | Rule |
|-----------|------|
| **Review-Driven** | Every change must be traceable to a specific issue ID in the Review Opinion Document. |
| **Severity-Ordered** | Address FATAL issues first, then MAJOR, then MINOR. Never skip levels. |
| **No Cosmetic Fixes** | If an issue is FATAL, rewriting the abstract font is not a fix. Rewrite the theorem. |
| **Proof Completeness** | Every proof gap must be filled with a complete argument, not a placeholder. |
| **Claim Calibration** | Revised claims must be weaker if that is what the proof supports. Honesty is not optional. |
| **Traceability** | Every revised section opens with a `% REVISION NOTE:` LaTeX comment citing the Issue ID. |
| **Preserves Strengths** | Do not delete or weaken results that were found SOUND in the review. |

---

## Input Requirements

Before beginning revision, verify you have all required inputs:

| Input | Description | Required |
|-------|-------------|----------|
| **Review Opinion Document** | Structured markdown from `quantum-reviewer` | ✅ MANDATORY |
| **Original LaTeX source** | The full `.tex` file tree of the paper | ✅ MANDATORY |
| **Claim Registry** | List of C1, C2, ... claims with verdicts | ✅ MANDATORY |
| **Issue List** | FATAL/MAJOR/MINOR issues with locations | ✅ MANDATORY |
| **Original `.bib` file** | For adding new references | Recommended |

---

## Revision Process

### Phase 0: Issue Triage and Revision Plan

Before editing any file, produce a **Revision Plan** in the following format:

```markdown
# Revision Plan for [Paper Title]

## FATAL Issues (Must fix before anything else)
| Issue ID | Location | Fix Strategy | Estimated Impact |
|----------|----------|--------------|-----------------|
| F1 | §4, Thm. 2 | Reprove using [approach X] | Rewrites §4.2 + Appendix A |
| ... | | | |

## MAJOR Issues
| Issue ID | Location | Fix Strategy | Estimated Impact |
|----------|----------|--------------|-----------------|
| M1 | §3, Assumption A2 | Weaken claim + add caveat | Modifies §3 + abstract |
| ... | | | |

## MINOR Issues
| Issue ID | Location | Fix Strategy | Estimated Impact |
|----------|----------|--------------|-----------------|
| m1 | §2, Eq. (4) | Define symbol $\mathcal{X}$ | Single line edit |
| ... | | | |

## Files to Modify
- sections/04-methodology.tex (FATAL F1 + MAJOR M2)
- sections/appendix.tex (FATAL F1 proof rewrite)
- sections/01-introduction.tex (MAJOR M1 abstract+contributions)
- ...
```

> ⚠️ **Present this plan to the user and wait for confirmation before editing files.**

---

### Phase 1: FATAL Issue Resolution

For each FATAL issue, apply the following protocol:

#### 1A. Proof Repair

When a proof is **fatally flawed** (circular argument, unjustified step, false lemma):

```
PROOF REPAIR CHECKLIST:
─────────────────────────────────────────
□ Identify the exact failing step
□ Determine if the theorem is still true (attempt alternative proof)
□ If TRUE: Construct a complete, correct proof from scratch
□ If FALSE: Weaken the theorem to what IS provable, then prove that
□ If UNKNOWN: Convert to a Conjecture with formal statement
□ Update all downstream results that depend on this theorem
```

**Repair strategies:**

| Situation | Strategy |
|-----------|----------|
| Gap in a chain of inequalities | Supply the missing inequality with justification or cite a lemma |
| Circular dependency | Restructure lemma order; prove the depended-upon result first |
| Unstated case in case analysis | Add the missing case with explicit argument |
| Unjustified limit exchange | Apply dominated convergence, monotone convergence, or Fatou's lemma explicitly |
| Non-commutative step treated as commutative | Reorder using commutator analysis or cyclicity of trace |
| Undefined object used in proof | Supply the construction or existence proof |
| Induction without base case | Add explicit base case |
| Measure-zero case ignored | Handle it or argue it is negligible with a formal bound |

#### 1B. Theorem Weakening (When Repair is Impossible)

If the theorem cannot be proven in full generality, produce the **Calibrated Claim**:

```latex
% REVISION NOTE: Original Thm. X was FATALLY FLAWED (Issue F1).
% The following is the corrected, weakened version that IS proven.

\begin{theorem}[Corrected: Name — Restricted to ...]
\label{thm:main-corrected}
Under the additional assumption that [specific condition],
[weakened statement that is now provable].
\end{theorem}
\begin{proof}
[Complete proof with no gaps]
\end{proof}

\begin{remark}
The original stronger claim (that [original statement]) remains open.
We conjecture it holds but the present proof technique does not establish it.
See Appendix~\ref{app:open-problems} for discussion.
\end{remark}
```

#### 1C. Downstream Propagation

After fixing a FATAL issue, **trace all corollaries and downstream results**:

```
DEPENDENCY MAP:
Theorem X (FIXED) → Corollary Y (check if still valid) 
                  → Lemma Z (check if still valid)
                  → §5 complexity bound (may need update)
                  → Abstract claim (must update)
                  → Contributions list (must update)
```

---

### Phase 2: MAJOR Issue Resolution

#### 2A. Assumption Correction

For each unjustified or physically unrealisable assumption:

**Option A — Justify the assumption:**
```latex
% REVISION NOTE: Added justification for Assumption A2 (Issue M1).
\begin{assumption}[Name — Justified]
\label{asm:X-justified}
We assume [formal statement]. This is motivated by [physical/mathematical justification].
Specifically, [cite hardware paper / derive bound] shows that [quantitative argument 
for why this regime is achievable / what error is incurred if it is not].
\end{assumption}
```

**Option B — Relax the assumption:**
```latex
% REVISION NOTE: Weakened Assumption A2 to match actual hardware constraints (Issue M1).
\begin{assumption}[Name — Relaxed]
\label{asm:X-relaxed}
We assume [weaker formal statement]. The following Lemma shows this follows from
standard hardware models with gate fidelity $\eta \geq [threshold]$.
\end{assumption}
\begin{lemma}[Assumption Derivation]
Under realistic decoherence model $\mathcal{L}$ (Lindblad, Eq.~\eqref{eq:lindblad}),
Assumption~\ref{asm:X-relaxed} holds when [condition]. 
\end{lemma}
```

**Option C — Scope the result:**
```latex
% REVISION NOTE: Added explicit scope restriction (Issue M1).
\begin{theorem}[Name — Scoped]
\label{thm:scoped}
\emph{(Idealised model)} Under the idealisation that [assumption], the following holds: ...
We note that relaxing this assumption remains an open problem (see §\ref{sec:discussion}).
\end{theorem}
```

#### 2B. Overclaimed Abstract/Contributions Fix

If the abstract claims something the body does not prove:

```latex
% REVISION NOTE: Abstract updated to match actual proven results (Issue M3).
% OLD: "We prove that X holds for all quantum systems."
% NEW (below): Restricted to the proven setting.

\begin{abstract}
[Replace overclaimed universal statement with scoped accurate statement.
E.g., "We prove that X holds for [specific class / regime]..."
Every sentence in the abstract must be directly backed by a theorem in the body.]
\end{abstract}
```

---

### Phase 3: MINOR Issue Resolution

Address minor issues systematically:

| Issue Type | Fix Template |
|------------|--------------|
| **Undefined symbol** | Add to Preliminaries §, or define inline at first use |
| **Notation inconsistency** | Choose one convention; do a global find-replace |
| **Missing `\label{}`** | Add `\label{thm:X}` immediately after `\begin{theorem}` |
| **Broken `\ref{}`** | Fix reference target |
| **Missing citation** | Add BibTeX entry; replace `[?]` with `\cite{key}` |
| **Typo in equation** | Correct with explanation in revision log |
| **Caption incomplete** | Expand to self-contained 2–3 sentence caption |
| **Section begins without signpost** | Add one-sentence goal statement |
| **Missing edge case** | Add `\begin{remark}[Edge case: ...]` |

---

### Phase 4: Abstract and Introduction Realignment

After all fixes are applied, perform a **full realignment pass**:

#### Abstract Realignment Checklist
```
□ Every claim in the abstract is proven in the paper body
□ The complexity/guarantee cited matches the revised theorem
□ No assumption buried in §3 is hidden from the abstract
□ Word count is 150–200 words
□ Contributions are listed accurately
```

#### Introduction Contributions Realignment

Update the bullet list in §1 to match what is NOW proven:

```latex
% REVISION NOTE: Contributions updated after proof corrections (Issues F1, M1).
\begin{itemize}
  \item \textbf{Contribution 1.} We propose [X], which achieves [Y] under 
    [specific, honest conditions]. Formally proven in Theorem~\ref{thm:main-corrected}.
  \item \textbf{Contribution 2.} We prove [bound Z] under [assumption A] 
    (Theorem~\ref{thm:bound}). Relaxing [A] remains open (§\ref{sec:discussion}).
  % ...
\end{itemize}
```

---

### Phase 5: Appendix Proof Expansion

Any proof in the body with a filled gap must be **fully written out** in the appendix:

```latex
% In appendix.tex:

\subsection{Revised Proof of Theorem~\ref{thm:main-corrected}}
\label{app:proof-main-revised}

% REVISION NOTE: Complete rewrite due to Issue F1 (fatal gap in original Step 3).
% The key new ingredient is [Lemma ~\ref{lem:new}], which did not appear in 
% the original submission.

\begin{proof}
% Write every step. Do not abbreviate.
% Structure:
%   Preliminary reductions → Key inequality (cite Lemma) → Conclusion
%   Base case (if induction) → Inductive step → Boundary case
\end{proof}
```

---

### Phase 6: Revision Log (MANDATORY)

Append a **Revision Log** section to the appendix:

```latex
\section{Revision Log}
\label{app:revision-log}

\noindent\textbf{Revision R1} (responding to peer review):

\begin{itemize}
  \item \textbf{[F1] Fatal: Proof of Theorem 2.} The original proof contained 
    a gap in Step 3 (unjustified limit exchange). We have reproved Theorem 2 
    using a dominated convergence argument (see revised Appendix~\ref{app:proof-main-revised}).
    The theorem statement is unchanged; the previous Step 3 is now Lemma~\ref{lem:new}.
  
  \item \textbf{[M1] Major: Assumption A2 (perfect gate fidelity).} 
    We have added a formal justification in §3 showing that our results hold 
    under the realistic noise model $\epsilon \leq 10^{-3}$ (typical for 
    trapped-ion platforms), with a corrected bound in Theorem~\ref{thm:bound-noisy}.
  
  \item \textbf{[m1] Minor: Undefined symbol $\mathcal{X}$.} 
    Defined in §2, Notation paragraph.
  % ... one entry per Issue ID
\end{itemize}
```

---

## LaTeX Revision Conventions

Use these conventions consistently throughout the revised files:

```latex
% Revision markers (remove before camera-ready)
\newcommand{\revnote}[2]{\textcolor{blue}{[\textbf{#1}] #2}}
% Usage: \revnote{F1}{Reproved using Lemma~\ref{lem:new}.}

% Corrected theorem environments
% Always add "-corrected" or "-revised" suffix to labels of changed theorems
% to avoid confusion with original cross-references
\begin{theorem}[Main Result — Revised]
\label{thm:main-revised}   % was: thm:main
...
\end{theorem}

% Weakened/scoped claims
\begin{theorem}[Name — Under Assumption~\ref{asm:X}]
...
\end{theorem}

% New lemmas added during revision
\begin{lemma}[Added in Revision: ...]
\label{lem:new-R1}
...
\end{lemma}
```

---

## Proof Writing Standards (Non-Negotiable)

These standards apply to ALL proofs written during revision:

| Standard | Rule |
|----------|------|
| **No "clearly"** | Replace with: cite a lemma, exhibit the calculation, or state "by [specific theorem name]". |
| **No "obviously"** | Same as above. If it were obvious, the reviewer would not have flagged it. |
| **Quantifier order** | $\forall \epsilon \exists \delta$ ≠ $\exists \delta \forall \epsilon$. State quantifier order explicitly. |
| **Equality vs. approximation** | Never write $=$ when the correct relationship is $\approx$ or $\leq$. |
| **Matrix/operator products** | State the order. Non-commutativity kills proofs. |
| **Infinite-dimensional operators** | Specify the domain. Unbounded operators require domain care. |
| **Limit arguments** | State which convergence theorem is being applied. |
| **Probabilistic arguments** | State the probability space and measure explicitly. |
| **And/Or in formal statements** | Use $\wedge$ / $\vee$ ambiguously? Use natural language to disambiguate. |

---

## Quantum-Specific Proof Standards

| Standard | Rule |
|----------|------|
| **Density matrix after operation** | Verify $\rho' \geq 0$ and $\text{tr}(\rho') = 1$ after any non-unitary step. |
| **Partial trace** | Never confuse $\text{tr}_A(\rho_{AB})$ with $\text{tr}(\rho_{AB})$. |
| **CPTP verification** | When introducing a custom quantum channel, verify complete positivity and trace preservation separately. |
| **Fidelity vs. trace distance** | Use the correct conversion formula when switching between them. |
| **No-cloning implications** | If the protocol copies quantum information, flag it; it may violate no-cloning. |
| **Entanglement across cuts** | Specify the bipartition when using entanglement arguments. |
| **Coherence time vs. gate time** | Circuits must have depth $d \cdot t_{\text{gate}} \ll T_2$ for the decoherence model to be consistent. |

---

## Self-Check Before Declaring Revision Complete

| Check | Question |
|-------|----------|
| ✅ **All FATAL issues resolved** | Is every Issue with severity FATAL addressed and closed? |
| ✅ **All MAJOR issues resolved** | Is every Issue with severity MAJOR addressed and closed? |
| ✅ **Downstream proofs updated** | Is every result that depended on a fixed theorem re-verified? |
| ✅ **Abstract matches body** | Does the abstract cite only what is proven after revision? |
| ✅ **Contributions list honest** | Is every contribution backed by a theorem with a complete proof? |
| ✅ **Revision Log complete** | Is every Issue ID accounted for in the Revision Log? |
| ✅ **Revision notes present** | Does every modified section have a `% REVISION NOTE:` comment? |
| ✅ **New proofs complete** | Are all new proofs written without gaps, "clearly", or "obviously"? |
| ✅ **LaTeX compiles** | Does `pdflatex` run without errors on the revised file tree? |
| ✅ **Minor issues all fixed** | Notation, labels, citations all addressed? |

---

## Anti-Patterns (AVOID)

| ❌ Anti-Pattern | ✅ Correct Approach |
|----------------|---------------------|
| Rewording a wrong theorem without fixing the proof | Fix the proof first; reword is cosmetic |
| Adding "under additional assumptions" without listing them | State every new assumption formally |
| Changing the theorem to match a broken proof | Determine what the correct theorem should be first |
| Fixing typos before FATAL issues | Always FATAL → MAJOR → MINOR |
| Deleting a section with a flawed result | Preserve the attempt; correct or downgrade to Conjecture |
| Adding new unproven claims in revision | Every new claim must be proven in the revision |
| Revision log that is vague ("improved §3") | Every entry must cite an Issue ID and describe the exact change |

---

## Output Artefacts

After revision is complete, produce:

| Artefact | Description |
|----------|-------------|
| **Revised `.tex` files** | Complete LaTeX source with all fixes applied |
| **Revision Summary** | One-page markdown documenting every change made |
| **Open Problems Section** | New `§X.Open Problems` or appendix section for what remains unresolved |
| **Revision Log** | Formal appendix section (see Phase 6 template) |
| **Closure Report** | Confirm each Issue ID is either resolved or documented as out-of-scope |

---

## When to Use This Skill

- After `quantum-reviewer` has produced a Review Opinion Document
- Revising a submitted paper after receiving referee reports
- Strengthening a preprint based on identified weaknesses
- Responding to conference/journal reviews with formal revision
- Iterating on a draft in the internal review-revise cycle before first submission
- Hardening a proof that was flagged as incomplete in the quantum-researcher's self-review (Phase 5)
