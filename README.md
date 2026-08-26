# 🚀 Modality Fault Lines: Structural Corruptions Reveal Fragile Omni-Modal Reasoning

**Paper:** Findings of EMNLP 2026 | **Dataset:** [Hugging Face (coming soon)](https://huggingface.co/datasets/KZL96/ModalityFaultLines-SCEval)

SCEval is a human-verified benchmark for studying the robustness of omni-modal models when text, visual input, and audio remain available but their internal evidence structure becomes unreliable. It focuses on structural corruption rather than missing-modality ablation: the task, answer options, and modality channels are held fixed while controlled corruptions are applied to text, vision, audio, or their combinations.

Rather than asking whether a model can answer with one modality removed, SCEval asks a more practical question: does its answer remain stable when a channel is still available but its words, visual details, or temporal signal can no longer be trusted in the usual way? The benchmark is designed to expose this gap between clean omni-modal accuracy and robustness to degraded cross-modal evidence.

<p align="center">
  <img src="assets/sceval-case.png" alt="A clean tri-modal example and its structurally corrupted counterpart in SCEval" width="520">
</p>
<p align="center"><em>Example from SCEval. The three modality channels remain present, while their internal structure is perturbed.</em></p>

## 🧭 Overview

Clean text--vision--audio evaluation cannot determine whether a model uses cross-modal evidence robustly or succeeds through cues that are sufficient only when all inputs are intact. SCEval addresses this gap through the notion of a **modality fault line**: a condition in which model behavior becomes unstable even though the relevant modality remains present and interpretable.

Each SCEval condition is paired with its clean counterpart at the example level. A corruption operator modifies the internal structure of one or more modality channels without replacing the question, answer space, or sample identity. This distinguishes SCEval from standard missing-modality settings, in which removing a channel changes the input regime itself.

SCEval supports analysis at two complementary levels. Single-modality conditions identify which kind of evidence a model is sensitive to, while bimodal and trimodal conditions show whether simultaneous corruption produces a simple accumulation of errors or a qualitatively different failure pattern.

## 📦 Dataset Composition

| Property | Description |
| --- | --- |
| Verified base set | 273 tri-modal examples |
| Source benchmarks | Social-IQ, OmniBench, and VALOR |
| Modalities | Text, visual input, and audio |
| Question format | Multiple-choice questions with a reference answer |
| Corruption settings | Single-modality, bimodal, and trimodal conditions |
| Severity levels | 10, 30, 50, and 70 |
| Comparison design | Paired clean-versus-corrupted evaluation on the same base example |

The verified base set contains 100 examples from Social-IQ, 77 from OmniBench, and 96 from VALOR. Source examples are used under the terms of their respective benchmark releases.

Every base example links a clean tri-modal multiple-choice question to matched structural-corruption conditions. The paired design keeps the underlying item fixed, allowing changes in model behavior to be attributed to the altered evidence structure rather than to a different question or answer space.

## 🧩 Corruption Design

SCEval contains fourteen structural corruption operators. They perturb the organization, completeness, ordering, or fidelity of evidence while retaining the modality channel itself.

| Modality | Operators |
| --- | --- |
| Text (4) | `typo_ocr`, `drop_words`, `word_shuffle`, `sentence_break` |
| Vision (7) | `noise`, `occlusion`, `low_resolution`, `motion_blur`, `defocus_blur`, `overexposure`, `brightness` |
| Audio (3) | `remove`, `mute`, `distortion` |

Every operator is evaluated at four severity levels: 10, 30, 50, and 70. SCEval includes single-modality conditions, paired corruptions across two modalities, and joint corruptions across text, vision, and audio. For stochastic operators, multiple variants are generated at the same severity to separate the effect of corruption strength from a particular random realization.

## ✅ Human Verification

SCEval uses human verification at both the base-example and corrupted-variant levels.

- **Base-example verification.** Candidate examples are screened for question validity, a defensible reference answer, and tri-modal answerability from the clean input.
- **Corrupted-input verification.** Audio and visual variants are checked for perceptual interpretability. Variants that become unusable media artifacts are excluded from the corresponding accuracy aggregate.
- **Answer-preservation audit.** For audited headline conditions, annotators answer the corrupted input before seeing the reference answer and then assess whether the original answer remains defensible. This separates **gold-preserved** variants from **stress-only** variants, where the input remains interpretable but no longer supports the original reference answer.

This procedure ensures that a reported corruption effect is not conflated with an invalid question, missing media, or an answer that has ceased to be supported by the corrupted input.

## 📏 What SCEval Measures

SCEval measures how model behavior changes when evidence remains available but becomes structurally unreliable. Its paired design supports clean-to-corrupted comparisons within the same underlying example and makes it possible to examine whether corruption in one modality interacts with corruption in another.

The benchmark is intended as a behavioral diagnostic. It identifies empirical robustness patterns under controlled structural corruption; it does not by itself establish the internal causal mechanism or representation-level source of a model's behavior.

## 📈 Key Findings

In the paper, SCEval is evaluated on 15 omni-modal systems: seven proprietary/API models and eight open or open-API models. The results show that clean accuracy alone is not a reliable indicator of robustness under structurally degraded evidence.

| Evaluation setting | Result | Interpretation |
| --- | --- | --- |
| Severity-70 single-modality conditions | Five operators---`drop_words`, `word_shuffle`, `noise`, `remove`, and `mute`---each yield a panel-mean drop of at least 7 percentage points. | Fragility concentrates in corruption types that disrupt word identity, dense visual evidence, or audio segments. |
| Visual `noise` | Mean accuracy falls by 11.3 points for both the proprietary/API group and the open-model group. | Higher clean accuracy does not by itself imply greater robustness to this structural perturbation. |
| Joint text--vision corruption | Mild text corruption (`drop_words` at 30) combined with heavy visual noise (70) is the steepest joint failure in the representative-model analysis. | The most damaging condition is asymmetric; making every channel more severely corrupted is not necessarily worse. |
| Bimodal and trimodal conditions | The degradation pattern is structured and model-dependent, rather than a simple function of how many modalities are corrupted. | Text--vision damage forms the most stable shared modality fault line. |

## 🔗 Availability

The SCEval dataset will be released on the Hugging Face Hub:

<https://huggingface.co/datasets/KZL96/ModalityFaultLines-SCEval>

## ⚠️ Limitations

SCEval contains 273 English tri-modal multiple-choice examples from three source benchmarks. Its operators model controlled structural degradation rather than semantic substitutions or adversarially optimized attacks. The benchmark is designed as a focused diagnostic setting, not as an exhaustive account of omni-modal robustness across languages, tasks, or real-world corruption processes.

## 📚 Citation
