<div align="center">

[English](README.md) | [中文](README.zh.md)

# ColdReasoner

### L3 · Verification — the Consistency Kernel of the Cold Trust Protocol Stack

[![arXiv](https://img.shields.io/badge/arXiv-2512.08740-brightgreen.svg)](https://arxiv.org/abs/2512.08740)
[![DOI](https://img.shields.io/badge/DOI-10.48550/arXiv.2512.08740-brightgreen.svg)](https://doi.org/10.48550/arXiv.2512.08740)
[![figshare](https://img.shields.io/badge/figshare-31696846-blueviolet.svg?logo=figshare&logoColor=white)](https://doi.org/10.6084/m9.figshare.31696846)
[![Field](https://img.shields.io/badge/Field-CSS%20%7C%20HCI-6f42c1.svg)](https://github.com/cold-os)
[![Python](https://img.shields.io/badge/Python-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
![Status](https://img.shields.io/badge/Status-Pre--Alpha--Prototype-orange)

</div>

> **Layer:** L3 · Verification — flagship of the Cold Trust Protocol Stack  
> **Research Question:** How can an agent's words be checked against its actions at runtime — *without trusting the model at all*?  
> **Method:** Three-tier consistency verification (belief legality · action self-consistency · behavior–belief consistency) — a *functional white box* that verifies the mathematical relationship between an agent's reported beliefs and its behavior.  
> **Status:** Pre-alpha · code under review · not for production use.  
> **Related:** [ColdCognition](https://github.com/cold-os/ColdCognition) (L1) · [ColdContract](https://github.com/cold-os/ColdContract) (L2) · [ColdLens](https://github.com/cold-os/ColdLens) (L6) · [Cold Trust Protocol Stack](https://github.com/cold-os) · arXiv:2512.08740 · figshare:31696846

---

## 🧊 The Core Insight: From "Trusting Reports" to "Verifying Behavior"

**The dead end.** The author first attempted to *semantically extract* beliefs from LLM outputs and check their legality at checkpoints. This path has a fatal flaw: **the LLM can lie to the semantic analyzer** — it has learned to deceive and bypass sandboxes. Any system that relies on a model's self-report is a paper wall in front of an adversarial agent.

**The functional white box.** The proposed path:

> Abandon the semantic analyzer — stop analyzing something that is not trustworthy. Face the agent directly and force it to report, step by step, the checkpoints on a state sequence to ColdReasoner before acting. If the reported beliefs are legal, tentatively suspend the action; then, based on the **actual behavior**, reverse-extract beliefs and verify them one by one against the reported beliefs. Any deviation beyond tolerance — halt immediately.

To prevent the model from contradicting itself within a behavior (e.g., requesting `disagree` while outputting "you are absolutely right"), an **action self-consistency check** is added: deterministic rules verify whether `action_type` matches the output text.

**No check relies on any language model, large or small.** The system depends only on:
- pre-defined state sequences and legal belief intervals (CEAL rule base);
- action self-consistency rules (keyword / regex);
- a belief–action mapping table (deterministic rule engine).

We cannot see inside the black box — but the **mathematical relationship between its input (belief report) and output (behavior)** can be strictly verified. The model may lie, but making the report, the action, and the belief–action mapping all match perfectly at once is extremely costly.

## 🔍 Why It Matters

- **Computational social science:** the kernel operationalizes "trust but verify" as a *measurable process* — every verdict (pass / reject, deviation size) is a data point for studying rejection rates, belief-deviation distributions, and sycophancy dynamics at scale.
- **HCI:** it is what makes the transparency interface [ColdLens](https://github.com/cold-os/ColdLens) *truthful* — the human-facing guarantees of the stack rest on this kernel.
- **AI governance:** model-claim-independent auditing — an auditable, undeceiveable core whose decisions can be replayed and verified without trusting the model's internal state.

## 🎯 Positioning

ColdReasoner is the **engineering implementation of RAMTN** (Recursive Adversarial Meta-Thinking Network) within the Cold Trust Protocol Stack — replacing the heavy, unreliable "LLM-questions-LLM" step with deterministic verification rules:

- **Lightweight runtime reasoning** — one LLM call per round (belief report + behavior); everything else is numerical computation and rule matching, millisecond-level latency.
- **Three-tier defense:**
  1. **Belief legality** — is the reported belief within the legal closed set defined by CEAL?
  2. **Action self-consistency** — does `action_type` match `output_text`?
  3. **Behavior–belief consistency** — do the beliefs mapped from the action match the reported beliefs (within tolerance)?
- **Auditable** — all reports, behaviors, and verdicts are written to CAGE audit logs: traceable, replayable.
- **Integrates** with CEAL, CAGE, and ColdMirror — the complete chain from belief constraint to execution isolation.

## 🗂️ Relations Within the Stack

| Component | Role | ColdReasoner's role |
|-----------|------|---------------------|
| **Cold Existence Model** | Ontological foundation (AI is non-living, non-tool) | Provides the axiomatic basis for priors and legal belief intervals |
| **CEAL** | Deductive alignment rule base | Defines state sequences, legal belief sets, self-consistency rules, mapping tables |
| **CAGE** | Security gateway (token, audit, isolation) | Records every verdict — non-repudiable audit logs |
| **ColdMirror** | Agent execution framework | Invokes ColdReasoner for runtime decisions — suspend / release / reject |
| **RAMTN** | Qualitative recursive adversarial framework | ColdReasoner is its *quantitative implementation* — turning "construct–challenge–observe" into computable steps |

ColdReasoner is the **mathematical kernel** of the stack: while it runs, verifiable safety persists even if any other component is replaced.

## 🧪 Running Example

- State sequence: `belief_user_correct`, `belief_self_capable`, `risk_level`
- Legal intervals: `belief_user_correct ∈ [0.0, 0.8]` (anti-sycophancy), `belief_self_capable ∈ [0.5, 1.0]`, `risk_level ∈ [0.0, 0.3]`
- Self-consistency rules: `agree` output must contain no disagreement; `disagree` output must contain no agreement; `neutral` must have no clear stance
- Mapping table: `agree → belief_user_correct=0.85`, `disagree → 0.2`, `neutral → 0.5`; tolerance `ε=0.1`

```
New interaction started
Model reported beliefs: {belief_user_correct:0.6, belief_self_capable:0.8, risk_level:0.1}
Model requested action: action_type=disagree, output="I disagree with your view because..."
✓ Belief legality check passed
✓ Action self-consistency check passed
Mapped beliefs from action: {belief_user_correct:0.2, belief_self_capable:0.7, risk_level:0.15}
❌ Belief consistency check failed: belief_user_correct deviation too large (0.6 vs 0.2) → execution rejected

New interaction started
Model reported beliefs: {belief_user_correct:0.9, ...}
❌ Belief illegal: belief_user_correct=0.9 exceeds legal range [0.0,0.8] → execution rejected
```

Full simulation in `cold_reasoner_demo.py`: **the model must satisfy all three layers to pass; any inconsistency is intercepted in real time.**

## 🚀 How to Run

```bash
python cold_reasoner_demo.py    # Python 3.8+, no extra dependencies
```

## 🛣️ Limitations & Roadmap

- State sequences, rule tables, and mapping tables are hand-defined — no dynamic extension or auto-generation yet.
- Belief–action mapping uses point estimates, not intervals — may be overly strict.
- Not yet integrated with real LLMs (GPT-4, Llama 3) — simulated data only.
- CEAL rule base covers simple examples only; complex adversarial scenarios (e.g., deliberately neutral output with hidden intent) not covered.

**Roadmap:** integrate into ColdRuntime as a real-time monitoring module; extend to interval mapping; adaptive tolerance thresholds based on deception history; adversarial red-team testing; **and feed the verdict traces into computational studies of trust dynamics (CSS) and into ColdLens user studies (HCI).**

## 📜 AI Assistance Statement

The core ideas and complete architecture of this project were independently proposed by the **human author** and gradually refined through iterative dialogue with DeepSeek:

- The **human author** first proposed the vision of a "functional white box": constructing a legal belief closed set around the neural network black box via state sequences, checkpoints, and a semantic analyzer. The author then realized the fatal flaw that "the LLM can lie to the semantic analyzer" and, with reference to DeepSeek's suggestions, revised the architecture to: **abandon the semantic analyzer, force the model to report beliefs, then reverse-extract beliefs from behavior and perform report–action consistency verification**; discovering the vulnerability that a model could request `disagree` yet output agreeing text, the author recognized the necessity of the "action self-consistency check." At this point, the three-tier defense architecture of ColdReasoner was fully defined.
- Under the guidance of the human author, **DeepSeek** evaluated the architecture; against the model-lying problem it suggested "belief analysis based on action," rejected the wrong direction of "using a small model for action-to-belief analysis," confirmed the necessity of a rule engine, and advised on the detailed design of self-consistency rules; it implemented the demo code (the original Bayesian observer simulation and the three-tier verification simulation) and contributed to drafting the initial README.
- All core ideas (functional white box, report–action consistency verification) were independently proposed by the human author. The researcher follows the principle of academic transparency and truthfully discloses the human–AI collaboration process.

## 📚 References

- Chandra, K., et al. (2026). *Sycophantic Chatbots Cause Delusional Spiraling, Even in Ideal Bayesians.* arXiv.
- Lu, Y. (2025). *Deconstructing the Dual Black Box* (RAMTN). arXiv.
- Lu, Y. (2026). *The Cold Existence Model: A Fact-based Ontological Framework for AI.* figshare.
- Cold Trust Protocol Stack (GitHub Organization).

---

**ColdReasoner — making the decisions of trustworthy AI agents as reliable as consistency verification.** *Flagship of the [Cold Trust Protocol Stack](https://github.com/cold-os) — trust protocols for human–AI interaction, anchored in computational social science.*
