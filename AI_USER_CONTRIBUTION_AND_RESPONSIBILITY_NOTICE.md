# AI and User Contribution & Responsibility Notice

## Purpose of This Notice

This document records, in explicit terms, how the current robot validation programs were developed, how responsibilities were divided between the human developer/operator and AI assistance, and what limitations remain.

The programs in this package should be treated as **prototype, operator-assisted validation tools**. They are intended to support testing work, not to replace engineering judgement, physical inspection, or independent verification.

---

## 1. Human Contribution and Responsibility

The human developer/operator contributed the engineering context and practical test intent, including:

- identifying the robot functions and subsystems that needed to be checked;
- describing the intended test sequence and operational workflow;
- defining or refining the requirements to be examined;
- defining what observable behaviour should be considered acceptable, failed, uncertain, or in need of further review;
- determining the practical success criteria used to judge whether a test:
  - executed successfully,
  - produced the expected result,
  - passed the intended validation objective,
  - or required further human review;
- carrying out physical robot testing and observing actual hardware behaviour;
- providing runtime observations, screenshots, terminal outputs, and failure information;
- deciding the final physical or visual acceptance judgement where the program cannot determine correctness automatically.

The human operator remains responsible for deciding whether the evidence produced by the scripts is sufficient for the engineering purpose for which the scripts are being used.

---

## 2. AI Contribution

AI assistance was used as a development aid.

Its contribution included:

- helping organise the overall program structure;
- converting verbally described test intentions into clearer program logic;
- helping structure requirements and test-flow definitions;
- proposing Python implementation patterns;
- generating and refining portions of the source code;
- helping organise PASS / FAIL / REVIEW / ERROR handling;
- helping define and document test-result structures;
- assisting with process management, logging, reporting, and command orchestration;
- helping analyse observed runtime errors and suggest possible revisions;
- helping produce program documentation and operating instructions.

AI-generated or AI-assisted code was produced from the information, requirements, assumptions, and observations available during development.

AI assistance does **not** independently verify that the physical robot, wiring, sensors, ROS configuration, or test assumptions are correct.

---

## 3. Requirement and Success-Criteria Definition

A test result depends on the requirement and acceptance criteria defined for that test.

The current programs may use a combination of:

- automatic command return status;
- process state;
- expected terminal output;
- ROS topic or device availability;
- USB / serial-device information;
- timing and readiness checks;
- operator observation;
- manual Y / N / U judgement.

Therefore:

> **A program completing without a software error does not necessarily mean that the robot has passed the engineering test.**

Similarly:

> **A process remaining active does not necessarily mean that the underlying hardware or ROS function is operating correctly.**

A test should only be treated as PASS when the available evidence satisfies the intended acceptance criteria for that test.

Where the evidence is incomplete or ambiguous, the appropriate result is **REVIEW / Further Review Required**, rather than assuming PASS or hardware failure.

---

## 4. Distinction Between "Worked" and "Passed"

For these programs, the following distinction is important:

### Program / Test Worked

The test procedure itself executed as intended, for example:

- the required command was launched;
- the program completed the expected execution path;
- evidence was collected;
- the program did not encounter an unexpected software exception.

### Test Passed

The observed evidence also met the engineering acceptance criteria defined for that test.

A test can therefore:

```text
WORKED + PASSED
WORKED + FAILED
WORKED + REVIEW REQUIRED
PROGRAM ERROR / TEST NOT COMPLETED
```

These conditions should not be treated as equivalent.

---

## 5. Unforeseen Conditions

The current implementation was developed from the test cases, robot behaviour, hardware configurations, and failure situations encountered during the available development period.

There may be **unforeseen conditions that are not currently represented in the program logic**, including but not limited to:

- unexpected USB enumeration behaviour;
- different `/dev` aliases or udev rules;
- intermittent cables or connectors;
- USB hub or power instability;
- different LiDAR models or serial converters;
- ROS package or launch-file changes;
- changed terminal output;
- delayed ROS node or topic startup;
- hardware faults that imitate software faults;
- software faults that imitate hardware faults;
- multiple devices with similar USB identities;
- processes that remain alive while the subsystem is malfunctioning;
- environmental or physical conditions not represented in the acceptance logic;
- failure combinations that were not encountered during development.

The absence of a programmed check for a condition does not mean that the condition cannot occur.

---

## 6. Required Caution When Using the Programs

Use the programs as an **engineering aid**, not as the sole authority for hardware acceptance.

Operators should:

- confirm the correct robot ID before testing;
- verify the physical setup and USB connections;
- monitor the robot during motor or navigation tests;
- review terminal and RViz behaviour where applicable;
- inspect generated logs when the result is unexpected;
- use `REVIEW` when the evidence is insufficient;
- avoid declaring a component defective solely from one missing alias or one failed software check;
- stop the test if physical behaviour appears unsafe or inconsistent with expectations;
- independently investigate results that could have safety, equipment, or operational consequences.

---

## 7. Current Development Status

The package should currently be described as:

> **Prototype operator-assisted robot validation scripts with partially automated checks and human-in-the-loop acceptance.**

It should **not** currently be described as:

- a fully autonomous test system;
- a complete verification framework;
- a safety-certified diagnostic system;
- a guarantee that all possible robot failures will be detected.

Future development should progressively separate the current phase-level programs into smaller, independently repeatable test cases and validate each test case against known-good and known-fault conditions.

---

## 8. Responsibility Summary

```text
Human developer/operator:
    Test intent
    Engineering context
    Requirement definition/refinement
    Physical observations
    Acceptance judgement
    Final interpretation of evidence

AI assistance:
    Logic organisation
    Program-structure assistance
    Code generation/refinement
    Documentation
    Troubleshooting support

Program:
    Executes defined logic
    Collects available evidence
    Reports results according to implemented criteria

Not guaranteed:
    Detection of every possible or unforeseen hardware/software condition
```

The user of this package should review the actual test evidence and apply engineering judgement before treating any result as final.
