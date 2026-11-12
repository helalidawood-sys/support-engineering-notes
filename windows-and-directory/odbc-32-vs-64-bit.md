# Database connection fails instantly after moving to 64-bit Office

**Symptom.** On a new laptop with 64-bit Office, Access or Excel cannot
connect through an ODBC DSN. It fails at once with no useful message.

**Cause.** The old database client was 32-bit. It registered its driver
only in the 32-bit ODBC registry hive (`WOW6432Node`). 64-bit Office reads
the 64-bit hive and cannot load a 32-bit DLL.

## Check
- Office bitness: File > Account > About.
- `C:\Windows\System32\odbcad32.exe` is the 64-bit administrator. The
  32-bit one is in `SysWOW64`. The names are misleading.
- Compare the driver lists in both.

## Fix
1. Remove any ghost 32-bit driver entry.
2. Install the 64-bit client.
3. Set `TNS_ADMIN` machine-wide to the folder holding `tnsnames.ora`.
4. Create or import the DSNs as the target user. User DSNs live in HKCU.
5. Log off or reboot. Environment variables load at logon.

## Pitfalls
- `reg import` run as admin writes to the admin's HKCU.
- Checking HKCU from an elevated prompt shows the admin's hive. Verify
  under `HKU\<SID>` or from a prompt run as the user.
- Testing before logoff fails because the aliases cannot be resolved.
- Old .reg exports can still point at the 32-bit driver path.
