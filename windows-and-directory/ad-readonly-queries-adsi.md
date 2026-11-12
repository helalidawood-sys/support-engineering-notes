# Read-only Active Directory queries with ADSI

Useful when RSAT and the ActiveDirectory module are not installed. All of
this is read-only and works from plain PowerShell.

## A basic lookup
```powershell
$s = [adsisearcher]'(&(objectCategory=person)(objectClass=user)(sAMAccountName=jdoe))'
$s.PropertiesToLoad.AddRange(@('displayName','whenCreated'))
$user = $s.FindOne()
$user.Properties['displayname']
```

## Exclude disabled accounts
Add this to the filter:
`(!(userAccountControl:1.2.840.113556.1.4.803:=2))`

## Searching a subtree
Set `$s.SearchRoot` to an OU. A substring filter on `distinguishedName`
is not supported, so search from the OU you want.

## When was an attribute last changed?
Request `msDS-ReplAttributeMetaData` in `PropertiesToLoad`. A normal
user can read it. For each attribute it gives:
- `dwVersion`: the number of originating writes
- `ftimeLastOriginatingChange`: the time of the last write, in UTC

It does not give the old value or who made the change. Version 1
written seconds after `whenCreated` means the attribute was set by the
creation process and never touched. Many accounts sharing one timestamp
window point to a bulk run.

## The Citrix home directory
It is not a normal property. The value lives inside the `userParameters`
blob as `CtxWFHomeDir`, with the drive in `CtxWFHomeDirDrive`. As I
decoded the blob:
1. Get the bytes with
   `[Text.Encoding]::Unicode.GetBytes([string]$user.Properties['userparameters'][0])`.
2. A 96-byte reserved header, then a 2-byte signature, then a 2-byte
   entry count.
3. Each entry has: name length (2 bytes), value length (2 bytes), type
   (2 bytes), the name in UTF-16LE, then the value as ASCII hex of the
   real bytes. Decode the hex to read the path.

## Limits
Your own account may be denied on home folders, so folder contents may
not be comparable from where you sit. Say so in your findings instead of
guessing.
