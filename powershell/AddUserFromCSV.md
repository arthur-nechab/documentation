```powershell
#Importation du module Active Directory 
Import-Module ActiveDirectory
 
#On récupère ensuite l'emplacement du fichier CSV
$CSV = Import-csv C:\shortlist.csv

#On désactive tout les users dans IT
Get-ADUser -F * -SearchBase 'OU=IT,DC=itic,DC=lan' | Disable-ADAccount
Get-ADUser -F * -SearchBase 'OU=IT,DC=itic,DC=lan' | Set-ADUser -Description "OFF"
Write-Host "Tout les comptes sont desactivés." -ForegroundColor red

#On récupère les informations du CSV
ForEach ($user in $CSV)
{	
	$prenom = $user.Prenom
	$nom = $user.Nom
	$email = $user.Email
    $login = $prenom+$nom

    #Si user existe dans le CSV et dans l'AD, on l'active et on le met à jour
    if (Get-ADUser -F {SamAccountName -eq $Login})
    {
    Enable-ADaccount -identity $login
    Set-ADUser -identity $login -Description "ON" -EmailAddress $email
    Write-Host "$prenom $nom a été activé et mis à jour." -ForegroundColor cyan
    }

    #Sinon crée l'user dans l'AD avec son dossier
    else
    {
    New-ADUser -GivenName "$prenom" -Surname "$nom" -Name "$prenom $nom" -SamAccountName "$login" -AccountPassword (ConvertTo-SecureString "Azerty1234" -AsPlainText -Force) -UserPrincipalName "$login@itic.lan" -Enabled $True -ChangePasswordAtLogon $True -PasswordNeverExpires $False -Description "NEW" -EmailAddress $email -Path "OU=IT,DC=itic,DC=lan" 
    Write-Host "$prenom $nom a été crée avec son dossier." -ForegroundColor green
    
    New-Item -Name $Login -ItemType Directory -Path c:\SharedFolder\
    
    #On donne les droits à l'user
    $temp = Get-Acl c:\SharedFolder\$login
    $AccessRule = New-Object Security.AccessControl.FileSystemAccessRule("itic.lan\$login","FullControl, Synchronize",3,0,"Allow")
    $temp.AddAccessRule($AccessRule)
    $temp | Set-Acl
    Write-Host "Et ses droits." -ForegroundColor green
    Write-Host "..."
    }
}
```
