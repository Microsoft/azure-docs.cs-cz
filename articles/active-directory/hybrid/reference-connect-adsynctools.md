---
title: 'Azure AD Connect: AdSyncTools PowerShell Reference | Dokumenty společnosti Microsoft'
description: Tento dokument obsahuje referenční informace pro modul ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60454655"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell Reference
Následující dokumentace obsahuje referenční informace pro modul PowerShell ADSyncTools.psm1, který je součástí služby Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Vymazání adsynctoolskonzistenceGuid

### <a name="synopsis"></a>SYNOPSE
Vymazání identifikátoru mS-Ds-ConsistencyGuid od uživatele ad

### <a name="syntax"></a>SYNTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vymazání hodnoty v mS-Ds-ConsistencyGuid pro cílového uživatele služby AD

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Nastavení cílového uživatele ve ad

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-AdsynctoolsadModuleLoaded

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>-Databáze
{{Popis databáze výplně}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Popis instance výplně}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill Password Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill Server Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill Popis uživatelského jména}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsKonzistenceGuidMigrace

### <a name="synopsis"></a>SYNOPSE
Sestava Konzistence exportu

### <a name="syntax"></a>SYNTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Generuje sestavu ConsistencyGuid založenou na importu souboru CSV z importu ADSyncToolsImmutableIdMigration

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Použít alternativní ID přihlášení (mail)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-NeměnnéIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Výstup
Výstupní název souboru pro soubory CSV a LOG

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLInstances

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SYNOPSE
Získání uživatele ze ad

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí objekt služby AD, který má být v režimu: Podpora více doménových vrstev.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Cílový uživatel ve ad nastavit ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-adsynctoolsconsistencyguid"></a>Zapoužitíidentifikátoru Get-ADSyncTools

### <a name="synopsis"></a>SYNOPSE
Získejte mS-Ds-ConsistencyGuid od uživatele ad

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí hodnotu v atributu mS-Ds-ConsistencyGuid cílového uživatele služby AD ve formátu GUID.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Nastavení cílového uživatele ve ad

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjektGuid

### <a name="synopsis"></a>SYNOPSE
Získání objektového guidu od uživatele ad

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí hodnotu v atributu ObjectGUID cílového uživatele služby AD ve formátu GUID.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Nastavení cílového uživatele ve ad

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-adsynctoolsrunhistory"></a>Get-AdSyncToolsRunHistorie

