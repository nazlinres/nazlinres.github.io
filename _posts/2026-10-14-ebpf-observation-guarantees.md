---
title: 'eBPF as an Observation Substrate for Container Security: What Can Be Guaranteed?'
date: 2026-10-14
permalink: /posts/2026/10/ebpf-observation-guarantees/
tags:
  - eBPF
  - runtime security
  - container security
  - observability
---

In my previous post I argued that syscall-based detection is limited by what the sensor records, particularly by whether it captures arguments and not only call names. This post examines the sensor itself. eBPF has become the dominant mechanism for collecting runtime telemetry in cloud-native environments, and tools such as Falco, Tracee, and Tetragon build on it. A detection model can only be as sound as the observations it receives, so the question here is: *what properties does eBPF-based observation actually guarantee, and where do those guarantees end?*

## 1. Why eBPF Became the Default

eBPF allows sandboxed programs to run inside the kernel, attached to hooks such as tracepoints, kprobes, and Linux Security Module (LSM) hooks. Before a program is loaded, the in-kernel verifier checks that it terminates, accesses memory safely, and respects bounded resource use. For security monitoring, this offers three attractive properties:

- **Single vantage point:** Because containers share one kernel, one sensor observes all workloads without modifying images or injecting sidecars.
- **Safety by construction:** The verifier reduces the risk that a monitoring program crashes the host, compared with a loadable kernel module.
- **Low overhead:** Filtering and aggregation can occur in kernel space, so only relevant events cross into user space.

These are properties of the *platform*. They are not properties of the *observations*, and the distinction is the subject of this post.

## 2. The Completeness Problem

A sound detector requires that the event stream be complete, or that its incompleteness be known. Several mechanisms can break completeness silently.

**Event loss under load.** Events are typically transferred to user space through perf buffers or ring buffers of finite size. When a workload produces events faster than the consumer drains them, events are dropped. An attacker who can generate high-volume benign activity may therefore degrade visibility at precisely the moment it matters. A monitoring system should expose its drop counters as first-class signals, since a silent gap is indistinguishable from the absence of activity.

**Verifier-imposed limits.** The verifier restricts program complexity, stack size, and loop structure. Complex logic, such as deep path resolution or rich argument parsing, must be truncated or deferred to user space. Truncation is a design decision with security consequences: a path cut off at a fixed length may hide the component that distinguishes a benign file from a sensitive one.

**Coverage of hooks.** A sensor attached to a subset of syscalls or kernel functions observes only that subset. New kernel interfaces, such as newer asynchronous I/O mechanisms like io_uring, can allow operations that bypass hooks designed around traditional syscalls. Security researchers have noted that monitoring tools built around classical syscall tracing may have blind spots for such interfaces.

## 3. The Time-of-Check to Time-of-Use Problem

The argument-capture issue from my previous post has a deeper layer. When a sensor attaches at syscall *entry*, pointer arguments such as file paths refer to user-space memory. The kernel reads that memory later, and a multithreaded process can modify it in between. A monitor that reads the path at entry may record a different value from the one the kernel ultimately uses.

This class of weakness in syscall-entry monitoring was publicly demonstrated by Guo and Zeng (2021) under the name "Phantom attacks." The general lesson is architectural: *where* a hook is placed determines whether its observations can be trusted. Hooks placed after the kernel has copied and validated arguments, for example at LSM hooks or on internal kernel functions, observe the values that are actually used. This improves integrity, but at a cost: such hooks are more kernel-version-specific and less stable as interfaces.

This tension between observation fidelity and portability recurs throughout the design space.

## 4. Portability and the Maintenance Burden

Kernel internals are not a stable interface. Programs attached to kernel functions may break across versions when functions are renamed or structures change. The CO-RE (Compile Once, Run Everywhere) approach, based on BTF type information, mitigates this by relocating field accesses at load time, though it requires kernels built with BTF support and does not remove all version dependence.

For researchers, this has a methodological implication. A detection result reported on one kernel version and sensor configuration may not be reproducible on another if the event stream differs. Papers should therefore report the kernel version, the hooks used, the argument handling policy, and the observed drop rates. Without these, a claim about "syscall-based detection" is under-specified.

## 5. The Sensor as Part of the Attack Surface

Loading eBPF programs requires elevated privileges (on recent kernels, capabilities such as CAP_BPF and CAP_PERFMON, in addition to others depending on the program type). A runtime security agent therefore typically runs as a highly privileged workload. Its compromise would grant an attacker both broad host access and the ability to blind the monitoring. This is a familiar pattern: the security component enlarges the trusted computing base it is meant to protect.

Two mitigations are commonly discussed: minimizing the agent's privileges and exposed interfaces, and treating the agent's own integrity as an object of monitoring. Neither eliminates the problem, and neither is guaranteed by eBPF itself.

## 6. Summary of Guarantees

| Property | Provided by eBPF itself? | Depends on |
|---|---|---|
| Safe execution of the probe | Largely yes (verifier) | Verifier correctness |
| Complete event delivery | No | Buffer sizing, load, consumer speed |
| Argument integrity | No | Hook placement (entry vs. post-validation) |
| Coverage of all relevant operations | No | Hook selection, kernel interfaces in use |
| Portability across kernels | Partially | BTF/CO-RE availability |
| Sensor integrity | No | Agent privileges and hardening |

## 7. Open Questions

- Can a monitoring system provide a *measurable completeness bound*, so that downstream models know how much of the true event stream they are seeing?
- How should detection models treat missing data: as noise, as a feature, or as evidence in itself?
- What reporting standards would make runtime security evaluations comparable across kernel versions and sensor designs?

These questions link the sensing layer to the modeling layer. A learned model that is accurate on a clean, complete stream may behave very differently on the degraded streams found in production, which is why I regard sensor characterization as part of the research problem, not as preparatory engineering.

## References

- Guo, R., & Zeng, J. (2021). Phantom Attacks: Evading System Call Monitoring. *DEF CON 29*.
- Gregg, B. (2019). *BPF Performance Tools*. Addison-Wesley.
- Linux kernel documentation. BPF documentation (verifier, BTF, CO-RE).
- Forrest, S., Hofmeyr, S. A., Somayaji, A., & Longstaff, T. A. (1996). A Sense of Self for Unix Processes. *IEEE Symposium on Security and Privacy*.
