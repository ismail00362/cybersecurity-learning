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

## Service and version detection

A basic port scan reports the inferred state of a port. Adding `-sV` asks Nmap to send service-detection probes to an open port and try to identify the service and, when possible, its software version.

Run this while the local server is listening:

```bash
nmap -sT -sV -p 8000 127.0.0.1
```

Compare it with the basic scan:

```bash
nmap -sT -p 8000 127.0.0.1
```

| Scan | Main purpose |
| --- | --- |
| `-sT` | Check whether a TCP connection can be established to the selected port. |
| `-sT -sV` | Check the port and probe the service to identify its name and possible version. |

Nmap's service and version identification is based on the responses it receives. The result may be incomplete or uncertain, so treat it as a fingerprint to verify rather than guaranteed truth. The actual service and version labels depend on the server response and Nmap's detection database.

### Evidence to record

- Service/version shown by the basic scan: [record what Nmap displayed]
- Service/version shown with `-sV`: [record what Nmap displayed]
- What extra information did `-sV` provide? [explain in your own words]

## Responsible use

Keep this exercise on loopback. Do not replace the target with a public or third-party system unless you have explicit authorization.
