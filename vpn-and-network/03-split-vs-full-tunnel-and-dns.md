# Split vs full tunnel, and what it does to DNS

## Definitions
- **Full tunnel.** All traffic goes through the VPN.
- **Split tunnel.** Only listed destinations (subnets or domains) go
  through the VPN. Everything else goes straight to the internet.

## What changes for the user and for support
- Internet apps are usually faster on split tunnel, because they skip
  the corporate path. They also skip corporate inspection.
- DNS needs a plan. Internal names must be answered by internal DNS
  servers and public names by the local resolver. This is called split
  DNS.
- Client profiles often differ per operating system, so the same user
  can behave differently on Windows and on macOS.

## Typical failures
- The route to a subnet exists, but the name does not resolve.
- An internal name resolves to a public address, or fails entirely.
- One domain was added to the split list and a related one was missed.

## Checks
- `nslookup <name> <internal-dns-ip>` against `nslookup <name>`.
  Different answers show a split DNS problem.
- `route print` (Windows) or `netstat -rn` (macOS, Linux) shows which
  routes the VPN installed.
- PowerShell `Find-NetRoute -RemoteIPAddress <ip>` shows which
  interface a destination will use.

## Change record template
Write one for every VPN configuration change:
- **Action:** what is being changed
- **Why:** the request or incident behind it
- **Perimeter:** which users, OS and gateways
- **Impact:** what users will notice
- **Risk:** what could go wrong
- **Rollback:** the exact steps to undo it
