Perfect — I’ll turn that into a **clean, professional GitHub README** that is:

* readable (not a paper dump)
* honest about negative results
* research-grade
* suitable for reviewers, recruiters, or collaborators

Below is a **ready-to-paste `README.md`**.
You can use it **as-is**.

---

# 🧠 Inducing Compositional Reasoning via Architectural Bottlenecks in Tiny Transformers

This repository presents a systematic experimental study on whether **architectural constraints**, rather than model scale, can induce **compositional reasoning** in small Transformer models.

We evaluate a family of **5–10M parameter Transformers** on the **SCAN compositional generalization benchmark**, focusing on **mean performance and stability across random seeds**, rather than best-case accuracy.

> **Core finding:**
> Reasoning is an *emergent but fragile* property of constrained architectures. Without explicit inductive bias, both classical and quantum bottlenecks remain unstable.

---

## 📌 Motivation

Despite impressive scaling results, large language models often fail at **systematic compositional generalization**—the ability to correctly interpret novel combinations of known primitives. Prior work suggests this failure is not primarily due to insufficient capacity, but rather due to **shortcut learning enabled by unconstrained token-to-token attention**.

This project tests the hypothesis that **architectural bottlenecks**, even in *small* models, can induce reasoning behavior by forcing information to pass through a narrow latent state.

---

## 🧪 Task: SCAN Compositional Generalization

All experiments are conducted on the **SCAN** dataset, a synthetic command-to-action benchmark designed to test compositional reasoning.

* **Input:** Natural language commands
  e.g. `jump around right twice`
* **Output:** Action sequences
  e.g. `JUMP RTURN JUMP RTURN`
* **Split:** Length generalization
  Models are trained on short compositions and evaluated on longer, unseen compositions.
* **Metric:** Exact-match sequence accuracy

SCAN is intentionally adversarial: a single incorrect compositional decision results in total sequence failure.

---

## 🧱 Baseline Model

The baseline is a standard encoder–decoder Transformer with:

* 2 encoder layers
* 2 decoder layers
* Hidden dimension: 128
* ~5–10M parameters
* Full token-to-token attention
* Cross-entropy loss with teacher forcing

### Baseline Performance

| Model                | Mean Accuracy | Variance |
| -------------------- | ------------- | -------- |
| Baseline Transformer | ~0.55         | Low      |

Despite high training accuracy, the baseline consistently fails to generalize compositionally.

---

## 🧠 Reasoning Bottleneck Architecture

To restrict shortcut learning, we introduce a **slot-based reasoning bottleneck** between the encoder and decoder.

```
Input Tokens
     ↓
Transformer Encoder
     ↓
Slot-Based Reasoning Bottleneck
     ↓
Transformer Decoder
```

Instead of allowing the decoder to attend to all encoder tokens, **all information is compressed into a fixed number of latent slots**, which the decoder attends to exclusively.

The **number of slots** is the primary control variable.

---

## ⚙️ Experimental Setup

* Bottleneck sizes evaluated: **2, 4, 6, 8, 12, 16**
* **5 random seeds** per configuration
* Metrics reported:

  * Mean test accuracy
  * Standard deviation
* All other hyperparameters held constant
* LayerNorm applied to bottleneck outputs
* No auxiliary losses or curriculum learning

---

## 📊 Results

### Quantitative Summary (SCAN Length Split)

| Bottleneck Slots         | Mean Accuracy | Std Dev |
| ------------------------ | ------------- | ------- |
| Baseline (no bottleneck) | ~0.55         | Low     |
| 2                        | ~0.49         | —       |
| 4                        | ~0.57         | ~0.07   |
| 6                        | ~0.59         | ~0.15   |
| 8                        | ~0.67         | ~0.17   |
| 12                       | ~0.71         | ~0.14   |
| 16                       | ~0.45–0.57    | —       |

### Key Observations

* **Architectural bottlenecks improve mean compositional accuracy** without increasing parameter count.
* **Reasoning emerges only in a narrow capacity regime**.

  * Too few slots → information loss.
  * Too many slots → shortcut learning returns.
* **Emergent reasoning is highly unstable**.

  * Some seeds achieve very high accuracy (~0.88).
  * Others collapse almost completely.

---

## 🔬 Analysis: Reasoning as an Emergent Phase

The results resemble a **phase transition**:

* **Low capacity:** Underfitting, no reasoning
* **Intermediate capacity:** Sporadic reasoning emergence
* **High capacity:** Shortcut learning dominates again

This suggests that attention-based bottlenecks can *discover* symbolic structure, but lack an inductive bias to **prefer or stabilize** it. Reasoning is therefore **not guaranteed**, even when induced.

---

## ⚛️ Hybrid Quantum Bottleneck Experiments

Motivated by the instability of classical bottlenecks, we explore **hybrid quantum–classical reasoning bottlenecks**.

### Design

* Encoder representations are pooled into a compact latent vector
* Projected into a low-dimensional quantum input space
* Processed by a parameterized quantum feature map (angle encoding + entanglement)
* Measurement outcomes are projected back into latent slots
* Decoder attends only to these quantum-processed slots

All comparisons hold model size, optimizer, data splits, and evaluation metrics constant.

### Results

| Model                           | Mean Accuracy | Std Dev |
| ------------------------------- | ------------- | ------- |
| Baseline Transformer            | ~0.55         | Low     |
| Classical Bottleneck (12 slots) | ~0.71         | ~0.14   |
| Quantum Bottleneck (4 slots)    | ~0.44         | ~0.21   |

### Interpretation

Naive quantum bottlenecks **fail to improve accuracy or stability** and often increase variance. Quantum interference amplifies both signal and noise without encoding task-relevant symbolic structure.

This is a **negative but informative result**.

---

## 🧠 Discussion

* Classical bottlenecks fail due to **linear mixing and shortcut learning**
* Quantum bottlenecks fail due to **over-compression and unstructured interference**
* Both failures arise from **missing inductive bias**

Quantum components should not be treated as drop-in replacements for attention. They must be aligned with **structured, symbolic representations**, not raw pooled features.

---

## 🧾 Reproducibility

* Fixed architectures
* Identical training settings
* Multiple random seeds
* No pretrained models
* No prompt engineering
* Negative results reported explicitly

---

## 🔮 Future Directions

* Structured quantum mixing over symbolic latent variables
* Hybrid neuro-symbolic bottlenecks with quantum kernels
* Quantum-assisted *stability* mechanisms rather than compression
* Sparse, per-sample quantum reasoning instead of batch-level aggregation

---

## 📌 One-Line Takeaway

> **Reasoning is an emergent property of architectural constraint—and without explicit inductive bias, both classical and quantum bottlenecks remain unstable.**

