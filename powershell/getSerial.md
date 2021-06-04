```powershell
#La liste ici est une liste d'adresse IP qui reviens à la ligne à chaque adresse.
$computers = Get-Content "D:\liste_test.txt"

#Lecture IP par IP pour activer WinRM
$resultat = foreach ($computer in $computers) {
	$System = Get-WmiObject Win32_ComputerSystem -ComputerName $computer | Select-Object -Property Name,Model
    $BIOS = Get-WmiObject Win32_BIOS -ComputerName $computer | Select-Object -Property SerialNumber
	[PSCustomObject]@{
        ComputerName = $computer #Nom de l'ordinateur
		Name = $System.Name #Système d'exploitation
		Model = $System.Model #Modèle
        Serial = $BIOS.SerialNumber #Numéro de série
	}
}
#Affichage sur la console
$resultat

#Exportation sur une fiche excel
$resultat | Export-Csv -Path D:\resultat.csv -NoTypeInformation 
```
