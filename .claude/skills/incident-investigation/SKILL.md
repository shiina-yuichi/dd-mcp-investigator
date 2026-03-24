---
name: incident-investigation
description: Investigate incidents using Datadog MCP tools through an iterative Observe-Hypothesize-Act loop, and generate an interactive HTML report
---

# Incident Investigation Skill

Investigate incidents using the Datadog MCP Server. Identify root causes through an iterative Observe → Hypothesize → Act loop and visualize the entire investigation process as an interactive HTML report.

**Invocation**: `/incident-investigation`

---

## Core Principle: Observe → Hypothesize → Act Loop

This skill operates using the following investigation loop:

```
┌─────────────────────────────────────────────────┐
│  OBSERVE                                         │
│  Collect telemetry data and identify anomalies   │
│                                                   │
│          ↓                                       │
│                                                   │
│  HYPOTHESIZE                                     │
│  Form hypotheses about potential root causes     │
│                                                   │
│          ↓                                       │
│                                                   │
│  ACT                                             │
│  Query tools to validate or invalidate hypotheses│
│                                                   │
│          ↓                                       │
│                                                   │
│  UPDATE                                          │
│  Refine understanding based on new evidence      │
│  → Chain additional investigation steps          │
│  → Repeat until converging on root cause         │
│                                                   │
│          ↓ (converged or insufficient data)      │
│                                                   │
│  CONCLUDE                                        │
│  Present evidence-based conclusion               │
│  OR mark as inconclusive if data is insufficient │
└─────────────────────────────────────────────────┘
```

**Key**: Each step builds on previous findings. Do NOT enumerate all hypotheses upfront — adapt and course-correct as new evidence emerges.

---

## Investigation Workflow

### Step 1: Source Identification

Identify the investigation source using Datadog MCP tools:

- **Monitor alert**: Retrieve details with `search_datadog_monitors`
- **Incident**: Get details via `search_datadog_incidents` / `get_datadog_incident`
- **Host issue**: Get host info with `search_datadog_hosts`
- **User report**: Identify target services/resources from the report

```
Investigation record format:
- source_type: "MONITOR ALERT" | "INCIDENT" | "USER REPORT"
- source_title: Alert title / report description
- source_id: Monitor ID / Incident ID (if applicable)
- affected_resources: Affected resources (hostname, service name, etc.)
- time_range: Time range of the issue
```

### Step 2: Initial Observation

Collect telemetry data broadly based on source information. **Record the absence of data as an important clue.**

#### Core Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Metrics | `get_datadog_metric` | Query time-series data (CPU, memory, disk, network, etc.) |
| Metric context | `get_datadog_metric_context` | Get metric metadata, available tags, and tag values |
| Metric search | `search_datadog_metrics` | List and filter available metrics |
| Log search | `search_datadog_logs` | Search logs by time, query, and service |
| Log analysis | `analyze_datadog_logs` | SQL-based log aggregation and statistical analysis |
| Hosts | `search_datadog_hosts` | Search and filter monitored hosts |
| Spans | `search_datadog_spans` | Search and filter APM spans |
| Trace details | `get_datadog_trace` | Retrieve complete traces by trace ID |
| Services | `search_datadog_services` | Search services in the Software Catalog |
| Service deps | `search_datadog_service_dependencies` | Visualize upstream/downstream service relationships |
| Events | `search_datadog_events` | Search deploys, config changes, security findings, etc. |
| Monitor search | `search_datadog_monitors` | Search monitors by status, thresholds, alert conditions |
| Monitor groups | `search_datadog_monitor_groups` | Search and filter monitor groups |
| Incident search | `search_datadog_incidents` | Search incidents by state, severity, metadata |
| Incident details | `get_datadog_incident` | Retrieve incident details and metadata |
| Dashboards | `search_datadog_dashboards` | Search related dashboards |
| Notebooks | `search_datadog_notebooks` / `get_datadog_notebook` | Search and retrieve notebooks |
| RUM | `search_datadog_rum_events` | Search RUM events |

