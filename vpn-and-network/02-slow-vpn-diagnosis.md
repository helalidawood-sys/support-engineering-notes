# Slow over VPN: where the time goes

**Symptom.** Users report slow applications, slow file access or slow
page loads while on the VPN.

## 1. Is it the VPN at all?
Repeat the same action in the office or off the VPN. If it is just as
slow there, the VPN is not the cause. Look at the application, the
server and the path instead.

## 2. Compare an affected user with a working user
Collect the client's diagnostic report from both machines and compare:
- MTU values. A smaller MTU on one side can mean fragmentation.
- Whether the client uses its datagram transport (UDP/DTLS) or has
  fallen back to TCP. TCP carried inside TCP behaves badly when packets
  are lost.
- Error counters, such as events where data could not be written.

If the two reports look alike, stop blaming the VPN client and look
elsewhere.

## 3. Compare the paths
Run `tracert` and `ping` to the same target from the VPN and from the
LAN. Note hop count and latency. A long path with tens of milliseconds
against a short path under 1 ms explains slow chatty protocols.

## 4. Check site and name mapping
A client subnet with no directory site can be sent to a distant domain
controller or file server.
- `nltest /dsgetsite` shows the site the machine believes it is in. An
  error about no site name means the subnet is not mapped.
- `nltest /dsgetdc:<domain>` shows which controller it picked.
- On file shares, a DFS referral can send users to a remote server.

## 5. Check the proxy path
Is web traffic going through a distant proxy? Compare the proxy a
machine uses on the VPN and in the office.

## 6. Write down numbers
Record before and after values for every test. "Slow" cannot be
compared. "2,000 ms per file open" can.
