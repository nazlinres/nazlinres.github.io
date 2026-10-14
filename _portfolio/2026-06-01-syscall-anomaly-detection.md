---
title: "A Python Library for Syscall-Based Anomaly Detection"
collection: portfolio
permalink: /portfolio/2026-06-01-syscall-anomaly-detection
excerpt: 'An open-source Python library that models system call sequences in container environments as a basis for detecting deviations from normal behavior.'
date: 2026-06-01
---

Because containers share the host kernel, a process's interaction with that kernel provides one of the most direct observations of its behavior. This project is an open-source Python library built on that premise: it treats system call (syscall) sequences in container environments as a behavioral signal and detects anomalies as statistically meaningful deviations from a learned model of normal execution.

**Conceptual Approach:**

- **Sequence-based behavioral representation:** Syscall streams are monitored in real time and treated as ordered observations of process behavior.
- **N-gram feature extraction:** Local sequential structure is captured through n-gram representations, offering a simple and interpretable baseline.
- **LSTM-based anomaly detection:** Longer-range temporal dependencies are modeled with a recurrent neural architecture, allowing subtler deviations to be identified.
- **Threshold calibration:** Detection thresholds are tuned to manage the trade-off between sensitivity and false positive rates.
- **Deployment context:** The library is designed to be compatible with Docker and Kubernetes environments.

**Technologies:** Python, PyTorch, Scikit-learn, Docker, Kubernetes

**Project Status:** Under development

**GitHub:** [github.com/nazlinres/syscall-anomaly-detection](https://github.com/nazlinres/syscall-anomaly-detection)
