# Escalating with evidence

Teams act faster on numbers than on impressions. This is the shape of
an escalation that gets read.

## Steps
1. **Measure many calls.** Capture a sample (a HAR works) and report the
   minimum, median and worst time.
2. **Give a baseline.** For example, a static file on the same host
   answering in about 20 ms against API calls taking about 5 s.
3. **Show the pattern.** A minimum equal to a round number, such as
   5.0 s, points at a timeout constant. Identical waits for a 0-byte
   and a large response rule out bandwidth.
4. **List what you ruled out.** For example: network path fine (fast
   401 without credentials), not specific to one client, not load.
5. **State a lead, and mark it as a lead.** Name who can confirm it.
6. **Attach sanitised data** and ask one concrete question.

## Message skeleton
- What users see, and since when
- The numbers (count, min, median, worst, baseline)
- What I ruled out
- My lead, and what would confirm it
- The question: "Can you check X on your side?"
