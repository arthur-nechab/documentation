```powershell
$computers = Get-Content "D:\liste_test.txt"

$resultat = foreach ($computer in $computers) {
	$System = Get-WmiObject Win32_ComputerSystem -ComputerName $computer | Select-Object -Property Name,Model
    $BIOS = Get-WmiObject Win32_BIOS -ComputerName $computer | Select-Object -Property SerialNumber
	[PSCustomObject]@{
        ComputerName = $computer
		Name = $System.Name
		Model = $System.Model
        Serial = $BIOS.SerialNumber
	}
}
$resultat

$resultat | Export-Csv -Path D:\resultat.csv -NoTypeInformation
```