#### APM Tools (Deep Analysis)

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Span search | `apm_search_spans` | Search spans using APM query syntax |
| Trace exploration | `apm_explore_trace` | Execute deep analysis queries on trace data |
| Trace summary | `apm_trace_summary` | Generate AI-powered trace overviews |
| Trace comparison | `apm_trace_comparison` | Compare traces to identify performance differences |
| APM metrics | `apm_analyze_trace_metrics` | Analyze APM metrics over time |
| Span tag discovery | `apm_discover_span_tags` | Discover available tag keys on spans |
| Watchdog | `apm_search_watchdog_stories` / `apm_get_watchdog_story` | Search and retrieve Watchdog anomaly detection insights |
| Change detection | `apm_search_change_stories` | Detect deployment and infrastructure changes |
| Latency analysis | `apm_latency_bottleneck_analysis` | Analyze latency contributors across traces |
| Latency tag analysis | `apm_latency_tag_analysis` | Compare span tags between anomaly and baseline periods |
| APM recommendations | `apm_search_recommendations` / `apm_get_recommendation` | APM optimization suggestions |
| Investigation guide | `apm_investigation_methodology` | Debugging guidance for APM issues |
| Primary tags | `apm_get_primary_tag_keys` | Retrieve organization's primary tag keys |

#### Alerting Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Monitor validation | `validate_datadog_monitor` | Validate monitor definitions |
| Monitor templates | `get_datadog_monitor_templates` | Retrieve monitor creation templates |

#### Database Monitoring (DBM) Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Query plans | `search_datadog_dbm_plans` | Search query execution plans for performance analysis |
| Query samples | `search_datadog_dbm_samples` | Search individual query execution performance metrics |

#### Error Tracking Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Error search | `search_datadog_error_tracking_issues` | Search Error Tracking Issues across data sources |
| Error details | `get_datadog_error_tracking_issue` | Retrieve detailed Error Tracking Issue information |

#### Network Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Network analysis | `analyze_cloud_network_monitoring` | Investigate network-level issues and anomalies |
| Network devices | `search_ndm_devices` / `get_ndm_device` | Search and retrieve monitored network devices |
| Interfaces | `search_ndm_interfaces` | Retrieve network interfaces for devices |

#### Security Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Secrets scan | `datadog_secrets_scan` | Scan code for hardcoded secrets |
| Security signals | `search_datadog_security_signals` | Search Cloud SIEM and threat protection signals |
| Security findings | `search_security_findings` / `analyze_security_findings` | Search and analyze security findings |
| Findings schema | `security_findings_schema` | Get available security findings fields |

#### Cases Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Case search | `search_datadog_cases` / `get_datadog_case` | Search and retrieve Case Management cases |
| Case create/update | `create_datadog_case` / `update_datadog_case` | Create and update cases |
| Add comment | `add_comment_to_datadog_case` | Add markdown comments to cases |
| Jira link | `link_jira_issue_to_datadog_case` | Associate Jira tickets with cases |
| Projects | `list_datadog_case_projects` / `get_datadog_case_project` | List and retrieve case projects |
| User search | `search_datadog_users` | Search Datadog users |

#### Software Delivery Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| CI pipelines | `search_datadog_ci_pipeline_events` / `aggregate_datadog_ci_pipeline_events` | Search and aggregate CI pipeline events |
| Tests | `search_datadog_test_events` / `aggregate_datadog_test_events` | Search and aggregate test events |
| Flaky tests | `get_datadog_flaky_tests` | Search flaky tests with triage details |
| Code coverage | `get_datadog_code_coverage_branch_summary` / `get_datadog_code_coverage_commit_summary` | Get coverage metrics by branch or commit |

#### Feature Flags Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Flag list/details | `list_datadog_feature_flags` / `get_datadog_feature_flag` | List and retrieve feature flags |
| Flag create | `create_datadog_feature_flag` | Create new feature flags |
| Environments | `list_datadog_feature_flag_environments` / `update_datadog_feature_flag_environment` | List and update flag environments |
| Allocations | `list_datadog_feature_flag_allocations` / `sync_datadog_feature_flag_allocations` | List and sync allocation rules |
| Implementation check | `check_datadog_flag_implementation` | Verify flag implementation in codebases |

#### Synthetics Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Test search | `get_synthetics_tests` | Search Synthetic tests |
| Test edit | `edit_synthetics_tests` | Edit HTTP API Synthetic tests |
| Test create | `synthetics_test_wizard` | Create HTTP API Synthetic tests via wizard |

#### Workflows Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Workflow search | `list_datadog_workflows` / `get_datadog_workflow` | List and retrieve workflows |
| Workflow execute | `execute_datadog_workflow` / `get_datadog_workflow_instance` | Execute workflows and check status |
| Trigger setup | `update_datadog_workflow_with_agent_trigger` | Add agent triggers to workflows |

#### Onboarding Tools

