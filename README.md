# ZCA Agents

Agents built using **Zero Context Architecture (ZCA)**.

ZCA is an architectural approach for building reliable agent systems that interact with real-world environments. It separates decision-making, execution, and environment interaction into distinct layers.

Instead of treating agents as a single monolithic system, ZCA structures them as:

```text
                OPERATOR SYSTEM
        (goals, evaluation, boundaries)
                        │
                        │ goal + context
                        ▼
              ┌─────────────────┐
              │  ZCA Agent      │
              │  (decision)     │
              └─────────────────┘
                        │
                        │ execution request
                        ▼
              ┌─────────────────┐
              │ BrowserRuntime  │
              │ (execution)     │
              └─────────────────┘
                        │
                        │ browser actions
                        ▼
              ┌─────────────────┐
              │ Website / APIs  │
              │ Environment     │
              └─────────────────┘
                        ▲
                        │
                        │ observations / state
                        │
              ┌─────────────────┐
              │ Deterministic   │
              │ Boundary Check  │
              └─────────────────┘
                        │
                        │ result + trace
                        ▼
                OPERATOR SYSTEM
```

## Why ZCA Agents Exist

Many agent systems mix reasoning, execution, and environment interaction into one layer. That makes them hard to debug, hard to evolve, and fragile in dynamic environments.

ZCA agents enforce clear boundaries:

- the operator layer defines what must be achieved
- the execution runtime performs actions in the environment
- the verification layer determines whether the result is acceptable

This separation improves:

- reliability
- observability
- traceability
- system evolution

## Core Concepts

### Operator Tasks

Agents operate on explicit tasks, not raw prompts.

Example:

```typescript
execute({
  task: "Extract the invoice from the billing portal",
  capability: "automation.extract_invoice",
  context: {
    portal: "stripe",
    accountId: "acct_123",
    invoiceId: "inv_1024"
  },
  verify(result) {
    const violations: string[] = [];

    if (typeof result.invoiceNumber !== "string" || result.invoiceNumber.length === 0) {
      violations.push("Missing invoice number");
    }

    if (typeof result.amount !== "number") {
      violations.push("Missing amount");
    } else if (result.amount <= 0) {
      violations.push("Amount must be greater than zero");
    }

    return {
      passed: violations.length === 0,
      violations
    };
  }
})
```

A task describes what success looks like.
A capability defines the workflow domain used to attempt it.

`context` provides structured input for execution.
`verify` defines the deterministic acceptance boundary for the run.

### Context

`context` provides structured, capability-specific input such as:

- entity identifiers
- environment details
- known task parameters
- session or runtime handles

Examples:

```typescript
context: {
  portal: "stripe",
  accountId: "acct_123",
  invoiceId: "inv_1024"
}
```

`context` is not a prompt extension and should not contain step-by-step instructions.

Good context narrows the environment.
It does not tell the agent how to think.

### Capabilities

Capabilities represent operator-level actions rather than low-level tools.

Examples:

- `automation.extract_invoice`
- `automation.submit_form`
- `automation.capture_session`
- `automation.retrieve_policy_quote`

Capabilities represent bounded workflows, not single browser actions. A capability may involve multiple navigation steps, state transitions, and runtime decisions before producing a valid result.

### Deterministic Verification

ZCA agents evaluate outcomes using deterministic verification rules.

Examples:

- required output fields
- schema validation
- invariants
- expected system state

This ensures probabilistic execution still produces verifiable results.

In practice, a capability may define default verification rules, while each execution can add stricter run-specific checks.

### Execution Runtime

ZCA agents delegate environment interaction to a runtime such as:

- browser automation runtimes
- API execution systems
- integration layers

In this repository, the runtime is typically BrowserAgent.

### Traces

Every execution produces structured traces such as:

- execution attempts
- capability selection
- environment responses
- verification outcomes

Traces make it easier to understand what happened and why a run passed or failed.

## Relationship to BrowserAgent

ZCA agents operate above the browser execution runtime.

```text
Operator system
↓
ZCA agent
↓
BrowserAgent
↓
Website environment
```

BrowserAgent performs browser interaction.
ZCA agents provide task-level structure, verification, and traceable execution boundaries around that runtime.

## Design Philosophy

ZCA agents prioritize:

- explicit tasks
- structured input
- deterministic verification
- observable execution
- separation between decision and environment interaction

This makes agent systems more reliable in dynamic real-world environments such as:

- browser automation
- operational workflows
- business process automation
- system integrations

## Learn More

Zero Context Architecture:

[https://www.to2d.xyz/architecture/zero-context-architecture/](https://www.to2d.xyz/architecture/zero-context-architecture/)