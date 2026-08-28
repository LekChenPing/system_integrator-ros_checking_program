# Yahboom Robot Validation Package
## Prototype Operator-Assisted Validation Scripts

### Purpose

This package provides a prototype, operator-assisted workflow for checking Yahboom teaching robots.

It contains:
- Phase 1: ROS 2 quick checks
- Phase 2: robot control and LiDAR checks
- Phase 3: mapping/navigation stack checks
- Master runner: sequential execution of all three phases

This is **not a fully automated or certified test framework**. Some acceptance decisions still depend on operator observation of the physical robot, terminal output, and RViz.

## Package files

```text
phase1_ros_quickcheck_v1_2_0_0_documented.py
phase2_robot_ctrl_lidar_v1_1_0_1_documented.py
phase3_navigation_stack_v1_0_0_0_documented.py
robot_validation_master_v1_0_0_0_documented.py
README_INSTRUCTIONS.md
KNOWN_LIMITATIONS.md
PACKAGE_MANIFEST.txt
```

## Expected environment

Default paths used by the scripts:

```text
Phase 1 workspace: ~/ros2_ws
Phase 2 workspace: ~/yahboomcar_ws
Phase 3 workspace: ~/yahboomcar_ws
Result root:       ~/robot_check_results
```

Requirements:
- Ubuntu / Linux environment used by the Yahboom robot
- ROS 2 installation expected by the robot workspaces
- Python 3
- Required Yahboom ROS packages and launch files already installed
- Required USB devices physically connected

## Installation

```bash
mkdir -p ~/robot_tools
cp phase1_ros_quickcheck_v1_2_0_0_documented.py ~/robot_tools/
cp phase2_robot_ctrl_lidar_v1_1_0_1_documented.py ~/robot_tools/
cp phase3_navigation_stack_v1_0_0_0_documented.py ~/robot_tools/
cp robot_validation_master_v1_0_0_0_documented.py ~/robot_tools/
chmod +x ~/robot_tools/*.py
```

The master runner expects the three phase scripts to be in the same directory unless alternate paths are provided.

## Check the interface

```bash
python3 ~/robot_tools/robot_validation_master_v1_0_0_0_documented.py --help
```

Each phase also supports `--help`.

## Dry run

```bash
python3 ~/robot_tools/robot_validation_master_v1_0_0_0_documented.py \
  R23 \
  --dry-run \
  --no-open-report
```

Replace `R23` with the actual robot ID.

A dry run checks program flow and result generation without treating it as a real hardware validation. `REVIEW` statuses during dry run are normal.

## Full validation run

```bash
python3 ~/robot_tools/robot_validation_master_v1_0_0_0_documented.py R23
```

The master executes:

```text
Phase 1 — ROS CHECK
Phase 2 — ROBOT CONTROL / LIDAR CHECK
Phase 3 — NAVIGATION STACK CHECK
```

## Operator inputs

```text
Y = observed behaviour is acceptable / PASS
N = observed behaviour is not acceptable / FAIL
U = uncertain / further review required
q = finish the current keyboard-control or observation stage
```

`q` ends an observation/control stage. It is not itself a PASS judgement.

Do not select `Y` only because a process remains running. Check the required physical robot and/or RViz behaviour.

## Results

Default result root:

```text
~/robot_check_results
```

A master run creates a directory similar to:

```text
~/robot_check_results/R23/<timestamp>_master/
```

Typical outputs:

```text
master_report.txt
master_result.json
master_event.log
phase1_result.json
phase2_result.json
phase3_result.json
phase_runs/
```

## Continue after a failed phase

```bash
python3 ~/robot_tools/robot_validation_master_v1_0_0_0_documented.py \
  R23 \
  --continue-on-fail
```

Use this only when the operator understands the earlier failure and determines that continuing is safe and useful.

## Run phases independently

```bash
python3 ~/robot_tools/phase1_ros_quickcheck_v1_2_0_0_documented.py R23
python3 ~/robot_tools/phase2_robot_ctrl_lidar_v1_1_0_1_documented.py R23
python3 ~/robot_tools/phase3_navigation_stack_v1_0_0_0_documented.py R23
```

These are phase-level programs. Individual checks inside a phase are not yet fully separated into standalone test-case executables.

## Important LiDAR note

The current code is not fully consistent about LiDAR device aliases:

- Phase 2 exposes `--lidar-port`; its current default is `/dev/ydlidar`.
- Parts of Phase 2 also inspect `/dev/rplidar`.
- Phase 3 explicitly inspects `/dev/rplidar`.

Before relying on LiDAR results, confirm the actual udev alias:

```bash
ls -l /dev/myserial /dev/rplidar /dev/ydlidar /dev/ttyUSB* /dev/ttyACM* 2>/dev/null
```

Do not classify a LiDAR as hardware-failed solely because one expected alias is absent.

## Status meanings

```text
PASS     acceptable evidence
FAIL     acceptance condition not met
REVIEW   incomplete/ambiguous evidence or human review required
ERROR    program/runtime error
BLOCKED  not executed because an earlier dependency blocked it
SKIPPED  intentionally not executed
```

## Development / maintenance note

This package is a prototype produced from actual robot-validation work. It is useful as an operator-assisted commissioning workflow and as a reference implementation for future development.

Recommended future architecture:

```text
small independently executable test cases
        ↓
reusable execution / evidence / cleanup framework
        ↓
phase-level test suites
        ↓
thin master runner
```

AI assistance was used during development to help structure requirements, implementation, documentation, and runtime troubleshooting. Hardware acceptance remains dependent on actual robot evidence and operator judgement.
