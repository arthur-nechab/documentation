```powershell
#La liste ici est une liste d'adresse IP qui reviens à la ligne à chaque adresse.
$computers = Get-Content "D:\liste_test.txt"

#Lecture IP par IP pour activer WinRM
foreach ($computer in $computers) {
       Write-Host "Activation de WinRM sur" $computer "..." -ForegroundColor red 
       psexec \\$computer -s C:\Windows\System32\winrm.cmd qc -quiet
        if ($LastExitCode -eq 0) {
			Write-Host "Restart de WinRM" $computer "..." -ForegroundColor red 
            psservice \\$computer restart WinRM
            if ($LastExitCode -eq 0) {
				Write-Host "WinRM est maintenant active sur" $computer -ForegroundColor green}
            else {exit 1}
       }
}
```
