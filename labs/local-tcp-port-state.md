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

## Reading Nmap Output

Use the headings and values together. The line below is a field guide, not scan output:

```text
PORT  STATE  SERVICE  VERSION  REASON
```

| Field | How to interpret it |
| --- | --- |
| `PORT` | The port number and transport protocol, such as `8000/tcp`. |
| `STATE` | Nmap's inference for that port: `open`, `closed`, `filtered`, or sometimes `open or filtered`. |
| `SERVICE` | A service name associated with the port or identified by probing. Without `-sV`, it may only be the conventional name from Nmap's port database. |
| `VERSION` | The product and version Nmap could identify from service responses; it may be unknown or uncertain. |
| `REASON` | The response or observation that supports the state, shown when using `--reason`. Exact reason labels vary by scan type and response. |

### Interpret the whole result

- `Host is up` means Nmap considers the host reachable; it does not mean every port is open.
- `closed` means the host responded, but no service accepted a connection on that port at scan time.
- `filtered` means Nmap could not determine the port state clearly. A `no-response` reason means no reply arrived; it does not by itself prove that the host is down or identify why the reply is missing.
- A summary such as `Not shown: 997 closed tcp ports` means Nmap scanned those ports and omitted repetitive results from the detailed list. It refers only to the ports included in that scan, not necessarily every port on the host.
- Treat service and version identification as evidence to verify, not proof that a particular vulnerability exists.

### Reflection

1. What is the difference between a port state and a service name?
2. Does `Host is up` mean that a selected port is open?
3. What does `no-response` tell you, and what does it not prove?
4. What does `Not shown` mean in a port summary?

## Responsible use

Keep this exercise on loopback. Do not replace the target with a public or third-party system unless you have explicit authorization.
