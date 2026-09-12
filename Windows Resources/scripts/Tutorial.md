## Set up

*Open powershell with administrative privileges*
Type:

```powershell
  set-executionpolicy remote-signed or set-executionpolicy unrestricted
```

## Running the script

*Open powershell with administrative privileges*
Type:

```powershell
  Invoke-WebRequest "https://raw.githubusercontent.com/noweshere/cyberpatriots-resources/main/Windows%20Resources/scripts/script.ps1" -UseBasicParsing | Invoke-Expression
```
