# Git Bash on Windows quirks

Small traps that each cost me a failed run.

## Backslashes collapse
Some tool wrappers turn a double backslash into a single one inside
commands and heredocs. UNC paths and regexes break silently. Use forward
slashes for ordinary Windows paths (`C:/Users/name/file.txt` works in
PowerShell and Python). Build UNC paths and regexes in code instead of
typing the backslashes.

## `/tmp` differs by tool
Git Bash `/tmp` maps to the Windows temp folder, but Windows Python
cannot open `/tmp/x`. Convert with `cygpath -m /tmp` or `cygpath -w file`.

## `python -I` hides user packages
Isolated mode ignores the per-user site-packages folder, so a package
installed with `pip install --user` (for example openpyxl) appears
missing. Run plain `python script.py`. Keep scripts in a different folder
from the data you process, so a stray file named like a standard module
cannot shadow it.

## PowerShell scripts
    powershell -NoProfile -ExecutionPolicy Bypass -File "$(cygpath -w script.ps1)"

Write output files as UTF-8 without BOM to keep accents:
`[IO.File]::WriteAllLines($path, $lines, (New-Object Text.UTF8Encoding($false)))`

## Heredocs with quotes
Long scripts with mixed quotes can break inside heredocs. Save the script
to a file with an editor and run the file.
