---
title: "Syscall Analysis for Detecting Container Escape Attacks"
collection: talks
type: "Video"
permalink: /talks/2026-05-20-syscall-container-escape
venue: "YouTube"
date: 2026-05-20
location: "Online"
---

Containers offer a virtualization model in which the operating system kernel is shared and isolation relies on kernel-level abstractions. While this model provides lightness and efficiency, it also introduces a structural security problem: the isolation boundary is an assumption enforced by software. This video examines container escape attacks, which constitute a violation of that boundary, through system call (syscall) sequences within a theoretical and methodological framework.

**Questions and Concepts Addressed:**

- **Theoretical basis of isolation:** What security assumptions underlie namespaces, cgroups, and the shared-kernel model, and under what conditions do these assumptions fail?
- **Attack taxonomy:** How can container escape attacks be categorized according to the mechanisms they exploit?
- **System calls as a level of observation:** Because syscall sequences represent a process's interaction with the kernel, why do they offer a suitable observation space for behavioral anomaly detection?
- **Modeling approaches:** Under what assumptions, and with what expressive power, do n-gram, LSTM, and Transformer-based methods model the sequential dependencies in syscall sequences?
- **Limitations:** How do false positive rates, computational cost, and scalability constrain the practical validity of existing approaches?
- **Open problems:** Which directions do real-time detection and lightweight models point to for future research?

**References:**

- Manco, F., et al. (2017). "Machine Learning Based Anomaly Detection for Container Security." *IEEE Security & Privacy*.
- Canzanese, R., et al. (2015). "System Call-Based Detection of Malicious Processes." *IEEE ICSE*.
- Liu, Y., et al. (2020). "Deep Learning for System Call-Based Anomaly Detection: A Survey." *ACM Computing Surveys*.

<iframe width="560" height="315" src="https://www.youtube.com/embed/VIDEO_ID" frameborder="0" allowfullscreen></iframe>
