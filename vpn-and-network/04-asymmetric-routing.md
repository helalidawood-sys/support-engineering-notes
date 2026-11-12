# Asymmetric routing

**Symptom.** A connection starts and then stalls. Traffic works in one
direction. It works from some networks and fails from others. A TCP
handshake never completes.

**Cause.** Return traffic takes a different path than outbound traffic,
usually because the return route is missing or points somewhere else. A
stateful firewall then drops packets of a flow whose start it never saw.

## Checks
1. Run traceroute in both directions, from each side if you can.
2. Compare routing tables on both ends and on the devices in between.
3. Read firewall logs for drops with an out-of-state or no-session
   reason.
4. Capture on both ends. A SYN that arrives while no SYN-ACK is ever
   seen confirms a return-path problem.
   - Windows: `pktmon` is built in.
   - Linux: `tcpdump -i any host <ip> and port <port>`

## In dynamic routing environments
Check how routes are propagated. Prefix lists and filters decide which
routes are announced, and local preference decides which path wins. A
static route redistributed into BGP without the right filter can send
return traffic the wrong way.

## Fix
Add the missing reverse route, or correct the preference. Retest from
both sides, then write down the cause and the change.
