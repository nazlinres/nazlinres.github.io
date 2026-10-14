---
title: "A Python Library for Syscall-Based Anomaly Detection"
excerpt: "An open-source library that models system call sequences in container environments to detect deviations from normal behavior. <br/><img src='/images/500x300.png'>"
collection: portfolio
---

Because containers share the host kernel, a process's interaction with that kernel is one of the most direct observations of its behavior. This project is an open-source Python library built on that premise: it treats system call (syscall) sequences as a behavioral signal and detects anomalies as statistically meaningful deviations from a learned model of normal execution.

**Conceptual Approach**

- **Sequence-based representation:** Syscall streams are treated as ordered observations of process behavior.
- **N-gram feature extraction:** Local sequential structure is captured through n-gram representations, offering a simple and interpretable baseline.
- **LSTM-based detection:** Longer-range temporal dependencies are modeled with a recurrent architecture, allowing subtler deviations to be identified.
- **Threshold calibration:** Detection thresholds are tuned to balance sensitivity against false positive rates.
- **Deployment context:** Designed for compatibility with Docker and Kubernetes environments.

**Technologies:** Python, PyTorch, Scikit-learn, Docker, Kubernetes

**Project Status:** Under development

**GitHub:** [github.com/nazlinres/syscall-anomaly-detection](https://github.com/nazlinres/syscall-anomaly-detection)
