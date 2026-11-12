# Rescuing and sanitising HAR files

A HAR file is a JSON export of browser network activity. Users send
them for slowness reports.

## Exporting a good one
- Set the Network filter to **All** before exporting.
- Ask the user to save the file to a local folder first. Files copied
  straight to a network share have arrived cut off at round byte counts.
- The default export is sanitised, but confirm it: search the file for
  `"name": "cookie"` and `authorization`.

## Rescuing a truncated or spliced HAR
`json.loads` fails on a broken file. The approach that worked:
1. Try `json.loads` first.
2. Otherwise scan the text for the start of each entry. For a
   DevTools export with 2-space indentation, entries begin with a line
   containing only `{` at an indent of 6 spaces.
3. Decode each entry with `json.JSONDecoder().raw_decode(text, index)`,
   where `index` is the exact position of the `{`. An index off by one
   silently returns zero entries.
4. De-duplicate across fragments on start time plus URL.

## Making it safe and small to share
- Remove `response.content.text` (response bodies).
- Remove the per-entry initiator call stacks. They can be about 28 kB
  each and cut a 10 MB merge to a few hundred kB.
- Redact cookies, authorization headers and OAuth state values.
- Validate the result is still readable JSON, then search it for
  leftover secrets before it leaves your machine.

## A parsing trap
Read headers such as trace IDs from the parsed JSON. Pairing them by
regex across entries once matched the wrong ones.
