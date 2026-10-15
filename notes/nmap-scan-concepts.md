# Nmap Scan Concepts

> Learning notes from authorized training labs. These notes summarize general concepts and omit room flags, target-specific answers, and private data.

## Learning goals

- Explain how TCP and UDP scans infer port states from network responses.
- Distinguish a completed TCP connection from a SYN probe that stops before completion.
- Record uncertainty accurately: no response does not prove that a port is open.

## TCP connection basics

A TCP connection normally begins with a three-way handshake:

1. The client sends `SYN` to request a connection.
2. The server replies with `SYN/ACK` if the port is listening.
3. The client sends `ACK` to complete the connection.

A closed TCP port commonly replies with `RST` (reset). A firewall may drop a probe or reject it, so scan results depend on the path and filtering rules.

## TCP Connect scan (`-sT`)

A TCP Connect scan asks the operating system to establish a normal TCP connection. It completes the handshake when the port is open, then closes the connection. This makes the result straightforward to interpret, though completed connections may be recorded by services and take more network exchanges.

## SYN scan (`-sS`)

A SYN scan sends the opening `SYN` probe. If it receives `SYN/ACK`, Nmap identifies the port as open and sends `RST` instead of completing the handshake. If it receives `RST`, the port is closed. If it receives no useful reply, the result may be filtered or otherwise inconclusive.

This is often called a half-open scan because the scanner does not finish the normal handshake. It is not guaranteed to be invisible: firewalls, monitoring systems, and modern intrusion detection can still detect scan traffic. On Linux, raw-packet scanning commonly requires elevated privileges; without them, Nmap may use a TCP Connect scan instead.

## UDP scan (`-sU`)

UDP has no TCP-style handshake or built-in acknowledgement that confirms delivery. A UDP service may not reply to an empty probe, so silence alone cannot establish that a port is open.

- A UDP response can indicate an open port.
- An ICMP port-unreachable response indicates a closed port.
- No response is ambiguous; Nmap may report `open|filtered`.

UDP scans can take longer because Nmap may need to wait and retry. Limit scanning to authorized targets and the ports needed for the lab.

## NULL, FIN, and Xmas scans

These TCP scan types send nonstandard flag combinations. For closed ports, systems may reply with `RST`; for open ports, some systems send no reply. Firewalls, operating-system differences, and packet filtering can make the result ambiguous, so silence does not prove a port is open.

- NULL scan (`-sN`): sends a TCP packet with no flags set.
- FIN scan (`-sF`): sends a packet with the `FIN` flag set.
- Xmas scan (`-sX`): sets `FIN`, `PSH`, and `URG`; the name refers to the multiple flags being lit up, like a decorated tree.

These scans are not universally reliable and should be understood as protocol behavior in a lab, not as a promise of firewall evasion.

## Host discovery and scan evidence

- Nmap normally tries host discovery before port scanning. `-Pn` skips that discovery step and treats the target as online; it does not send a ping first.
- `--reason` asks Nmap to show the response or condition behind a reported state.
- A ping (ICMP Echo) check and a TCP port scan test different things. A host can block ping and still respond to TCP probes.

## Safe lab workflow

1. Confirm the target IP and scope shown by the authorized lab.
2. Start with a limited scan that matches the lesson objective.
3. Record the exact command, time, target, and relevant output in the private lab log; redact credentials and tokens.
4. Explain what each response supports, and label ambiguous results as uncertain.
5. Stop the lab machine when finished.

Example for an authorized training target only:

```bash
nmap -sS --reason -p 1-1000 <authorized-lab-ip>
```

## Key takeaways

- TCP Connect completes the handshake; SYN scan stops after the SYN/ACK and resets the half-open connection.
- UDP silence is inconclusive because UDP does not acknowledge delivery by default.
- A scan result is an inference from observed responses, not proof that a service is absent or present in every circumstance.
- Good notes capture the reasoning and limitations, not just a command or result.

## Reflection questions

1. Why can a UDP port with no response appear as `open|filtered`?
2. What response distinguishes a closed TCP port from a likely open one during a SYN scan?
3. Why does `-Pn` not mean that Nmap first verifies the host with ping?
4. What extra evidence would help you interpret an ambiguous scan result?

## References

- Nmap Reference Guide: https://nmap.org/book/man.html
- Nmap Port Scanning Basics: https://nmap.org/book/port-scanning.html
