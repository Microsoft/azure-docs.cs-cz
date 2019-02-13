---
title: 'Azure AD Connect: Referenční informace prostředí PowerShell ADSyncTools | Dokumentace Microsoftu'
description: Tento dokument obsahuje referenční informace pro modul Powershellu ADSyncTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8988035d2ef28dfe4da632baca2e11205bc88340
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218104"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  Referenční informace prostředí PowerShell ADSyncTools
Následující dokumentace obsahuje referenční informace pro modul Powershellu ADSyncTools.psm1, která je součástí služby Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SYNOPSE
Vymazat mS-Ds-ConsistencyGuid od uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vymazat hodnotu v mS-Ds-ConsistencyGuid pro cílový AD uživatel

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
Cílový uživatel ve službě AD k nastavení

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>-Databáze
{{Vyplnit Popis databáze}}

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

#### <a name="-instance"></a>– Instance
{{Vyplnit popis Instance služby}}

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
{{Vyplnit popis hesla}}

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
{{Zadejte popis serveru}}

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
{{Vyplnit popis uživatelské jméno}}

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SYNOPSE
Exportovat sestavu ConsistencyGuid

### <a name="syntax"></a>SYNTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Generuje ConsistencyGuid sestavu na základě souboru CSV importovat z ADSyncToolsImmutableIdMigration importu

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
Použít alternativní přihlašovací ID (e-mailu)

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

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
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

#### <a name="-output"></a>-Output
Název výstupního souboru pro soubory CSV a protokolu

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Zadejte název hostitele popis}}

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
Načíst uživatele ze služby AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí objekt AD chcete provést: Podpora více doménové struktury

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
Cílový uživatel ve službě AD nastavit ConsistencyGuid

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SYNOPSE
Získat mS-Ds-ConsistencyGuid od uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí hodnotu v mS-Ds-ConsistencyGuid atribut target AD uživatele ve formátu identifikátoru GUID

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
Cílový uživatel ve službě AD k nastavení

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SYNOPSE
Získejte ObjectGuid od uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí hodnotu atribut ObjectGUID cílové AD uživatele ve formátu identifikátoru GUID

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
Cílový uživatel ve službě AD k nastavení

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SYNOPSE
Get AAD Connect historie spuštění

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce, která vrací historie spuštění služby AAD Connect ve formátu XML

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

