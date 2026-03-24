# dd-mcp-investigator

[日本語版はこちら](README.ja.md)

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for incident investigation using the [Datadog MCP Server](https://docs.datadoghq.com/bits_ai/mcp_server/). It identifies root causes through an iterative Observe → Hypothesize → Act loop and generates an interactive HTML report.

> **Note**: This is a personal project and is not affiliated with Datadog, Inc.

## Features

- Systematic root cause analysis using Datadog MCP tools (88 tools across 13 categories)
- Iterative hypothesis generation and validation loop
- Interactive HTML report with investigation flow sidebar and tabbed detail cards
- Hypothesis status visualization (Validated / Inconclusive / Invalidated)

## Prerequisites

### 1. Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Datadog MCP Server

The Datadog MCP Server must be configured in Claude Code. See [Datadog MCP Server Setup](https://docs.datadoghq.com/bits_ai/mcp_server/setup/) for details.

```bash
# Verify connection inside Claude Code
/mcp
```

## Installation

### Option 1: Run Claude Code in this directory (recommended)

```bash
cd dd-mcp-investigator
claude
```

Skills in `.claude/skills/` are auto-detected. Invoke with `/incident-investigation`.

### Option 2: Copy the skill into an existing project

```bash
cp -r /path/to/dd-mcp-investigator/.claude/skills/incident-investigation \
      /your/project/.claude/skills/incident-investigation
```

### Option 3: Paste SKILL.md content into CLAUDE.md

For lightweight usage, copy the contents of `.claude/skills/incident-investigation/SKILL.md` directly into your project's `CLAUDE.md`.

## Usage

### Invoke the skill

```
/incident-investigation
```

Or use natural language:

```
Investigate the monitor "CPU Usage High on host:example-host"
What caused INC-12345?
Analyze the latency spike on payment-service
```

### Investigation flow

```
1. SOURCE IDENTIFICATION
   └─ Retrieve monitor / incident / host details

2. INITIAL OBSERVATION
   └─ Collect metrics, logs, traces, hosts, events, etc.

3. HYPOTHESIS INVESTIGATION (iterative loop)
   ├─ Generate hypotheses from observations
   ├─ Validate each hypothesis with Datadog data
   ├─ Assign status (Validated / Inconclusive / Invalidated)
   └─ Drill down with sub-hypotheses

4. CONCLUSION
   └─ Root cause identification + recommended actions

5. HTML REPORT
   └─ Generate interactive HTML report
```

### HTML report structure

| Section | Description |
|---------|-------------|
| **Source card** | Alert / incident summary with red accent border |
| **Initial observation pill** | Number of initial observation steps |
| **Investigation Flow** | Sidebar tree of hypotheses with parent-child relationships. Click to scroll to detail. |
| **Hypothesis details** | Tabbed cards (Evidence / Research Steps / Assessment). Sub-hypotheses nested inside. |
| **Conclusion card** | Root cause or inconclusive finding with action items |

### Status legend

| Status | Color | Meaning |
|--------|-------|---------|
| Validated | Red (#E42313) | Hypothesis confirmed by data |
| Inconclusive | Amber (#D97706) | Insufficient evidence to confirm or deny |
| Invalidated | Gray (#B0B0B0) | Hypothesis contradicted by data |

## File structure

```
dd-mcp-investigator/
├── .claude/
│   └── skills/
│       └── incident-investigation/
│           ├── SKILL.md                        # Skill definition (auto-detected by Claude Code)
│           └── templates/
│               └── investigation-report.html   # HTML report template
├── CLAUDE.md                                   # Project config
└── README.md
```

## License

MIT
