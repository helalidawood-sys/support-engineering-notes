# VPN shows connected but web access is lost

**Symptom.** The client reports a healthy tunnel, yet browsers and web
apps time out. A reconnect often fixes it for a while.

## First five minutes
1. Confirm the tunnel is up: the virtual adapter has an address
   (`ipconfig /all`) and routes exist (`route print`).
2. Split DNS from connectivity. Run `nslookup <name>`, then try the same
   site by IP. Names failing while IPs work points at DNS. Both failing
   points at routing, the proxy, or a security agent.
3. Check the proxy and any web security agent: `netsh winhttp show proxy`,
   the browser proxy setting, and the agent's own status. An agent stuck
   before authentication can block web traffic while the tunnel looks fine.
4. Find what affected users share: gateway, client version, OS build,
   policy group.
5. Read proxy or firewall logs for the affected host. Many allowed
   connections and no blocks means the proxy is not the blocker. No
   entries at all means traffic never reaches it.

## Capture before you reconnect
A reconnect often clears the evidence. Save client logs and agent status
first, then reconnect and note how long the fix lasted.

## What changed?
Check recent policy changes, firewall rule updates and client updates.
Remember that settings such as environment variables only reach a running
application after it restarts.

## Keep a hypothesis log
Write each hypothesis with the evidence for and against it. Strike it
through openly when it is disproved. Colleagues save time and you build
trust.
