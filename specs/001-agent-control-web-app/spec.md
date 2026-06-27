# Feature Specification: Agent Control Web App

**Feature Branch**: `001-agent-control-web-app`

**Created**: 2026-06-27

**Status**: Draft outline

**Input**: User description: "Build a web app to manage Codex and Claude-style agent work across Threads, Approvals, Decisions, Projects, and Tasks. Use Spec Kit for spec writing and coding. High-level bullets only for now."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Monitor Active Agent Work (Priority: P1)

- As a user, I want one place to see active and historical agent threads across supported providers.
- I want to know what is running, blocked, complete, interrupted, or stale.
- I want each thread connected to the project, task, approvals, decisions, and analyses it produced.

**Why this priority**: The app is not useful unless it becomes the user's operational view of agent activity.

**Independent Test**: A user can open the Threads view and understand the state and ownership of current agent work without opening each provider separately.

**Acceptance Scenarios**:

1. **Given** agent activity exists, **When** the user opens Threads, **Then** the user sees current status, provider, workspace, and related task.
2. **Given** a thread is blocked, **When** the user views it, **Then** the user can navigate to the blocking approval, decision, or task state.

---

### User Story 2 - Resolve Approvals Safely (Priority: P1)

- As a user, I want a dedicated approvals inbox for live permission requests.
- I want approvals to show risk context, source provider, requested action, affected project/task/thread, and expiration/staleness state.
- I want approving or denying to use the provider's native return path when still live.

**Why this priority**: Approvals are the most time-sensitive and actionable item in the system.

**Independent Test**: A user can identify a pending approval, understand the request, choose an outcome, and see whether the provider accepted the response.

**Acceptance Scenarios**:

1. **Given** a live approval exists, **When** the user approves it, **Then** the approval records the outcome and the originating agent can continue.
2. **Given** an approval is stale, **When** the user views it, **Then** the app prevents false approval and explains that the original live request expired.

---

### User Story 3 - Audit Decisions (Priority: P2)

- As a user, I want a decisions log that records user, policy, hook, analyzer, and automation outcomes.
- I want approvals to appear as one type of decision, not the only type.
- I want decisions linked to the task, thread, event, approval, or analysis they affected.

**Why this priority**: Decisions explain why work continued, stopped, changed direction, or required human attention.

**Independent Test**: A user can inspect a task or thread and understand the chain of important choices that shaped it.

**Acceptance Scenarios**:

1. **Given** an approval was answered, **When** the user opens Decisions, **Then** the answer appears with actor, reason, time, and related approval.
2. **Given** a policy blocked an action, **When** the user views the decision, **Then** the user sees the policy outcome and affected work item.

---

### User Story 4 - Manage Projects and Tasks (Priority: P2)

- As a user, I want projects to group related agent work by repo, product area, initiative, or operating context.
- As a user, I want tasks to be the canonical units of work above provider-specific threads.
- I want tasks to contain plans, steps, runs, approvals, decisions, analyses, and artifacts.

**Why this priority**: Provider threads are not enough to manage durable work across retries, agents, and tools.

**Independent Test**: A user can create or inspect a project, open a task, and see all related agent work and current next action.

**Acceptance Scenarios**:

1. **Given** a project exists, **When** the user opens it, **Then** the user sees tasks grouped by state and priority.
2. **Given** a task has multiple agent runs, **When** the user views the task, **Then** the user sees each run without losing the task-level source of truth.

---

### User Story 5 - Understand Analyses and Next Actions (Priority: P3)

- As a user, I want the app to summarize what happened after important events.
- I want analyses to identify blocked reasons, risk, completion state, and likely next action.
- I want analyses to recommend decisions without silently taking action unless policy allows it.

**Why this priority**: Analyses make the system feel intelligent, but they depend on the event and task model being useful first.

**Independent Test**: A user can open a completed or blocked task and understand what happened and what to do next.

**Acceptance Scenarios**:

1. **Given** a run completed, **When** analysis finishes, **Then** the task shows summary, status guess, risk, and next action.
2. **Given** analysis recommends a user decision, **When** the user reviews it, **Then** the recommendation is clearly separate from an executed decision.

### Edge Cases

- Live approval handle is lost, expired, or already answered elsewhere.
- Multiple providers report related work for the same task.
- A thread is active but disconnected from any task.
- A task has conflicting analyses from different runs.
- An automation proposes an unsafe or low-confidence decision.
- A provider changes event shapes or fails to deliver expected lifecycle events.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST provide left-nav areas for Threads, Approvals, Decisions, Projects, and Tasks.
- **FR-002**: System MUST treat Projects and Tasks as canonical app-owned records.
- **FR-003**: System MUST link provider-specific threads, turns, sessions, and runs back to app-owned tasks when possible.
- **FR-004**: System MUST distinguish live approvals from historical decisions.
- **FR-005**: System MUST prevent users from acting on stale approvals as if they were still live.
- **FR-006**: System MUST record decisions with actor, subject, outcome, reason, and timestamp.
- **FR-007**: System MUST support analyses as derived records attached to projects, tasks, threads, approvals, decisions, or events.
- **FR-008**: System MUST show task state, next action, blocked reason, and related agent runs.
- **FR-009**: System MUST support provider adapters for ingesting thread/activity events and returning approval decisions.
- **FR-010**: System MUST preserve raw provider event payloads for audit and future parser improvements.
- **FR-011**: System MUST make recommendations visibly different from executed decisions.
- **FR-012**: System MUST support high-level plans and steps on tasks without requiring provider-specific plan formats.

### Key Entities

- **Project**: Durable grouping for related agent work; may map to a repo, workspace, initiative, or operating context.
- **Task**: Canonical unit of work owned by the app; may have plans, steps, runs, approvals, decisions, analyses, and artifacts.
- **Plan**: Proposed path for completing a task; can evolve before or during work.
- **Step**: Trackable part of a plan or task; can be pending, active, blocked, complete, skipped, or superseded.
- **Thread/Run**: Provider-specific execution attempt or conversation linked to a task.
- **Approval**: Live or historical permission gate that may require a provider-native response.
- **Decision**: Chosen outcome by a user, policy, hook, automation, analyzer, or agent.
- **Analysis**: Derived interpretation of events, state, risk, completion, and next action.
- **Event**: Raw provider or app lifecycle record.
- **Artifact**: Output, diff, document, summary, or evidence produced during work.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users can identify all currently blocked agent work from the Approvals or Tasks view in under 30 seconds.
- **SC-002**: Users can trace a completed task from task record to provider runs, approvals, decisions, and analyses without leaving the app.
- **SC-003**: Live approvals clearly show whether they are actionable, stale, answered, or cancelled.
- **SC-004**: At least 95% of agent events ingested by supported adapters remain available as raw audit records.
- **SC-005**: Users can distinguish recommendations from executed decisions in every decision-related view.
- **SC-006**: A task can survive multiple provider runs, retries, interruptions, and analyses while preserving one canonical task state.

## Assumptions

- The first version is a web app backed by a local or self-hosted broker, not a Zed fork.
- Codex and Claude are the first provider families to support.
- Provider integrations are adapter-based so future agents can be added without changing core product concepts.
- Projects and Tasks are app-owned, even when providers expose their own thread, session, task, todo, or plan structures.
- Analyses are derived and auditable; they can recommend actions but should not silently mutate critical state by default.
- Detailed implementation planning, storage choices, UI framework choices, and provider protocol details belong in later Spec Kit planning phases.
