# Local TCP Port State Experiment

## Objective

Observe how Nmap reports a TCP port while a local service is listening, then compare the result after the service is stopped.

## Scope and safety

- Target: `127.0.0.1` (the same computer)
- Port: `8000/tcp`
- This exercise is limited to the local machine.

## Setup

In the first terminal, start a temporary HTTP server bound to loopback:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

Leave it running during the first scan.

## Scan

In a second terminal, scan only port 8000 and ask Nmap to explain its state:

```bash
nmap -sT -p 8000 --reason 127.0.0.1
```

Stop the HTTP server with `Ctrl+C`, then run the same scan again.

## Expected interpretation

| Test condition | Expected state | Why |
| --- | --- | --- |
| HTTP server is running | `open` | A TCP connection to the listening service can be established. |
| HTTP server is stopped | `closed` | The host responds that no service is accepting connections on that port. |

The exact service label and reason text can vary by Nmap version and local system. Record the output you actually observe if you want to include exact evidence.

## What this demonstrates

A port state describes what the scan could infer at the time of the test. Starting or stopping the service changes whether a connection can be established, while the target remains the same. The `--reason` option helps connect Nmap's conclusion to the response it received.

## Notes to complete

- Result while the server was running: [record the observed state and reason]
- Result after stopping the server: [record the observed state and reason]
- My explanation: [describe what changed and why]

## Reflection

1. What changed in the Nmap result after the server stopped?
2. What does `--reason` add to the scan output?
3. Why is scanning `127.0.0.1` a useful safe starting point for this experiment?

## Responsible use

Keep this exercise on loopback. Do not replace the target with a public or third-party system unless you have explicit authorization.
