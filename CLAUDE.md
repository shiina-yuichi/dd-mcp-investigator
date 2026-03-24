# dd-mcp-investigator

Incident investigation helper using the Datadog MCP Server.

## Skills

### incident-investigation

Identify root causes through an iterative Observe → Hypothesize → Act loop and generate an interactive HTML report.

**Usage**: `/incident-investigation` or ask for an incident investigation

**Investigation loop** (repeat until convergence):
1. **Observe** — Collect telemetry data and identify anomalies
2. **Hypothesize** — Generate hypotheses from observations
3. **Act** — Validate/invalidate hypotheses using tools
4. **Update** — Refine understanding based on new evidence
5. **Conclude** — Evidence-based conclusion or mark as inconclusive

**Output**: Interactive HTML report (Investigation Flow sidebar + hypothesis detail cards with tabs + conclusion card)

**Report sections**:
- SOURCE card (alert / incident info)
- Initial observation pill (step count)
- Investigation Flow (hypothesis tree navigation — click to scroll to details)
- Hypothesis details (Evidence / Research Steps / Assessment tabs — sub-hypotheses nested)
- Conclusion card (root cause identified or inconclusive)

## Available Datadog MCP Tools

Key tools used during investigation:

- `search_datadog_monitors` / `search_datadog_monitor_groups` — Monitor search
- `search_datadog_incidents` / `get_datadog_incident` — Incident info
- `get_datadog_metric` / `get_datadog_metric_context` — Metrics
- `search_datadog_logs` / `analyze_datadog_logs` — Log search & analysis
- `search_datadog_hosts` — Host info
- `search_datadog_spans` / `get_datadog_trace` — Trace analysis
- `search_datadog_services` / `search_datadog_service_dependencies` — Service dependencies
- `search_datadog_events` — Event search
- `apm_*` — APM deep analysis (Watchdog, latency, trace comparison, etc.)
- `search_datadog_dbm_*` — Database Monitoring
- `search_datadog_error_tracking_*` — Error Tracking
- `analyze_cloud_network_monitoring` / `search_ndm_*` — Network analysis
- `search_datadog_security_*` / `analyze_security_findings` — Security
- `search_datadog_cases` / `create_datadog_case` — Case Management
- `search_datadog_ci_pipeline_events` / `search_datadog_test_events` — Software Delivery
- `list_datadog_feature_flags` — Feature Flags
- `get_synthetics_tests` — Synthetics
- `list_datadog_workflows` / `execute_datadog_workflow` — Workflows