| Target | MCP Tool | Purpose |
|--------|----------|---------|
| Onboarding | `browser_onboarding` / `devices_onboarding` / `kubernetes_onboarding` / `llm_observability_onboarding` / `test_optimization_onboarding` / `serverless_onboarding` / `source_map_uploads` | Guided onboarding for various services |

**Search tips**:
- If the first search returns 0 results, **expand the time range** (1h → 4h → 24h)
- When endpoint or resource names are unknown, **use wildcard searches or custom attribute searches**
- **Child spans (middleware) often contain error details and custom attributes** that parent spans lack

**Comprehensive log search (required)**:
If `host:xxx` or `service:xxx` returns 0 logs, **execute the following escalation before concluding "no logs exist"**. Logs forwarded via CloudWatch Logs Forwarder set the log group name (e.g., `/ssm/session-log`) in the `host` field, while EC2 instance IDs are stored in custom attributes (e.g., `target.id`).

```
Log search escalation (try top-to-bottom when 0 results):
1. host:<hostname>
2. source:cloudwatch OR source:aws
3. source:cloudtrail <hostname_or_instance_id>
4. <instance_id> (free text search)
5. @target.id:<instance_id>
6. source:* <service_or_host_keyword>
7. Repeat with Flex Logs storage (storage_tier: flex_and_indexes)
```

**Important**: Zero log results may mean "the query didn't match" rather than "no logs exist." Always complete the escalation steps before judging log availability.

### Step 3: Iterative Hypothesis Loop

Generate hypotheses from initial observations and validate them. Repeat until converging on the root cause.

#### Loop exit conditions

Exit the loop and proceed to Step 4 when any of the following is met:

1. **A hypothesis is Validated with a sufficient evidence chain** — consistent evidence supports the root cause
2. **All hypotheses are Invalidated with no leads for new hypotheses** — no new hypotheses can be derived from available data
3. **All relevant MCP tools have been queried with no additional meaningful data** — metrics, logs, spans, events, etc. have been exhaustively queried

#### Loop flow

```
Generate hypotheses from observations
    ↓
Validate the most likely hypothesis using tools
    ↓
Evaluate results → Assign hypothesis status
    ↓
┌─ Validated → Need deeper investigation?
│     ├─ YES → Generate sub-hypotheses and continue loop
│     └─ NO  → Proceed to conclusion
├─ Inconclusive → Try additional data collection / alternative approach
└─ Invalidated → Move to next hypothesis / generate new hypotheses
    ↓
Update understanding based on new evidence → Return to loop start
```

#### Hypothesis status definitions

| Status | Meaning | Criteria |
|--------|---------|----------|
| **Validated** | Confirmed by data | Multiple pieces of evidence consistently support the hypothesis |
| **Inconclusive** | Insufficient evidence | Data is lacking or contradictory evidence exists |
| **Invalidated** | Contradicted by data | Evidence contradicts the hypothesis |

#### Adaptation and course-correction principles

- **Revise hypotheses when unexpected data appears**: Do not cling to initial hypotheses
- **Add new hypotheses when new anomalies are discovered**: Dynamically expand investigation scope
- **Treat absence of data as evidence**: 0 logs = possible instrumentation gap
- **Prioritize temporal correlation**: Cross-reference anomaly timestamps with event timelines

### Step 4: Conclusion

After the loop converges, produce one of the following conclusions:

#### Pattern A: Root cause identified

When sufficient evidence clearly identifies the root cause:

```
conclusion:
  status: "root_cause_identified"
  root_cause: "Description of the root cause"
  confidence: "high" | "medium"
  evidence_chain:
    - "Evidence 1: ..."
    - "Evidence 2: ..."
  recommended_actions:
    - "Recommended action 1"
    - "Recommended action 2"
```

#### Pattern B: Inconclusive

When available data is insufficient to support a definitive conclusion:

```
conclusion:
  status: "inconclusive"
  reason: "Why a conclusion could not be reached"
  findings_so_far:
    - "Established fact 1"
    - "Established fact 2"
  missing_data:
    - "Missing data 1"
    - "Missing data 2"
  next_steps:
    - "Required follow-up action 1"
    - "Required follow-up action 2"
```

**Important**: Do not force a conclusion when evidence is insufficient. "Inconclusive" is a legitimate investigation outcome — its value lies in clearly stating what data is missing.

---

## HTML Investigation Report

After investigation completes, generate an interactive HTML report.
The template is located at `templates/investigation-report.html`.

### Generation steps

1. Convert investigation results into an `INVESTIGATION_DATA` JSON object
2. Replace the `INVESTIGATION_DATA` in the template HTML with the actual data
3. Save as `investigation-{incident_id}-{date}.html`

