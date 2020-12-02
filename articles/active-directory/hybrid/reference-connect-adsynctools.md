---
title: 'Azure AD Connect: Reference prostředí PowerShell ADSyncTools | Microsoft Docs'
description: Tento dokument poskytuje referenční informace pro modul prostředí PowerShell ADSyncTools. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2dd16f89851b9376557e544b86dc5e088891e63b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446987"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: Reference prostředí PowerShell ADSyncTools
Následující dokumentace obsahuje referenční informace pro modul ADSyncTools. psm1 PowerShell, který je součástí nástroje Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Instalace modulu PowerShellu pro ADSyncTools
Pokud chcete nainstalovat modul prostředí ADSyncTools PowerShell, postupujte takto:

1.  Otevření prostředí Windows PowerShell s práva pro správu
2.  Zadejte nebo zkopírujte a vložte následující text: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Stiskněte ENTER.
4.  Pokud chcete ověřit, že je modul nainstalovaný, zadejte nebo zkopírujte a vložte následující:
    ```powershell
    Get-module AdSyncTools
    ```
5.  Nyní byste měli vidět informace o modulu.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SYNOPSE
Zrušte zaškrtnutí políčka mS-DS-ConsistencyGuid od uživatele AD.

### <a name="syntax"></a>SYNTAXE

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Pro cílového uživatele AD vymažte hodnotu v mS-DS-ConsistencyGuid.

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SYNOPSE
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SYNOPSE
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>– Databáze
{{Vyplnit popis databáze}}

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
{{Popis instance Fill}}

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
{{Vyplnit popis jména uživatele}}

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SYNOPSE
Exportovat sestavu ConsistencyGuid

### <a name="syntax"></a>SYNTAXE

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vygeneruje sestavu ConsistencyGuid založenou na importovaném souboru CSV z Import-ADSyncToolsImmutableIdMigration

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
Použít alternativní přihlašovací ID (e-mail)

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

#### <a name="-userprincipalname"></a>– UserPrincipalName
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

#### <a name="-output"></a>– Výstup
Název výstupního souboru pro soubor CSV a soubory protokolu

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SYNOPSE
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-Název hostitele
{{Vyplnit popis názvu hostitele}}

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
Získat uživatele ze služby AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí objekt AD, který má být k: Podpora více doménových struktur.

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
Cílový uživatel ve službě AD pro nastavení ConsistencyGuid

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SYNOPSE
Získat mS-DS-ConsistencyGuid z uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vrátí hodnotu atributu mS-DS-ConsistencyGuid cílového uživatele služby AD ve formátu GUID.

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SYNOPSE
Získat ObjectGuid od uživatele AD

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SYNOPSE
Získat historii spuštění AAD Connect

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

