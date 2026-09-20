# Strike Robot + JEV

> A fast, confidence-aware decision layer for safer Physical AI.

Strike Robot combines **SR-Agentics**, a task-conditioned scene-understanding stack, with **JEV** to help robots make rapid, structured decisions in real-world environments. It is designed for missions where acting quickly matters—but acting without enough confidence is unacceptable.

## Why this exists

Patrol, industrial-inspection, and search-and-rescue robots operate in uncertain environments. Sending every event to a large reasoning model is slow and expensive; acting on a single weak signal can be unsafe.

Strike Robot uses JEV as a lightweight decision gate between perception and deeper deliberation. It determines whether the robot has enough evidence to continue, should gather more evidence, needs to escalate to an operator, or must enter an emergency response.

## Decision architecture

```text
Sensors and robot state
        ↓
Task-Conditioned Scene Graph
        ↓
JEV — typed probabilities and confidence
        ↓
Risk-weighted action gate
        ↓
CONTINUE · RE-OBSERVE · VERIFY · ESCALATE · EMERGENCY
        ↓
VLM / LLM verification and reporting when needed
        ↓
Local edge safety controls
```

### The role of each layer

| Layer | Responsibility |
| --- | --- |
| **SR-Agentics** | Builds a compact mission-relevant state from observations, scene relationships, policy, and action history. |
| **JEV** | Answers typed decision questions in parallel, returning a probability and confidence for each. |
| **Action gate** | Applies safety thresholds and severity weights to choose the next safe action. |
| **VLM / LLM** | Performs deeper visual or multimodal verification only when the situation warrants it. |
| **Edge safety** | Keeps real-time motion constraints and safety-critical control local to the robot. |

## Core capabilities

- **Fast structured decisions** — JEV returns decision-ready probabilities and confidence rather than open-ended text.
- **Confidence-aware abstention** — low confidence triggers re-observation or human review instead of a guess.
- **Risk-weighted response** — operational severity determines whether an event should be verified, escalated, or treated as an emergency.
- **Efficient model routing** — deeper VLM/LLM reasoning is reserved for events that need it.
- **Auditable operation** — evidence, thresholds, decisions, and escalation paths can be logged for review and field validation.

## Decision policy

At each time step, SR-Agentics constructs a compact task-conditioned state:

```text
xₜ = Φ(scene graph, observations, mission context, action history)
```

For each decision question, JEV returns:

```text
JEV(xₜ, question) → (probability, confidence)
```

The system combines those outputs into a risk score and applies calibrated thresholds:

```text
high risk                     → EMERGENCY
meaningful risk               → VERIFY
insufficient confidence       → RE-OBSERVE
otherwise                     → CONTINUE
```

Thresholds and severity weights must be calibrated using simulation, logged field trials, and site operating procedures—not headline benchmark performance alone.

## Example deployments

### Factory perimeter and hazard triage

A robot observes an unexpected liquid pool near a restricted maintenance bay. JEV can estimate hazard likelihood, restricted-zone risk, and evidence confidence. If the aggregate risk crosses the emergency threshold, Strike Robot can stop forward motion, maintain a safe standoff distance, save synchronized sensor evidence, and raise a high-priority alert. A VLM may then verify the scene and produce an explanatory report.

### Search and rescue

When a robot sees a possible human silhouette behind debris, confidence may be too low to declare a victim while accessibility risk remains high. Instead of making an unsupported claim or attempting an unsafe approach, Strike Robot can request another viewpoint, activate thermal or acoustic sensing, mark the location in the scene graph, and send an evidence packet to the incident commander.

## Safety principles

- JEV does **not** replace perception, motion planning, safety-certified controls, or human operating procedures.
- The system does **not** identify hazardous substances from visual evidence alone; it reports probable events and routes them into an appropriate verification workflow.
- Safety-critical motion control remains local at the edge and independent of cloud-level model routing.
- Every action constraint, escalation route, confidence threshold, and risk weight must be validated for the deployment environment.

## Repository status

This repository documents the Strike Robot + JEV decision architecture. Implementation modules, deployment recipes, and evaluation assets can be added as the system evolves.

## License

Licensed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
