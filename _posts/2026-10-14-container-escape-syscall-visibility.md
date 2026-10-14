---
title: 'What Syscall Sequences Can and Cannot Tell Us About Container Escapes'
date: 2026-10-14
permalink: /posts/2026/10/container-escape-syscall-visibility/
tags:
  - container security
  - syscall analysis
  - anomaly detection
  - cloud-native security
---

Containers do not virtualize hardware. They partition a single kernel's view of the system using namespaces, limit resource consumption with cgroups, and restrict capabilities and system calls with mechanisms such as seccomp. The security boundary is therefore a set of kernel-enforced policies, not a hardware-enforced separation. A container escape is, by definition, an event in which a process violates one of these policies. This post asks a specific question: if we observe only the system calls a container makes, which classes of escape are visible, and which are structurally hidden from us?

## 1. The Observation Model

Syscall-based anomaly detection rests on a simple premise: the sequence of system calls a process issues is a compact behavioral signature. Forrest et al. (1996) introduced this idea for host intrusion detection by modeling short sequences of calls as a "self" profile, and later work extended it with richer statistical and neural sequence models.

Applied to containers, this premise has a practical appeal. Because all containers on a host share one kernel, a single observation point (for example, an eBPF-based sensor) can monitor every workload without modifying images. However, the premise carries an implicit assumption: *that malicious behavior manifests as a different ordering or frequency of syscalls.* Whether this holds depends on the escape mechanism.

## 2. A Taxonomy by Mechanism

It is useful to classify escapes by what they exploit, because each class leaves a different footprint at the syscall level.

**Class A: Kernel vulnerabilities.** The attacker triggers a flaw in the shared kernel itself, for example a memory-corruption bug reachable through a syscall interface. Dirty Pipe (CVE-2022-0847) is a well-known example: a flaw in pipe buffer handling allowed unprivileged processes to overwrite data in read-only files. Footprint: often unusual use of less-common syscalls or unusual argument patterns, which sequence models may flag, though exploit code can be written to resemble benign calls.

**Class B: Container runtime vulnerabilities.** The flaw lies in the runtime that sets up the container, not in the kernel. CVE-2019-5736 in runc allowed a malicious container process to overwrite the host runc binary through `/proc/self/exe`. CVE-2024-21626 involved a leaked file descriptor that could cause the container's working directory to resolve outside the container's root. Footprint: the individual syscalls (`open`, `write`, `chdir`) are entirely ordinary. What is abnormal is the *path argument* and the *target*, not the call.

**Class C: Misconfiguration.** The container is granted privileges that dissolve the boundary by design, such as `--privileged` mode, a mounted container runtime socket, or excessive Linux capabilities. Footprint: the attacker uses legitimate, permitted operations (`mount`, `ptrace`, `setns`). Detecting abuse requires context about what the workload *should* be permitted to do.

## 3. The Argument Blindness Problem

The taxonomy exposes a central limitation. Most classical and neural syscall models operate on a sequence of syscall *names* (or numbers). In Class B and Class C escapes, the decisive information is in the *arguments*: which path was opened, which flags were set, which namespace was entered. A sequence such as `open → write → close` is indistinguishable at the name level whether it writes to an application log or to a host binary.

Including arguments enlarges the feature space considerably. File paths are high-cardinality and environment-specific, which raises two problems:

- **Vocabulary explosion:** Models must either hash or abstract paths, losing precision, or accept sparse, hard-to-generalize inputs.
- **Portability:** A profile learned on one deployment may encode paths that do not exist in another, inflating false positives when workloads change.

This suggests that the choice of representation, not the choice of model architecture, may be the dominant factor in detection quality for runtime-level escapes.

## 4. Seccomp as a Prior, Not a Detector

Seccomp filtering is sometimes discussed alongside anomaly detection, but they play different roles. A seccomp profile is a *static allowlist or denylist*: it blocks specific syscalls outright. Docker's default profile, for instance, blocks a few dozen syscalls considered dangerous while permitting the majority required by typical applications.

This makes seccomp effective against escapes that depend on a blocked call, and ineffective against attacks that use only permitted calls. Anomaly detection is complementary: it can, in principle, flag deviations among permitted behavior. The two can be combined productively, since a learned behavioral profile can be used to *generate* a tighter, workload-specific seccomp profile, shrinking the attack surface that detection must cover.

## 5. Evaluation Pitfalls

Several methodological issues recur in this literature and deserve scrutiny:

1. **Dataset realism:** Many evaluations rely on legacy syscall datasets (such as ADFA-LD) collected from conventional hosts, not containerized microservices. Results may not transfer.
2. **Attack realism:** Injected "attacks" are sometimes synthetic sequences, which tests whether a model detects *that particular pattern*, not whether it detects an adversary who is aware of the detector.
3. **Concept drift:** Container workloads are redeployed frequently. A model trained on last week's behavior may treat a legitimate update as an anomaly.
4. **Adversarial adaptation:** An attacker who knows a detector models short subsequences can pad malicious activity with benign calls. This line of work, known as mimicry attacks (Wagner and Soto, 2002), predates deep learning and is not eliminated by it.

## 6. Open Questions

- How should arguments be represented so that detection captures security-relevant semantics (for example, whether a path lies outside the container's expected filesystem view) without overfitting to a single deployment?
- Can detection be made robust to mimicry, or should it be treated only as one layer in a defense that also includes isolation hardening?
- What is the cost of high-fidelity sensing? eBPF-based collection is comparatively lightweight, but argument capture and inference introduce overhead that must be measured under realistic loads.

These questions motivate the open-source syscall anomaly detection library listed in my portfolio, which currently explores n-gram and LSTM baselines as a starting point for answering them empirically.

## References

- Forrest, S., Hofmeyr, S. A., Somayaji, A., & Longstaff, T. A. (1996). A Sense of Self for Unix Processes. *IEEE Symposium on Security and Privacy*.
- Wagner, D., & Soto, P. (2002). Mimicry Attacks on Host-Based Intrusion Detection Systems. *ACM CCS*.
- Creech, G., & Hu, J. (2014). A Semantic Approach to Host-Based Intrusion Detection Systems Using Contiguous and Discontiguous System Call Patterns. *IEEE Transactions on Computers*, 63(4).
- Karlsson, M. (2022). The Dirty Pipe Vulnerability (CVE-2022-0847). Disclosure write-up, cm4all.
- NIST National Vulnerability Database. CVE-2019-5736 and CVE-2024-21626 entries.