#### <a name="-days"></a>-Dnů
{{Vyplnit dnů popis}}

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SYNOPSE
Získání uživatelů s chybami SourceAnchor změnit

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce dotazů AAD Connect historie spuštění a vyexportuje všechny uživatele oznámena tato chyba: "Atribut SourceAnchor změnila."

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
#Required Parameters
```

$sourcePath = Read-Host - řádku "zadejte cestu k souboru vašeho protokolu s názvem souboru" #"\<cesta_k_souboru\>" $outputPath = Read-Host-Prompt "Zadejte vaše mimo cestu k souboru s názvem souboru" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-sourcePath
{{Vyplnit sourcePath popis}}

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
{{Vyplnit outputPath popis}}

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SYNOPSE
Importovat ImmutableID z AAD

### <a name="syntax"></a>SYNTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Generuje soubor se všemi uživateli Azure AD Synchronized obsahující ImmutableID hodnotu ve formátu GUID požadavky: MSOnline PowerShell Module

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

#### <a name="-output"></a>-Output
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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Získat synchronizovat uživatele z Azure AD Koš

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>– Dotaz
{{Zadejte popis dotazu}}

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
{{Vyplnit popis objektu SqlConnection}}

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SYNOPSE
Skript pro odebrání atributem UserCertificate certifikáty s vypršenou platností

### <a name="syntax"></a>SYNTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tento skript má všechny objekty z cíle organizační jednotky ve vaší doméně služby Active Directory – filtrovat podle třídy objektu (uživatele nebo počítače) a odstraní všechny certifikáty s vypršenou platností atributem UserCertificate k dispozici.
Ve výchozím nastavení (režim BackupOnly) pouze provede zálohování, certifikáty s vypršenou platností do souboru a není nutné žádné změny ve službě AD.
Pokud používáte – BackupOnly $false pak jakékoli atributem UserCertificate k dispozici pro tyto objekty vypršela platnost certifikátu se odebere ze služby AD po zkopírování do souboru.
Jednotlivé certifikáty budou zálohovány do oddělených název souboru: ObjectClass_ObjectGUID_CertThumprint.cer skript také vytvoří soubor protokolu ve formátu CSV obsahující všechny uživatele s certifikáty, které buď platné nebo vypršela jejich platnost, včetně skutečné akce (vynecháno/exportované/Deleted).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

{Remove ADSyncToolsExpiredCertificates - TargetOU "organizační jednotky Uživatelé, OU = = Corp, DC = Contoso, DC = com" - ObjectClass uživatele

#### <a name="example-2"></a>PŘÍKLAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove ADSyncToolsExpiredCertificates-{TargetOU "OU = počítače, OU = Corp, DC = Contoso, DC = com" - ObjectClass počítače - BackupOnly $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Cíl organizační jednotky k vyhledávání objektů služby AD

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

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly neodstraní všechny certifikáty ze služby AD

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
Objekt třídy filtru

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SYNOPSE
Krátký popis

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

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Zadejte název hostitele popis}}

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

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SYNOPSE
(POSTUP) Obnoví ze souboru certicate atributem AD UserCertificate

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

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SYNOPSE
MS-Ds-ConsistencyGuid nastavit uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Nastavit hodnotu v mS-Ds-ConsistencyGuid atribut pro cílové uživatele AD

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
Cílový uživatel ve službě AD nastavit ConsistencyGuid

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
ImmutableId (pole bajtů, GUID, GUID řetězec nebo řetězec ve formátu Base64)

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SYNOPSE
{{Vyplňte stručný obsah}}

### <a name="syntax"></a>SYNTAXE

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>POPIS
{{Zadejte popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Příklad sem přidejte popis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Zadejte název hostitele popis}}

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
{{Vyplnit Popis portu}}

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

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SYNOPSE
Vytvoří soubor trasování z a krok AD importu

### <a name="syntax"></a>SYNTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Trasování všech dotazů protokolu ldap AAD Connect AD importu spustit z daného checkpoint vodoznak AD (soubor cookie oddílu). Vytvoří soubor trasování ".\ADimportTrace_yyyyMMddHHmmss.log" v aktuální složce.

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
{{Vyplnit popis ADConnectorXML}}

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

#### <a name="-dc"></a>-dc
AD Connector exportovat soubor XML

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

#### <a name="-filter"></a>-filtru
Rozlišující název kořenové domény doménové struktury

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
Typy objektů AD pro trasovacího \> * = všechny typy objektů

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

#### <a name="-adwatermark"></a>-ADwatermark
Pokud už běží jako správce domény není nutné poskytnout přihlašovací údaje služby AD.
Ruční zadání vodoznak, namísto XML soubor například $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SYNOPSE
Krátký popis

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

#### <a name="-context"></a>– Místní
Popis Param1 nápovědy

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
Popis param2 nápovědy

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
Popis param2 nápovědy

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

#### <a name="-filter"></a>-Filtru
Popis param2 nápovědy

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SYNOPSE
Aktualizace uživatele s novou ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Aktualizuje uživatele s použitím nové hodnoty ConsistencyGuid (ImmutableId) přijata z této sestavy ConsistencyGuid podporuje funkce přepínače WhatIf Poznámka: ConsistencyGuid sestavy musí být importovány pomocí Output_delimiter kartu

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
DistinguishedName

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

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
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

#### <a name="-output"></a>-Output
Název výstupního souboru pro soubory protokolu

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

#### <a name="-whatif"></a>-WhatIf
Ukazuje, co by se stalo při spuštění rutiny.
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

#### <a name="-confirm"></a>– Potvrzení
Vyzve k potvrzení před spuštěním rutiny.

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
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
