# A result count equal to the limit is truncated

**Lesson.** If a fetch returns exactly the number you asked for as the
limit, assume it stopped early. Any count you derive from it is wrong.

**How I found it.** A scan of many mail folders with a limit of 5,000
returned exactly 5,000 records. The two largest folders had used up the
whole budget before the useful ones were reached, so a per-person count
came out far too low.

## Safer pattern
1. Enumerate the folders (or pages, or partitions) first and read their
   totals.
2. Fetch each one with a limit above its own total.
3. Assert that the result is below the limit:

```python
records = fetch(limit=5000)
assert len(records) < 5000, "Hit the limit: result is truncated"
```

4. Report the totals together with the method, so a reader can see the
   count is complete.
