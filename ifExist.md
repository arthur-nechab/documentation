```vba
Sub TestSiDossierExiste()

Dim Dossier As String

Dossier = "C:\Dossier_test"

    If DossierExiste(MonDossier) = True Then
        MsgBox "Le dossier existe"
    Else
        MsgBox "Le dossier n'existe pas"
    End If

End Sub```
