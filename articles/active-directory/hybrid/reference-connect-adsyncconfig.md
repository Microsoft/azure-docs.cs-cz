---
title: 'Azure AD Connect: Referenční informace prostředí PowerShell ADSyncConfig | Dokumentace Microsoftu'
description: Tento dokument obsahuje referenční informace pro modul Powershellu ADSyncConfig.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.openlocfilehash: 01b138fff437e41366b8442729fba89c82ed9073
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487961"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  Referenční informace prostředí PowerShell ADSyncConfig
Následující dokumentace obsahuje referenční informace pro modul Powershellu ADSyncConfig.psm1, která je součástí služby Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SYNOPSE
Získá název účtu a domény, který je nakonfigurovaný v každé AD Connector.

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>POPIS
Tato funkce využívá rutinu "Get-ADSyncConnector", který se nachází v AAD Connect načíst z parametrů připojení tabulka zobrazující účet konektory předávacího AD.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SYNOPSE
Získá objekty AD se dědičnost oprávnění zakázáno

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vyhledá ve službě AD od SearchBase parametr a vrátí všechny objekty, Filtroval ObjectClass parametru, které mají dědičnosti seznamu ACL v tuto chvíli zakázané.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase "Contoso"

#### <a name="example-2"></a>PŘÍKLAD 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase 'Contoso"- ObjectClass 'uživatele'

#### <a name="example-3"></a>PŘÍKLAD 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase organizační jednotky AzureAD, DC = Contoso, DC = com - ObjectClass = "*"

### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
SearchBase pro dotaz protokolu LDAP, který může být DistinguishedName domény AD nebo úplného názvu domény

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

#### <a name="-objectclass"></a>-ObjectClass
Třída objekty, které chcete hledat, který může být "*" (pro všechny třídy objektu), "uživatel", "skupina", "kontejner" atd. Ve výchozím nastavení tato funkce bude hledat "organizationalUnit" objekt třídy.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro základní oprávnění ke čtení.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncBasicReadPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty odvozené počítače
2.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty odvozené zařízení
3.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty odvozené sady cizích objektů zabezpečení
5.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny následné uživatelské objekty
6.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty inetorgperson potomků
7.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty odvozené skupiny
8.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny odvozené objekty kontaktu

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro funkci hybridní Exchange.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncExchangeHybridPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Přístup k vlastnosti pro čtení a zápis na všechny atributy pro všechny následné uživatelské objekty
2.
Přístup k vlastnosti pro čtení a zápis na všechny atributy pro všechny objekty inetorgperson potomků
3.
Přístup k vlastnosti pro čtení a zápis na všechny atributy pro všechny objekty odvozené skupiny
4.
Přístup k vlastnosti pro čtení a zápis na všechny atributy pro všechny odvozené objekty kontaktu

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro funkci veřejné složky pošty Exchange.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncExchangeMailPublicFolderPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Přístup k vlastnosti pro čtení pro všechny atributy pro všechny objekty odvozené publicfolder

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro mS-DS-ConsistencyGuid funkce.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncMsDsConsistencyGuidPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Přístup k vlastnosti pro čtení a zápis na atribut mS-DS-ConsistencyGuid pro všechny následné uživatelské objekty

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro synchronizaci hodnot hash hesel.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncPasswordHashSyncPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Replikace změn adresáře
2.
Replikace změn adresáře vše

Tato oprávnění jsou uvedeny všechny domény v doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro zpětný zápis hesla ze služby Azure AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncPasswordWritebackPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Resetování hesla pro následné uživatelské objekty
2.
Vlastnost oprávnění k zápisu na lockoutTime atribut pro všechny následné uživatelské objekty
3.
Vlastnost oprávnění k zápisu na pwdLastSet atribut pro všechny následné uživatelské objekty

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SYNOPSE
Zvýšit oprávnění u objektu AD, která není zahrnutá v opačném případě v libovolné skupině zabezpečení chráněný AD.
Typickým příkladem je automaticky vytvořen pomocí AAD Connect účtu AD Connect (MSOL).
Tento účet má oprávnění replikace pro všechny domény, ale dají snadno ohrozit jako není chráněný.

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncRestrictedPermissions bude posílit zálohy oprávnění účtu k dispozici.
Upevňování oprávnění zahrnuje následující kroky:
1.
Zakázat dědičnosti na zadaný objekt
2.
Odeberte všechny položky řízení přístupu pro daný objekt, s výjimkou ACE konkrétní sama na sebe.
Chcete zachovat beze změny výchozích oprávnění, pokud jde o sama na sebe.
3.
Přiřadíte tato konkrétní oprávnění:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, jehož oprávnění musí být zvýšit.
To je obvykle MSOL_nnnnnnnnnn účet nebo účet vlastní domény, který je nakonfigurovaný v AD Connector.

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

#### <a name="-credential"></a>-Credential
Přihlašovací údaje správce, který má dostatečná oprávnění k omezení oprávnění k účtu ADConnectorAccountDN. Obvykle se jedná o správce rozlehlé sítě nebo domény. Aby se zabránilo chybám vyhledávání účtu použijte plně kvalifikovaný název domény účtu správce.
Příklad: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Při použití DisableCredentialValidation funkce nebude kontrolovat, jestli přihlašovací údaje poskytnuté v – přihlašovací údaje jsou platné ve službě AD a pokud zadaný účet nemá potřebná oprávnění k omezení oprávnění k účtu ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializace vaší doménové struktury služby Active Directory a doménu pro zpětný zápis skupin ze služby Azure AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkci Set-ADSyncUnifiedGroupWritebackPermissions vám poskytne potřebná oprávnění k účtu synchronizace AD, které zahrnují následující:
1.
Obecný r/w, odstranit, odstranění stromu a podřízené Create\Delete pro všechny typy objektů a podobjektů skupiny

Tato oprávnění se použijí pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v parametru ADobjectDN nastavení těchto oprávnění k tomuto objektu AD (včetně dědičnosti na dílčí objekty).
V takovém případě bude ADobjectDN rozlišující název kontejneru, který by bylo žádoucí propojit pomocí funkce GroupWriteback.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PŘÍKLAD 4:
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Doména účtu služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName účet služby Active Directory, který je nebo bude využívat ke správě objektů v adresáři Azure AD Connect Sync.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName AD cílového objektu a nastavit oprávnění (volitelné)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Volitelný parametr k označení, pokud kontejner AdminSDHolder by neměly být aktualizovány s těmito oprávněními

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SYNOPSE
Zobrazuje oprávnění zadaného objektu AD.

### <a name="syntax"></a>SYNTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tato funkce vrátí všechna oprávnění AD aktuálně nastavená pro daný objekt AD zadaný v parametru - ADobjectDN.
ADobjectDN třeba zadat ve formátu DistinguishedName.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Vyplnit popis ADobjectDN}}

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

#### <a name="commonparameters"></a>CommonParameters
Tato rutina podporuje společné parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction a -WarningVariable.
Další informace najdete v části about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
