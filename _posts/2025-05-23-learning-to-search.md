---
layout: distill
title: "Learning to Search: Amortized Reasoning in LLMs with GFlowNets"
description: >
   Many tasks we care about—chain‑of‑thought (CoT) reasoning, story infilling, tool‑augmented arithmetic—are instances of intractable posterior inference inside a pretrained LLM. Common fine‑tuning strategies such as supervised learning, PPO‑style RLHF, or DPO chase one high‑reward trajectory and ignore the rest, forfeiting diversity and reliability.
   This post explains how Generative Flow Networks (GFlowNets) turn LLM fine‑tuning into train‑time search: the model is taught to sample complete reasoning paths with probability proportional to their joint likelihood, thereby amortizing Bayesian inference. We weave together intuition, a toy demo, and results from Hu et al. (ICLR 2024) to show why GFlowNets can be a drop‑in alternative that is (i) more data‑efficient, (ii) more robust to reward miss‑specification, and (iii) naturally enables model‑averaged predictions.

tags: distill formatting
giscus_comments: true
date: 2025-05-23
featured: true

authors:
  - name: annonymous

bibliography: 2025-05-23-learning-to-search.bib

toc:
  - name: Problem & Motivation
  - name: Motivational Example
  - name: GFlowNets 101 — A Primer
  - name: Core Algorithm Box
  - name: Empirical Sections
  - name: Comparison Table
  - name: What We Learned & Limitations
  - name: Fine-tuning with Chain-of-thought Reasoning
  - name: Chain-of-thought Reasoning Without Prompting

---

## Problem & Motivation
> How can we say that inference is closely related to reasoning?

- Problem statement: LLMs expose only left‑to‑right conditionals; anything else—infilling, CoT, lexical constraints—requires sampling from an intractable posterior.
- Limitation of current practice: Beam‑search heuristics distort probabilities; PPO/DPO collapse to a few modes and are brittle to reward hacking.
- Key equation:
$p(Y \mid X)=\sum_Z p(Y \mid X Z) p(Z \mid X)$
with posterior $p(Z \mid X, Y) \propto p(X Z Y)$ being the object we actually need.
- Visual: latent‑variable plate $(X \rightarrow Z \rightarrow Y)$.

## Motivational Example
> Can an LLM Random-Sample 0-100?

- GPT‑J fails uniformity; PPO fixes validity but stays skewed.
- GFlowNet fine‑tuning matches the target distribution (Figure 2).
- Take‑away: distribution‑matching vs reward‑maximising.

## GFlowNets 101 — A Primer
- Sequential sampler that learns $q(Z) \propto R(Z)$.  
- Subtrajectory‑Balance objective, LoRA adaptation for efficiency.
- Contrast with VI, MCMC, Policy‑Gradient RL.
- Visual: autoregressive sampling tree + flow conservation.


## Core Algorithm Box
- Pseudo‑code: initialize policy with base LLM → SubTB loss → replay buffer → converge to posterior.
- Side bar: why train‑time search ≠ inference‑time beam search.

## Empirical Sections
### 5.1 Sentence Continuation (likelihood–diversity trade‑off)
Each subsection: setup ➜ numbers ➜ one‑line “lesson”.
### 5.2 Story Infilling (ROCStories)
Each subsection: setup ➜ numbers ➜ one‑line “lesson”.
### 5.3 Subjectivity Classification (10–50 labels, EM‑style)
Each subsection: setup ➜ numbers ➜ one‑line “lesson”.
### 5.4 Tool‑Augmented Arithmetic (OOD length‑generalisation)
Each subsection: setup ➜ numbers ➜ one‑line “lesson”.

## Comparison Table: GFlowNet vs MLE / PPO / DPO / STaR
Axes: diversity, sample‑efficiency, reward robustness, compute cost.

## What We Learned & Limitations
Strengths: posterior coverage, Bayesian model averaging, low‑data wins.
Limitations: needs a decent reward LM; doesn’t add new knowledge; exploration in very long sequences; experiments $\le$ 6B params.

## Fine-tuning with Chain-of-thought Reasoning
Refer to <d-cite key="lobo2025impactfinetuningchainofthoughtreasoning"></d-cite>. Chain-of-thought fine-tuning can degrade the performance.
> Prior research suggests that, despite generating reasoning steps before the final
answer, LLMs may produce reasoning that don’t align with their internal decision-making processes, as these operate in different representational spaces (Tanneru et al., 2024; Agarwal et al., 2024; Rafailov et al., 2023).

## Chain-of-thought Reasoning Without Prompting
Refer to <d-cite key="wang2024chainofthoughtreasoningprompting"></d-cite>. We can use chan-of-thought without prompting, but the performance is limited due to the combinatorial explosion of possible way of thinking.

---

