# Reading browser timing for slowness reports

## Capture
1. Open DevTools > Network. Set the filter to **All** (an export only
   includes what the current filter shows).
2. Tick **Preserve log**, then reproduce the problem.
3. Click a slow request and open the **Timing** tab.

## The phases
Queueing or stalled, DNS lookup, initial connection, SSL, waiting for
server response (time to first byte), content download.

## How to read them
- A long **waiting for server response** with a tiny download means the
  server, or a device in the middle, held the request.
- A long **content download** points at the network.
- A large **blocked** time on HTTP/1.1 is usually queueing behind the
  per-host connection limit of about six.
- Many requests waiting the same minimum time (for example all 5.0 s or
  more) point at a fixed timeout, not at load.
- A tight band of waits with none below the floor is a timeout
  constant. Waits near whole multiples of it suggest sequential expiries.
- A 0-byte response that waits as long as a 79 kB one rules out a
  bandwidth explanation.
- Exclude long-polling endpoints from your statistics.

## Two cheap controls
1. Compare a static asset on the same host and connection with an API
   call. A static file answering in about 20 ms while the API takes 5 s
   is strong evidence.
2. Call the same API without credentials and time it:
   ```
   curl -s -o /dev/null -w "dns:%{time_namelookup} connect:%{time_connect} tls:%{time_appconnect} ttfb:%{time_starttransfer} total:%{time_total}\n" https://example.invalid/api/health
   ```
   A fast 401 shows the network path and the front door are fine.

## A lead, not a conclusion
Some client libraries ship with default timeouts of exactly 5000 ms.
When you see a 5 s floor, check the stack's default timeouts. Treat it
as a lead to confirm with the owning team.