#### <a name="-days"></a>– Dní
{{Vyplnit dny Description}}

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SYNOPSE
Získat uživatele s SourceAnchor změněnými chybami

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce dotazuje historii spuštění AAD Connect a exportuje všechny uživatele, kteří hlásí chybu: "atribut SourceAnchor se změnil."

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
#Required Parameters
```

$sourcePath = Read-Host-prompt "zadejte cestu k souboru protokolu s názvem souboru" # " \<Source_Path\> " $outputPath = Read-Host-prompt "zadejte cestu k souboru s názvem souboru" # " \<Out_Path\> ".
 
 Get-ADSyncToolsUsersSourceAnchorChanged-zdrojová_cesta $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>PŘÍKLAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-zdrojová_cesta
{{Vyplnit popis sourcePath}}

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

#### <a name="-outputpath"></a>– outputPath
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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SYNOPSE
Import ImmutableID z AAD

### <a name="syntax"></a>SYNTAXE

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vygeneruje soubor se všemi synchronizovanými uživateli Azure AD, které obsahují hodnotu ImmutableID ve formátu GUID požadavky: MSOnline modul PowerShellu.

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

#### <a name="-output"></a>– Výstup
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
Získat synchronizované uživatele ze složky Koš služby Azure AD

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SYNOPSE
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>– Dotaz
{{Popis dotazu Fill}}

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

#### <a name="-sqlconnection"></a>– SqlConnection
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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SYNOPSE
Skript pro odebrání certifikátů s vypršenou platností z atributu UserCertificate

### <a name="syntax"></a>SYNTAXE

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tento skript přebírá všechny objekty z cílové organizační jednotky ve vaší doméně služby Active Directory filtrované podle třídy objektu (uživatel/počítač) a odstraní všechny certifikáty s vypršenou platností, které jsou k dispozici v atributu UserCertificate.
Ve výchozím nastavení (v režimu bezplatná) se zálohují jenom certifikáty s vypršenou platností do souboru a nedělají žádné změny ve službě AD.
Pokud použijete-li-li-li-li $false-userCertificate, po zkopírování do souboru budou všechny certifikáty, které jsou v atributu pro tyto objekty přítomny, odstraněny ze služby AD.
Každý certifikát se zálohuje do odděleného názvu souboru: ObjectClass_ObjectGUID_CertThumprint. cer vytvoří taky soubor protokolu ve formátu CSV, ve kterém se zobrazí všichni uživatelé s certifikáty, které jsou platné nebo jejichž platnost vypršela, včetně skutečné provedené akce (přeskočení/exportu/odstranění).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = Users, OU = Corp, DC = contoso, DC = com"-ObjectClass User "

#### <a name="example-2"></a>PŘÍKLAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = počítače, OU = Corp, DC = contoso, DC = com"-ObjectClass Computer-bez možnosti přihlášení $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Cílová organizační jednotka pro vyhledávání objektů AD

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

#### <a name="-backuponly"></a>-Nena nejvyšší úrovni
Bez jakýchkoli těchto certifikátů nebude služba AD odstraňovat žádné certifikáty.

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

#### <a name="-objectclass"></a>– ObjectClass
Filtr třídy objektu

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

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
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-Název hostitele
{{Vyplnit popis názvu hostitele}}

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
(do) Obnoví atribut AD UserCertificate ze souboru certifikátu.

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
Nastavení mS-DS-ConsistencyGuid pro uživatele AD

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Nastavte hodnotu v atributu mS-DS-ConsistencyGuid pro cílového uživatele AD.

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
Cílový uživatel ve službě AD pro nastavení ConsistencyGuid

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

#### <a name="-value"></a>-Value
ImmutableId (pole bajtů, identifikátor GUID, řetězec GUID nebo řetězec Base64)

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SYNOPSE
{{Vyplnit v souhrnech}}

### <a name="syntax"></a>SYNTAXE

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>POPIS
{{Vyplnit popis}}

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Sem přidejte Příklad popisu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-Název hostitele
{{Vyplnit popis názvu hostitele}}

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

#### <a name="-port"></a>– port
{{Vyplnit popis portu}}

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
Vytvoří trasovací soubor z a krok importu služby AD.

### <a name="syntax"></a>SYNTAXE

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Sleduje všechny dotazy LDAP pro import služby AAD Connect ze zadaného kontrolního bodu pro vytvoření meze AD (soubor cookie oddílu). Vytvoří trasovací soubor ". \ ADimportTrace_yyyyMMddHHmmss. log" v aktuální složce.

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

#### <a name="-dc"></a>– DC
Soubor XML pro export konektoru služby AD

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

#### <a name="-filter"></a>– filtr
Kořenový název domény doménové struktury

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
Typy objektů AD ke sledování \> * = všechny typy objektů

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
Pokud je už spuštěný jako správce domény, nemusíte zadávat přihlašovací údaje služby AD.
Ruční zadání meze místo souboru XML, například $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)"

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

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

#### <a name="-context"></a>– Kontext
Popis pro param1 Help

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
Popis pro param2 Help

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
Popis pro param2 Help

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

#### <a name="-filter"></a>– Filtr
Popis pro param2 Help

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SYNOPSE
Aktualizuje uživatele novou ConsistencyGuid (ImmutableId).

### <a name="syntax"></a>SYNTAXE

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Aktualizuje uživatele novou hodnotou ConsistencyGuid (ImmutableId), kterou jste provedli v sestavě ConsistencyGuid. Tato funkce podporuje poznámku přepínače WhatIf: ConsistencyGuid musí být naimportována s kartami Demiliter

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

#### <a name="-distinguishedname"></a>-Rozlišující
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

#### <a name="-action"></a>– Akce
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

#### <a name="-output"></a>– Výstup
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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
