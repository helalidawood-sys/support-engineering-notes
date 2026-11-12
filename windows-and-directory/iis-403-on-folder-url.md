# IIS returns 403 on a folder URL

**Symptom.** Users see the IIS "403 - Forbidden" page on the application's
folder URL (`https://site/app/`). The full file URL
(`https://site/app/Default.aspx`) works after login.

## Reading the symptom
Login completes, so the account has permission. Folder URLs depend on the
Default Document setting. The likely cause is that no default document is
serving for the application.

## Checks
1. Read the sub-status code on the error page. `403.14` means directory
   listing denied, which is what IIS returns when no default document
   matches.
2. IIS Manager > the site or app > Default Document: is it enabled, and is
   the start page (for example `Default.aspx`) in the list?
3. Look for a `<defaultDocument>` section in the app's `web.config`. If
   it is missing, the setting is inherited from the parent site or
   `applicationHost.config`. Check there next.
4. Look for redirect or URL Rewrite rules. A site-level catch-all
   redirect can drop the path and send everyone to the bare folder URL.
5. IIS keeps a configuration history that can be compared with the
   current config. This needs a server administrator.

## Other causes seen on similar sites
- A renewed certificate that was not bound in IIS.
- A runtime error after an update.

## Workaround and fix
- Workaround: give users the full start-page URL.
- Fix: enable the Default Document and list the start page. Point any
  catch-all redirect at the real start page.

## Hygiene
Never paste connection strings or encrypted configuration blobs into
tickets.
