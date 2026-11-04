# NUEVO — endpoint evidence ledger and safe adversary-emulation lab

NUEVO is a defensive endpoint telemetry and validation platform. This feature branch adds a synthetic-only adversary-emulation lab inspired by Lumma-like infostealer behaviors and mapped to MITRE ATT&CK. It is not LummaC2 and does not run attack payloads.

## Lab capabilities

- Browser credential and session-store canaries; no actual browser database access, token/cookie collection, or DPAPI decryption.
- Session/device-binding reuse anomalies using non-secret pseudonymous fixtures.
- ClickFix / fake-CAPTCHA training telemetry: clipboard-change occurrence and process lineage only; no clipboard contents, encoded commands, or script execution.
- Process-image tampering telemetry and sandbox-probe/gating signals; no process manipulation, UI surveillance, or anti-sandbox evasion.
- AMSI/ETW instrumentation integrity and independent sensor-health metadata; no patching or disabling telemetry.
- Syscall provenance mismatch and executable-memory/image-backing mismatch signals; no syscall execution or memory reads/writes/dumps.
- DNS churn and high-entropy/TXT patterns using reserved `.invalid` markers; no DNS or socket operations.
- Correlated detections with evidence references, explanation, confidence and human-reviewed mitigation recommendations.
- Offline Sysmon / Windows Security 4663 metadata import with path reduction, ground-truth validation, benign false-positive control, event-kind ablation, and local OTLP JSON export.

## Run locally

```bash
go test -race ./...
go vet ./...
go run ./cmd/nuevo-lab --list
go run ./cmd/nuevo-lab --scenario clickfix-user-training --run-id clickfix-demo-01
go run ./cmd/nuevo-lab --scenario memory-backed-execution-provenance --run-id memory-demo-01
go run ./cmd/nuevo-lab --scenario dns-tunnel-pattern --run-id dns-demo-01 --otel-json > events.otlp.json
```

See [the safe emulation lab](docs/SAFE-EMULATION-LAB.md). `configs/asr-baseline.json` is recommendation-only; the PowerShell posture collector is read-only. No endpoint settings are changed automatically.

## Safety boundaries

No credential/cookie extraction, DPAPI/browser-secret decryption, clipboard reading, payload execution, process injection/hollowing, AMSI/ETW patching, direct/indirect syscall implementation, sandbox-evasion code, persistence installation, C2, exfiltration or automated remediation is included. High-risk behaviors are represented as synthetic evidence metadata, not performed.

The source archive is stored as independently base64-encoded chunks under `artifacts/`. The workflow `.github/workflows/verify-source-snapshot.yml` reconstructs and checks its gzip/tar integrity on branch updates.
