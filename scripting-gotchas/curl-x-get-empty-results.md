# curl returns "0 results" with no error

**Symptom.** A REST search with `curl -X GET --data-urlencode "q=..."`
returns an empty result, and no error. It looks like "nothing found".

**Cause.** `--data-urlencode` puts the parameters in the request body.
`-X GET` forces the method, so the server receives a GET with a body and
ignores it. The query never ran.

**Fix.** Use `-G` so the data is appended to the URL as a query string.

    curl -s -G -H "Authorization: Bearer $TOKEN" \
      --data-urlencode 'q=project = X' \
      --data-urlencode 'maxResults=15' \
      "https://example.invalid/rest/api/search"

## Habit worth keeping
Before trusting an empty result, run a canary query that must match, such
as a record you know exists. If the canary returns zero, the request is
broken, not the data.
