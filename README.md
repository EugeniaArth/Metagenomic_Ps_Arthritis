# When diseases mask each other: disentangling overlapping microbiome signatures

Different diseases can produce surprisingly similar shifts in the microbiome, making their microbial signatures difficult to distinguish. Crohn's disease and colorectal cancer share dysbiotic features — but their biology is different. The goal is to build a model that recognises **disease-specific microbial signatures even when another disease is present.**

This project starts with a focused case: **Psoriasis** and **Psoriatic Arthritis**.

---

## Dataset: TettAJ_2016

| | Count |
|---|:-----:|
| **Psoriasis only** | 21 subjects (69 samples) |
| **Psoriasis + Arthritis** (comorbid) | 7 subjects (19 samples) |
| **Healthy controls** | ~47 subjects (OhJ_2014 + HMP_2012 skin) |
| Body site | Skin (ear + elbow, left + right) |
| Sequencing | Illumina HiSeq, MetaPhlAn3 profiles |
| Source | [curatedMetagenomicData](https://waldronlab.io/curatedMetagenomicData/) |

> ⚠️ **Subject-level**, not sample-level. Each subject contributes multiple skin
> sites — these are averaged per subject before modelling to avoid
> pseudo-replication.

---

## Question

Does arthritis comorbidity **mask or alter** the psoriasis skin microbiome
signature? Can we decompose the signal into:

1. **Shared component** — microbial changes common to psoriasis with or
   without arthritis
2. **Arthritis-specific component** — changes only seen in comorbid patients
3. **Interaction component** — non-additive effects when both diseases
   coexist

---

## Approach

```
Psoriasis-only (21) ────────→ microbiome profile
                                │
Psoriasis+Arthritis (7)  ──────→ microbiome profile
                                │
Healthy controls (47)   ──────→ baseline
```

1. **CLR transform** the abundance matrix
2. Fit a **multi-label / multi-output model** that can predict both
   conditions independently
3. Decompose signatures using **sparse CCA** or **NMF** into shared,
   disease-specific, and interaction components
4. Validate: can we learn arthritis-specific markers from the comorbid
   group and recognise them in psoriasis-only patients?

---

## Why this pair?

- **No pure-arthritis group exists** in TettAJ_2016 — so a 4-way design
  (healthy / A / B / both) is impossible. But a 2-group comparison
  (Psoriasis vs Psoriasis+Arthritis) can still test whether arthritis
  comorbidity shifts the microbial community.

- The 7 comorbid subjects are the **same patients** as the psoriasis-only
  group, differing only by arthritis status — an ideal setup to isolate
  the arthritis signal.

---

## Beyond this pair

Once the method works on psoriasis↔arthritis, the framework generalises to:

- AD ↔ AD+AllergicRhinitis (ChngKR_2016, n=5 vs 8)
- Cross-study: train on psoriasis, predict on atopic dermatitis
- Positive controls: CDI↔CD (n=127 comorbid), IBD↔BD (n=660 comorbid)
- Synthetic mixtures of real profiles at known ratios

---

## Repository structure

```
.
├── Project.Rmd          # Main notebook: data inventory, comorbidity search,
│                        # subject averaging, feasibility
├── Project.html         # Knitted output
├── Project.md           # Quick-reference summary
└── data/                # (future) processed abundance tables, models
```

---

## References

- Tett AJ et al. (2016) *Nat Commun.* PMID: [28649415](https://pubmed.ncbi.nlm.nih.gov/28649415)
- Chng KR et al. (2016) *Microbiome.* PMID: [27562258](https://pubmed.ncbi.nlm.nih.gov/27562258)
- curatedMetagenomicData: [waldronlab.io](https://waldronlab.io/curatedMetagenomicData/)