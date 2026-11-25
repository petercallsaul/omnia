---
name: fullstack
description: >-
  Use when the user types /fullstack, asks for fullstack orchestration, or wants to develop a project by automatically detecting, filtering, and invoking every applicable installed skill and plugin while excluding irrelevant ones.
---

# Fullstack Orchestration Skill (`/fullstack`)

## Overview

The `fullstack` skill is an intelligent meta-orchestrator. When invoked with a single command or prompt (`/fullstack [task/project description]`), it performs a comprehensive audit of the project, evaluates all installed Antigravity plugins and skills, filters out irrelevant ones, and applies every single applicable skill across each stage of the development lifecycle.

---

## The Workflow

```text
/fullstack [command/prompt]
   │
   ├── 1. Project Detection (Framework, Language, Existing Code, Intent)
   │
   ├── 2. Skill Audit & Exclusion (Filter out non-relevant tools)
   │
   ├── 3. Lifecycle Relevance Matrix (Categorize applicable skills)
   │
   └── 4. Coordinated Execution (Invoke applicable skills in sequence/subagents)
```

---

## Step 1: Project Detection

Inspect the current workspace (or project description):
- **Web App**: React, Next.js, Vue, Svelte, Vite, HTML/CSS.
- **Backend / API**: Node.js, Express, Python (FastAPI/Django), Go, Firebase, Cloudflare Workers.
- **Mobile**: Flutter, Android (Kotlin/Java), React Native.
- **AI Agent / Automation**: LLM pipelines, autonomous agents, RAG, tool calling.
- **Greenfield**: Empty workspace or new project request.

---

## Step 2: Skill Audit & Exclusion Filter

Review all installed plugins and skills against the project's domain. **Strictly exclude** skills that do not relate to the project:

| Category | Skill / Plugin | Exclusion Condition |
| :--- | :--- | :--- |
| **Mobile** | `android-cli`, `compose-preview` | Exclude if the project is NOT an Android/mobile application. |
| **Design / Media** | `canva` | Exclude if the task is strictly software/code with no marketing/presentation needs. |
| **Science / Bio** | `alphafold`, `chembl`, `ncbi`, `pdb`, `pymol` | Exclude if the project is NOT computational biology, chemistry, or bioinformatics. |
| **Geolocation** | `google_maps_platform` | Exclude if the app has no mapping, routing, or location requirements. |
| **Enterprise Cloud**| `harness` | Exclude if deploying to simple hosts (Vercel/Netlify) without enterprise Harness pipelines. |

---

## Step 3: Applicable Skills Lifecycle Matrix

Map the remaining applicable skills to the standard 6-stage lifecycle:

### Stage 1: Specification & Documentation
- **`context7-mcp`**: Query live official documentation and reference for all libraries and frameworks used.
- **`figma`** (`figma-design-to-code`, `figma-code-connect`): Translate Figma specs and designs into code if design files/URLs are present.

### Stage 2: Architecture & Foundation
- **`firebase`** (`firebase-basics`, `firebase-auth-basics`, `firebase-firestore`): Configure Firebase Auth, Firestore, and backend rules if Firebase is used.
- **`auth0`**: Set up authentication, user pools, and SSO if Auth0 is chosen.
- **`base44`**: Full-stack app scaffolding, database, and workflow runs.

### Stage 3: AI & Intelligence (if applicable)
- **`gemini-api`** / **`ai-sdk`**: Implement multimodal LLM capabilities, function calling, and structured output.
- **`google-antigravity-sdk`**: Design autonomous background agents and multi-agent coordination.

### Stage 4: Implementation & Test-Driven Development
- **`superpowers:test-driven-development`**: Write failing tests before implementation.
- **`superpowers:systematic-debugging`**: Investigate bugs with evidence and root-cause tracing.
- **`chrome-devtools-plugin`**: Browser automation, DOM inspection, accessibility (`a11y-debugging`), and network analysis.
- **`browse`** (Browserbase): Headless cloud browsing for integration verification.

### Stage 5: Code Quality, Review & Security
- **`coderabbit`** (`code-review`, `autofix`): Run AI code review to detect bugs, code smells, and security vulnerabilities before completion.
- **`crowdstrike`** (`falcon-mcp`): Host security, CVE assessment, and endpoint risk posture (if tenant configured).
- **`dynatrace`**: Runtime telemetry, traces, and performance health (if APM configured).

### Stage 6: Deployment & CI/CD
- **`vercel-plugin`** / **`netlify`**: Deploy the application, manage environment variables, and configure preview URLs.
- **`superpowers:verification-before-completion`**: Confirm test suite passes and application runs end-to-end.

---

## Step 4: Execution Protocol

Whenever `/fullstack` is invoked:

1. **Print the Orchestration Plan**:
   - Show the detected stack.
   - List the **Excluded Skills** (and why).
   - List the **Active Applicable Skills** (and their assigned stage).
2. **Execute Phase-by-Phase**:
   - Use each applicable skill in its respective stage.
   - For services without credentials (e.g. Falcon, Dynatrace), note their optional status or provide configuration steps without blocking the rest of the development.
3. **Verify & Deliver**:
   - Run verification commands.
   - Provide the complete, working solution to the user.
