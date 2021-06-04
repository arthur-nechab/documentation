```vba
Sub TestSiDossierExiste()

Dim Dossier As String

Dossier = "D:\DATAINFO\Signature"

    If DossierExiste(Dossier) = True Then
        MsgBox "Le dossier existe"
    Else
        MsgBox "Le dossier n'existe pas"
    End If

End Sub```