### INVESTIGATION_DATA structure

```javascript
const INVESTIGATION_DATA = {
  source: {
    type: "MONITOR ALERT",        // "MONITOR ALERT" | "INCIDENT" | "USER REPORT"
    title: "Alert title"
  },
  initial_observation: {
    steps: 6,                      // Number of initial observation steps
    duration: "1 minute"           // Time spent on investigation
  },
  conclusion: {
    status: "root_cause_identified", // "root_cause_identified" | "inconclusive"
    title: "Root cause description",
    points: [                      // Conclusion points
      "Point 1",
      "Point 2"
    ]
  },
  hypotheses: [                    // Array of main hypotheses
    {
      id: "H1",
      title: "Hypothesis title",
      description: "Detailed description",
      status: "validated",          // "validated" | "inconclusive" | "invalidated"
      evidence: [                   // Evidence list
        { text: "Evidence text (HTML allowed)", positive: true }  // positive: true=supports, false=contradicts, null=neutral
      ],
      research_steps: [             // Research steps
        { text: "What was investigated", tool: "MCP tool name used" }
      ],
      assessment: "Assessment text", // Overall assessment of the hypothesis
      sub_hypotheses: [             // Sub-hypotheses (same structure)
        { id: "H1-1", title: "...", description: "...", status: "invalidated", evidence: [...], research_steps: [...], assessment: "..." }
      ]
    }
  ]
};
```

### UI structure

- **Swiss Clean light theme**: White background (#FFFFFF) + red accent (#E42313) geometric design
- **Fixed sidebar + scrolling main 2-column layout**:
  - **Header**: SOURCE card + initial observation pill pinned at top
  - **Left sidebar (sticky)**: Investigation Flow tree always visible. Auto-highlights current section via IntersectionObserver. Click nodes to smooth-scroll to detail section. Includes conclusion node.
  - **Right main area (scrollable)**: Hypothesis detail cards fully expanded vertically with conclusion card at the bottom. Tabs switch between Evidence / Research Steps / Assessment.
  - **Mobile (≤768px)**: Sidebar hidden, falls back to vertical flow
- **Sidebar tree**: Parent-child relationships with indent + connecting lines. Status dots (Red=Validated, Amber=Inconclusive, Gray=Invalidated). Left border highlight on current section.
- **Hypothesis cards**: Independent sections with left status bar. Tabs for Evidence / Research Steps / Assessment. Sub-hypotheses nested inside parent cards.
- **Typography**: Space Grotesk (headings/labels) + Inter (body) dual font
- **Color palette**: Red (#E42313) / Green (#22C55E) / Text (#0D0D0D) / Secondary (#7A7A7A) / Border (#E8E8E8)
- **No border-radius**: Sharp corners for Swiss design precision
- **Accessibility**: prefers-reduced-motion support

### evidence positive flag

| Value | Meaning | Icon |
|-------|---------|------|
| `true` | Evidence supporting the hypothesis | Green checkmark |
| `false` | Evidence contradicting the hypothesis | Red X |
| `null` | Neutral / reference information | Amber minus |

---

## Output Format

During investigation, output text-based reports in conversation. After completion, generate an HTML file.

### Conversation output

During each loop iteration, report progress:

```markdown
### Loop N: [Loop purpose]
| Step | Observation/Action | Tool | Finding |
|------|-------------------|------|---------|
| 1 | ... | ... | ... |

**→ Hypothesis update**: H1: Validated, H2: Invalidated ...
```

### File output

After investigation completes, generate an HTML report:

- **Filename**: `investigation-{incident_id}-{date}.html`
- **Location**: Working directory
- **Contents**: Template HTML with `INVESTIGATION_DATA` populated with investigation results
- **View**: Open directly in a browser (`open investigation-xxx.html`)

---

## Important Notes

- **Execute the iterative loop faithfully**: Do NOT enumerate all hypotheses upfront — decide next actions based on each step's results
- **Record the absence of data**: 0 logs, no spans = report as possible instrumentation gap
- **Do not force conclusions**: If data is insufficient, mark as "inconclusive" and clearly state what is missing and what next steps are needed
- **Expand time ranges progressively**: 1h → 4h → 24h to search for evidence
- **Always check child spans**: Parent spans often lack error details — child spans (middleware) frequently contain custom attributes and error messages
- **Generate HTML report from template**: Replace `INVESTIGATION_DATA` in `templates/investigation-report.html` with investigation results
