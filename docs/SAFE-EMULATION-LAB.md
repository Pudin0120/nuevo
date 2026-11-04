# NUEVO — safe adversary-emulation lab

This branch adds a defensive, synthetic-only lab for validating endpoint telemetry and detections against behaviors often associated with information stealers. It is **not** LummaC2, a malware emulator, or a payload execution framework.

## Scenarios

| Scenario | Safe signal model | Rule |
|---|---|---|
| browser-credential-store-canary | Browser credential-store canary event; no database read or decryption | NUEVO-DET-007 |
| browser-session-store-canary | Session-store canary and process metadata; no cookie values | NUEVO-DET-014 |
| session-reuse-anomaly | Non-secret session pseudonym reused across device bindings | NUEVO-DET-008 |
| clickfix-user-training | Browser context, clipboard-change occurrence, process lineage; no clipboard text or script | NUEVO-DET-009 |
| process-hollowing-integrity | Process image-tamper metadata; no process modification | NUEVO-DET-010 |
| sandbox-probe-gating | Abstract environment/activity probes and a synthetic gate event; no mouse input or evasion | NUEVO-DET-011 |
| amsi-etw-integrity | Independent instrumentation integrity and provider-health signals; no patching | NUEVO-DET-012 |
| dns-churn-egress | Reserved .invalid markers and destination churn; no DNS/network request | NUEVO-DET-013 |
| syscall-provenance-anomaly | Independent EDR provenance mismatch metadata; no syscalls | NUEVO-DET-015 |
| memory-backed-execution-provenance | Executable-region/image-backing metadata; no memory allocation, write or dump | NUEVO-DET-016 |
| dns-tunnel-pattern | High-entropy/TXT metadata on reserved .invalid names; no DNS query | NUEVO-DET-017 |

## Defensive validation

- Event replay is deterministic and entirely in memory.
- Every synthetic event is chained and verifiable.
- Detection output includes evidence event IDs, rationale and confidence.
- Ground-truth validation compares expected/triggered rule IDs.
- The benign-admin scenario expects zero detections to catch false-positive regressions.
- Ablation reports which detections disappear when an event kind is removed.
- Mitigation recommendations are human-reviewed and never auto-applied.
- External Windows logs are imported offline using allowlisted metadata; sensitive paths are reduced to resource classes.
- OTLP/HTTP JSON can be written to stdout or a file; NUEVO itself does not transmit it.

## Run

```bash
go test -race ./...
go vet ./...
go run ./cmd/nuevo-lab --list
go run ./cmd/nuevo-lab --scenario clickfix-user-training --run-id clickfix-demo-01
go run ./cmd/nuevo-lab --scenario memory-backed-execution-provenance --run-id memory-demo-01
go run ./cmd/nuevo-lab --scenario dns-tunnel-pattern --run-id dns-demo-01 --otel-json > events.otlp.json
```

For the full source archive, rebuild instructions, ATT&CK mapping, Sysmon/Windows Security import schema, and baseline recommendations, see `artifacts/` and the source bundle linked in the PR description.

## Safety exclusions

No credential/cookie extraction, DPAPI or browser-secret decryption, clipboard reading, payload creation, execution, process injection/hollowing, AMSI/ETW patching, direct/indirect syscall execution, sandbox-evasion code, persistence installation, C2, or exfiltration is implemented. The simulator never changes endpoint settings.
