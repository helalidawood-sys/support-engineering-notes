# Python TLS errors behind a corporate proxy

**Symptom.** `requests.get(...)` fails with
`SSLCertVerificationError: self-signed certificate in certificate chain`,
while `curl` to the same address works.

**Cause.** The corporate proxy re-signs TLS traffic with an internal
certificate authority. Python's bundled CA list (certifi) does not contain
it. curl on Windows uses the operating system's certificate store, which
does.

## Do not
Set `verify=False`. It turns off the protection for every request.

## Fixes that keep verification on
- Export the corporate root CA as a PEM file, then point Python at it:
  set `REQUESTS_CA_BUNDLE` (for `requests`) or `SSL_CERT_FILE` (for
  most other libraries) to that file.
- For pip: `pip config set global.cert <path-to-pem>`.
- On Python 3.10 and later, the `truststore` package makes Python use the
  operating system store:
  ```python
  import truststore
  truststore.inject_into_ssl()
  ```
- Where policy does not allow installing packages, call `curl` from
  Python with `subprocess` and parse the JSON output.

Ask your security team for the approved CA file. Do not download one from
an unknown source.
