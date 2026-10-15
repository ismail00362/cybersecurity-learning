# TryHackMe: Further Nmap

## Lab record

- Platform: TryHackMe
- Room: Further Nmap
- Status: Completed
- Scope: The room's assigned, isolated training machine only

## Objective

Practice reading Nmap options, understand how scan types infer port states, and learn to explain the evidence behind scan results. The goal was to understand the behavior rather than memorize room answers.

## Concepts reviewed

### TCP Connect and SYN scans

A TCP Connect scan (`-sT`) lets the operating system complete the TCP three-way handshake when a port accepts the connection, then closes it. A SYN scan (`-sS`) sends the opening SYN probe; a SYN/ACK suggests an open port, while a reset suggests a closed port. The scanner resets after SYN/ACK rather than completing the handshake. Neither scan checks ICMP ping first as a prerequisite; host discovery and port scanning are separate parts of Nmap's workflow.

### UDP scanning

UDP does not provide a TCP-style handshake or built-in delivery acknowledgement. A service may receive a UDP packet and remain silent, so no reply is not proof that the packet failed or that the port is definitely open. Nmap can report an ambiguous state such as `open|filtered`; an ICMP port-unreachable response is evidence that the port is closed.

### NULL, FIN, and Xmas scans

These scans use unusual TCP flag combinations. An Xmas scan sets FIN, PSH, and URG. Some systems reset probes to closed ports and remain silent for open ports, but silence can also result from filtering. Results vary across operating systems and firewalls, so these scan types are not definitive and should not be treated as guaranteed firewall evasion.

### Host discovery and scan explanations

`-Pn` skips Nmap's host discovery step and proceeds as if the target is online; it does not perform a ping check. `--reason` helps explain why Nmap assigned a state. ICMP reachability and TCP port availability are different observations: a host may not answer ping while still responding on a TCP port.

## My takeaways

- I should explain a scan result from the response observed, not infer more than the evidence supports.
- Silence is especially ambiguous for UDP and for scans that rely on non-response behavior.
- A ping check is not the same thing as checking whether a TCP service is reachable.
- The SYN scan differs from a TCP Connect scan in whether it completes the TCP handshake.
- Recording my reasoning and uncertainties is more useful for review than saving a list of answers.

## Authorized practice record

Use the room-provided target only while the room is active. A generic example of a limited scan in an authorized lab is:

```bash
nmap -sS --reason -p 1-1000 <authorized-lab-ip>
```

Do not paste room flags, credentials, target IPs, or other target-sensitive details into this public report.

## Questions for review

1. Why can an unanswered UDP probe have more than one possible explanation?
2. What does Nmap skip when `-Pn` is used?
3. What is the key handshake difference between `-sT` and `-sS`?
4. Why should a silent response in an Xmas scan be treated cautiously?

## References

- Nmap Reference Guide: https://nmap.org/book/man.html
- Nmap Port Scanning Basics: https://nmap.org/book/port-scanning.html