### <a name="synopsis"></a>SYNOPSE
Získejte historii spuštění služby AAD Connect

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce, která vrací historii spuštění připojení AAD ve formátu XML

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-days"></a>-Dny
{{Popis dnů výplně}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-AdSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SYNOPSE
Získat uživatele s SourceAnchor změněny chyby

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce se dotazuje AAD Connect Run History a exportuje všechny uživatele, kteří hlásí chybu: Atribut SourceAnchor byl změněn.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Zadejte cestu souboru\<\>protokolu s názvem souboru" #" Source_Path " $outputPath =\<\>Read-Host -Prompt "Zadejte cestu ven souboru s názvem souboru" #" Out_Path "
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourceCesta $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fill outputPath Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigrace

### <a name="synopsis"></a>SYNOPSE
Importovat ID neměnné z AAD

### <a name="syntax"></a>SYNTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Generuje soubor se všemi synchronizovanými uživateli Azure AD obsahující hodnotu IMMuTABLEID v požadavcích na formát GUID: Modul Prostředí MSOnline PowerShell

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-output"></a>-Výstup
Výstupní soubor CSV

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin -IncludeSyncUsersFromRecycleBin -IncludeSyncUsersFromRecycleBin -Include
Získat synchronizované uživatele z koše Azure AD

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .


## <a name="invoke-adsyncdatabasequery"></a>Vyvolat databázový dotaz AdSync

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>-Dotaz
{{Fill Query Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection Description}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SYNOPSE
Skript pro odebrání certifikátů, jejichž platnost vypršela z atributu UserCertificate

### <a name="syntax"></a>SYNTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tento skript přebere všechny objekty z cílové organizační jednotky ve vaší doméně služby Active Directory – filtrované podle třídy objektů (uživatel/počítač) a odstraní všechny certifikáty, jejichž platnost vypršela v atributu UserCertificate.
Ve výchozím nastavení (režim BackupOnly) bude zálohovat pouze certifikáty, jejichž platnost vypršela, do souboru a neprovede žádné změny ve službě AD.
Pokud použijete -BackupOnly $false, bude po zkopírování do souboru odebrán jakýkoli certifikát s prošlou platností v atributu UserCertificate pro tyto objekty.
Každý certifikát bude zálohován na samostatný název souboru: ObjectClass_ObjectGUID_CertThumprint.cer Skript také vytvoří soubor protokolu ve formátu CSV zobrazující všechny uživatele s certifikáty, které jsou platné nebo jejichž platnost vypršela, včetně skutečné akce (Přeskočeno/Exportováno/Odstraněno).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass uživatel

#### <a name="example-2"></a>PŘÍKLAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Cílová ou k vyhledávání objektů AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-Pouze zálohy
BackupOnly neodstraní žádné certifikáty ze služby AD.

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Třída objektu, filtr

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="repair-adsynctoolsautoupgradestate"></a>Oprava-AdSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SYNOPSE
Stručný popis

### <a name="syntax"></a>SYNTAXE

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>POPIS
Dlouhý popis

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Vyřešit adresu AdSyncHostAddress

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Obnovit adsynctoolsvypršela platnostcertifikáty

### <a name="synopsis"></a>SYNOPSE
(CHCETE-LI) Obnoví atribut UserCertificate služby AD ze souboru certifikátu.

### <a name="syntax"></a>SYNTAXE

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>POPIS
Dlouhý popis

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Guid konzistence nástrojů Set-ADSyncTools

### <a name="synopsis"></a>SYNOPSE
Nastavení mS-Ds-ConsistencyGuid u uživatele ad

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Nastavení hodnoty v atributu mS-Ds-ConsistencyGuid pro cílového uživatele služby AD

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Cílový uživatel ve ad nastavit ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Hodnota
ImmutableId (byte pole, GUID, řetězec GUID nebo řetězec Base64)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="test-adsyncnetworkport"></a>Test-AdsyncNetworkPort

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte synopse}}

### <a name="syntax"></a>SYNTAXE

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>POPIS
{{Vyplňte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Přidat zde popis příkladu }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Popis portu výplně}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-AdsynctoolsadImport

### <a name="synopsis"></a>SYNOPSE
Vytvoří trasovací soubor z a krok importu ad

### <a name="syntax"></a>SYNTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Trasuje všechny ldap dotazy importu Služby AAD Connect AD, které jsou spuštěny z daného kontrolního bodu vodoznaku služby AD (souborcookie oddílu). Vytvoří trasovací soubor .\ADimportTrace_yyyyMMddHHmmss.log v aktuální složce.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc (směr ový)
Soubor XML exportu konektoru AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Cílový řadič domény

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filtr
Kořen kořene lesa DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typy objektů AD \> ke sledování * = všechny typy objektů

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADvodoznak
Pokud již běží jako správce domény, není nutné zajišťovat přihlašovací údaje služby AD.
Ruční zadávání vodoznaku, namísto XML souboru např $ADwatermark.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SYNOPSE
Stručný popis

### <a name="syntax"></a>SYNTAXE

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Dlouhý popis

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-context"></a>-Kontext
Popis nápovědy Param1

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Popis nápovědy Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Popis nápovědy Param2

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filtr
Popis nápovědy Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Aktualizace-ADSyncToolsKonzistenceGuidMigrace

### <a name="synopsis"></a>SYNOPSE
Aktualizuje uživatele s novým ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Aktualizuje uživatele s novou hodnotou ConsistencyGuid (ImmutableId) převzatou ze sestavy ConsistencyGuid Tato funkce podporuje whatif přepínač Poznámka: ConsistencyGuid Sestava musí být importována pomocí pololitru tabulátoru

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-distinguishedname"></a>-DistinguishedName
Distinguishedname

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-NeměnnéIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Akce
Akce

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Výstup
Výstupní název souboru pro soubory LOG

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-Co když
Zobrazuje, co by se stalo při spuštění rutiny.
Rutina není spuštěna.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Před spuštěním rutiny zobrazí výzvu k potvrzení.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .
