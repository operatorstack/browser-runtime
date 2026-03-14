# BrowserRuntime

A browser automation execution runtime for agent systems.

BrowserRuntime connects reasoning systems to real browser environments and exposes structured capabilities for interaction, observation, and execution. This repository is the execution layer: it owns browser control, page interaction, DOM-aware tooling, runtime configuration, and execution primitives.

Higher-level goal orchestration, verification loops, workflow state, and policy decisions belong in the operator runtime above it.

## Architecture Positioning

```text
Operator Runtime
(control layer)

↓ goals / verification / tracing

BrowserRuntime
(execution runtime)

↓ actions

Browser Environment
(websites / real-world systems)
```

## What This Repository Focuses On

- Browser session control
- Page interaction
- DOM inspection
- Environment observation
- Execution runtime behavior
- Playwright-backed automation primitives
- Tool and capability extension points

## What It Does Not Try To Be

This repository should not be read as the top-level agent control plane. It provides the browser-facing runtime that higher-level systems can call into. Planning, orchestration, goal management, and verification are intentionally treated as adjacent concerns.

## Current Compatibility

This is a documentation and positioning rebrand only.

- Public APIs remain unchanged
- Folder structure remains unchanged
- Runtime classes and exports remain unchanged

The runtime is still published under its existing package identity until a future explicit package rename happens.

## Installation

Install the currently published package together with Playwright:

```bash
npm install <published-package-name> playwright @playwright/test
```

## Quick Start

```typescript
import { chromium } from "playwright";
import { ComputerUseAgent } from "<published-package-name>";

const browser = await chromium.launch({ headless: false });
const page = await browser.newPage();

await page.goto("https://news.ycombinator.com/");

const agent = new ComputerUseAgent({
  apiKey: process.env.ANTHROPIC_API_KEY!,
  page,
});

const answer = await agent.execute("Tell me the title of the top story");
console.log(answer);

await browser.close();
```

## Runtime Capabilities

The existing runtime already exposes the low-level building blocks needed by higher-level operator systems, including:

- Browser automation through Playwright
- Structured execution through `ComputerUseAgent`
- Capability registration and tool extension
- Execution configuration for typing, screenshots, scrolling, and timing
- Browser-context access for custom tools and delegated execution
- Pause, resume, and cancel control surfaces for long-running runs

## Design Direction

BrowserRuntime is intended to be the browser execution substrate that other systems build on top of. In practical terms, that means:

- Keeping execution primitives composable
- Preserving stable runtime APIs
- Supporting operator-driven orchestration above the runtime boundary
- Separating environment execution concerns from decision-making concerns

## License

See `LICENSE`.
