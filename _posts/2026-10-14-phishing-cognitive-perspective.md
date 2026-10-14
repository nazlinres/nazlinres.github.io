---
title: 'Why Does Phishing Work? A Cognitive Perspective on User Behavior'
date: 2026-10-14
permalink: /posts/2026/10/phishing-cognitive-perspective/
tags:
  - human-centered security
  - phishing
  - user behavior
  - cybersecurity
---

Phishing remains one of the most persistent attack vectors, despite decades of technical countermeasures such as spam filtering, URL blacklisting, and email authentication. This persistence suggests that the problem cannot be reduced to a failure of detection technology alone. In this post I examine phishing from a human-centered perspective and ask a deliberately narrow question: which properties of human cognition does a phishing attack exploit, and what does this imply for how we design defenses and evaluate them?

## 1. Reframing the Problem

A common implicit assumption in security practice is that successful phishing reflects user negligence. This framing has long been challenged. Adams and Sasse (1999) argued that users are not the enemy of security: when security mechanisms conflict with users' goals, workload, or mental models, non-compliant behavior is a rational response rather than a character flaw.

Under this view, a phishing victim is better understood as a decision-maker operating under uncertainty, limited attention, and incomplete information. The research question shifts from "why are users careless?" to "under what conditions does a reasonable decision process produce an unsafe outcome?"

## 2. Dual-Process Accounts of Susceptibility

Dual-process theories of cognition (Kahneman, 2011) distinguish between fast, heuristic, largely automatic judgments and slower, deliberate, effortful reasoning. Phishing messages can be understood as artifacts engineered to keep the recipient in the first mode.

Vishwanath et al. (2011) proposed an information-processing model in which susceptibility depends on how much cognitive effort a recipient invests in evaluating a message. Their findings suggest that individual differences, such as habitual email use and attention to superficial cues, influence whether deceptive indicators are noticed at all. Attackers exploit this through:

- **Urgency and scarcity:** Time pressure suppresses deliberate evaluation.
- **Authority and familiarity:** Messages that imitate trusted senders draw on social heuristics described in the persuasion literature (Cialdini, 2001).
- **Context alignment:** A message that fits the recipient's current task is less likely to trigger suspicion.

## 3. The Visual Layer of Deception

Dhamija, Tygar, and Hearst (2006) examined why phishing websites succeed and reported that a substantial share of participants were deceived by well-constructed imitations. Their analysis indicated that users often relied on the visual appearance of a page and overlooked security indicators in the browser. This is a finding about interface and attention rather than intelligence: security cues that sit at the periphery of a user's task are structurally easy to miss.

The implication is that detection burdens placed on users should be evaluated against realistic attention budgets, not idealized ones.

## 4. Training and Its Limits

Embedded training approaches deliver instruction at the moment a user falls for a simulated attack. Kumaraguru et al. (2007) reported that such in-context training can improve users' ability to recognize phishing compared with passive instruction. However, several methodological concerns apply to this line of work in general:

1. **Retention:** Short-term improvements may not persist over long periods.
2. **Ecological validity:** Simulated campaigns may differ from real attacks in stakes and context.
3. **Measurement:** Click rate is an imperfect proxy for security behavior, since it omits reporting behavior and conflates caution with disengagement.

Training is therefore best regarded as one component of a layered defense, not a substitute for technical controls.

## 5. Implications for Research

Taken together, these perspectives suggest that human-centered security research should treat the user and the technical system as a single socio-technical unit. Several questions remain open:

- How can phishing susceptibility be measured in ways that reflect real-world behavior without compromising ethical constraints?
- Can detection systems and user interfaces be designed so that the decisions that matter are presented at moments of high attention?
- How should explainability methods, which I discussed in my previous post, be adapted so that the explanations given to non-expert users genuinely support their decisions rather than merely increasing confidence?

The last question connects human-centered security with explainable AI, and it is one of the directions I intend to pursue in my research.

## References

- Adams, A., & Sasse, M. A. (1999). Users Are Not the Enemy. *Communications of the ACM*, 42(12).
- Cialdini, R. B. (2001). *Influence: Science and Practice* (4th ed.). Allyn & Bacon.
- Dhamija, R., Tygar, J. D., & Hearst, M. (2006). Why Phishing Works. *CHI*.
- Kahneman, D. (2011). *Thinking, Fast and Slow*. Farrar, Straus and Giroux.
- Kumaraguru, P., et al. (2007). Protecting People from Phishing: The Design and Evaluation of an Embedded Training Email System. *CHI*.
- Vishwanath, A., Herath, T., Chen, R., Wang, J., & Rao, H. R. (2011). Why Do People Get Phished? Testing Individual Differences in Phishing Vulnerability Within an Integrated, Information Processing Model. *Decision Support Systems*, 51(3).
