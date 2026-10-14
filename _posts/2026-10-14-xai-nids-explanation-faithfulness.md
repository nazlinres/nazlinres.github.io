---
title: 'Explainability in Network Intrusion Detection: When Can an Explanation Count as Evidence?'
date: 2026-10-14
permalink: /posts/2026/10/xai-nids-explanation-faithfulness/
tags:
  - explainable AI
  - network intrusion detection
  - cybersecurity
  - SHAP
---

Machine learning-based network intrusion detection systems (NIDS) are routinely reported with high accuracy figures. Yet the question of why a model classifies a given flow as an "attack" often remains unanswered. Explainable AI (XAI) methods, developed to address this question, promise to give the security analyst a rationale for the model's decision. In this post I focus on a narrower question: to what extent is an explanation faithful to the model's actual behavior, and can an explanation carry evidential value without that faithfulness?

## 1. Problem Definition

In the NIDS context, an explanation serves two distinct functions. The first is *operational*: helping an analyst evaluate an alert quickly. The second is *epistemic*: examining which patterns the model relies on in order to detect erroneous or undesirable learning. For the first function, a persuasive explanation may seem sufficient. For the second, the explanation must accurately reflect the model's internal logic.

These two expectations are separate, because an explanation that appears reasonable to a human need not be faithful to the model. In the literature, this distinction is framed as the difference between *plausibility* and *faithfulness*.

## 2. Assumptions of Post-hoc Methods

Post-hoc methods such as LIME (Ribeiro et al., 2016) and SHAP (Lundberg and Lee, 2017) approximate the local behavior of a complex model with a simpler structure. This approach rests on three implicit assumptions:

- **Local linearity:** The model's decision surface can be well represented by a simple model in the neighborhood of the instance being examined.
- **Feature independence:** Perturbing features produces realistic data. In network traffic, however, attributes such as flow duration, packet count, and byte count are structurally dependent, so perturbed samples may fall outside the real data distribution.
- **Stability:** Similar inputs yield similar explanations.

Each of these assumptions is contestable for network traffic data. Slack et al. (2020) showed that LIME and SHAP can be deceived by an adversarial model that distinguishes the out-of-distribution samples the explainer queries. This finding indicates that explanations can be manipulated independently of the model's true logic, which is especially significant in adversarial settings such as security.

## 3. Dataset-Induced Illusions

The faithfulness of explanations is a function not only of the method but also of the patterns the model has learned. A model may have learned artifacts of the data collection process rather than attack behavior. Examinations of the widely used CIC-IDS2017 dataset (Engelen et al., 2021) have revealed labeling errors and problems arising from flow construction.

In such a case, even if the XAI method *correctly* reports that the model relies on these artifacts, an analyst may interpret the result as a finding about attack semantics. A distinction is therefore needed between an explanation being correct and an explanation being informative.

## 4. Evaluating Explanations

Objectively measuring explanation quality is an open research problem. Approaches proposed in the literature fall roughly into three groups:

1. **Deletion/insertion tests:** Measuring how the model output changes when the features an explanation deems important are removed.
2. **Stability analysis:** Examining the consistency of explanations across similar instances.
3. **Domain expert evaluation:** Testing the meaningfulness and usefulness of explanations to analysts.

None of these is sufficient alone. Deletion tests can generate out-of-distribution samples, stability does not guarantee faithfulness, and expert evaluation measures plausibility, not faithfulness.

## 5. Conclusion and Open Questions

The main conclusion is that an explanation should be treated as a *hypothesis* about model behavior and not used as *evidence* without verification. The following questions remain open for NIDS research:

- How can perturbation strategies be designed that respect the structural dependencies of network flow data?
- How should the robustness of explanations against adversarial manipulation be tested?
- To what extent do explanations remain reliable in situations outside the training distribution, such as zero-day attacks?

These questions also form the starting points of my ongoing systematic review.

## References

- Ribeiro, M. T., Singh, S., & Guestrin, C. (2016). "Why Should I Trust You?": Explaining the Predictions of Any Classifier. *KDD*.
- Lundberg, S. M., & Lee, S.-I. (2017). A Unified Approach to Interpreting Model Predictions. *NeurIPS*.
- Slack, D., Hilgard, S., Jia, E., Singh, S., & Lakkaraju, H. (2020). Fooling LIME and SHAP: Adversarial Attacks on Post hoc Explanation Methods. *AIES*.
- Engelen, G., Rimmer, V., & Joosen, W. (2021). Troubleshooting an Intrusion Detection Dataset: the CICIDS2017 Case Study. *IEEE Security and Privacy Workshops*.
