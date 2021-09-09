---
Tags: tips, ps, powershell, sysadmin
---

# Powershell Miscellany

tl;dr Every time I run into some action in Powershell (PS) that doesn't do what I would like to think it would.


Problem: Delete a folder that is not empty and created by an errant process
Solution: `Remove-Item -LiteralPath '<FOLDER-CANONICAL-PATH-HERE>' -Force -Recurse`