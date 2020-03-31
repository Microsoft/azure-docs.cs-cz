---
title: 'Azure AD Connect: ADSyncConfig PowerShell Reference | Dokumenty společnosti Microsoft'
description: Tento dokument obsahuje referenční informace pro modul PowerShell ADSyncConfig.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381191"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell Reference
Následující dokumentace obsahuje referenční informace pro modul PowerShell ADSyncConfig.psm1, který je součástí služby Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Účet Get-AdsyncadConnector

### <a name="synopsis"></a>SYNOPSE
Získá název účtu a domény, která je nakonfigurována v každém konektoru Služby AD

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>POPIS
Tato funkce používá rutinu Get-ADSyncConnector, která je k dispozici v aplikaci AAD Connect, k načtení z parametrů připojení v tabulce zobrazující účet konektoru Služby AD.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-AdSyncObjectsWithInheritanceDisabled Get-AdSyncObjectsWithInheritanceDisabled Get-AdSyncObjectsWithInheritanceDisabled Get-

### <a name="synopsis"></a>SYNOPSE
Získá objekty AD s dědičností oprávnění zakázáno

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Hledání ve skrytém vzhledem k parametru SearchBase a vrátí všechny objekty filtrované podle parametru ObjectClass, které mají aktuálně zakázanou dědičnost acl.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>PŘÍKLAD 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>PŘÍKLAD 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
SearchBase pro dotaz LDAP, který může být DistinguishedName domény služby AD nebo fQDN

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
Třída objektů pro vyhledávání, které mohou být '*' (pro libovolnou třídu objektu), "uživatel", "skupina", "kontejner" atd. Ve výchozím nastavení bude tato funkce hledat třídu objektů "organizationalUnit".

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="set-adsyncbasicreadpermissions"></a>Set-AdSyncBasicReadPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro základní oprávnění ke čtení.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncBasicReadPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Přístup ke službě Číst vlastnosti u všech atributů pro všechny objekty počítače potomků
2.
Přístup ke službě Číst vlastnosti u všech atributů pro všechny objekty podřízeného zařízení
3.
Přístup ke službě Číst vlastnosti u všech atributů pro všechny objekty cizí zabezpečení potomků
5.
Přístup ke službě Číst vlastnosti u všech atributů pro všechny objekty uživatele potomků
6.
Přístup ke službě Číst pro všechny atributy pro všechny objekty inetorgperson následovníků
7.
Přístup ke službě Číst ve všech atributech pro všechny objekty skupiny potomků
8.
Přístup ke službě Číst ve všech atributech pro všechny objekty kontaktů potomků

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-AdsyncExchangeHybridPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro funkci Exchange Hybrid.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncSyncSyncHybridPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Přístup ke vlastnostem pro čtení a zápis u všech atributů pro všechny objekty uživatele potomků
2.
Přístup ke vlastnostem pro čtení a zápis u všech atributů pro všechny objekty inetorgperson následovníků
3.
Přístup ke vlastnostem pro čtení a zápis u všech atributů pro všechny objekty skupiny potomků
4.
Přístup ke vlastnostem pro čtení a zápis u všech atributů pro všechny objekty kontaktů potomků

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Oprávnění set-adsyncexchangemailpublicfolderpermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro funkci Veřejné složky pošty exchange.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncExchangeMailPublicFolderPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Přístup ke službě Číst u všech atributů pro všechny objekty publicfolder descendant

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Oprávnění Guids konzistence set-ADSyncMsDsDsD

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro funkci mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncMsDsKonzistencGuidPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, mezi něž patří následující:
1.
Přístup ke vlastnostem pro čtení a zápis v atributu mS-DS-ConsistencyGuid pro všechny objekty uživatele potomků

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro synchronizaci hodnot hash hesel.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncPasswordHashSyncPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Replikace změn adresáře
2.
Replikace adresáře změní vše

Tato oprávnění jsou udělena všem doménám v doménové struktuře.

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který bude služba Azure AD Connect Sync používat ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, kterou bude služba Azure AD Connect Sync používat ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Oprávnění Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro zpětný zápis hesla ze služby Azure AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncPasswordWritebackPermissions uděluje požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Obnovit heslo u podřízených uživatelských objektů
2.
Přístup k vlastnosti zápisu v atributu lockoutTime pro všechny objekty následníka uživatelů
3.
Přístup k vlastnosti zápisu na atribut pwdLastSet pro všechny objekty uživatele potomků

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="set-adsyncrestrictedpermissions"></a>Oprávnění Set-AdsyncrestrictedPermissions

### <a name="synopsis"></a>SYNOPSE
Zpřísňte oprávnění pro objekt ad, který není jinak součástí žádné skupiny zabezpečení chráněné ho diody.
Typickým příkladem je účet AD Connect (MSOL) vytvořený službou AAD Connect automaticky.
Tento účet má oprávnění replikovat ve všech doménách, ale může být snadno ohrožena, protože není chráněna.

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncRestrictedPermissions zpřísní oprávnění oo poskytnutého účtu.
Oprávnění k dotažení zahrnují následující kroky:
1.
Zakázat dědičnost zadaného objektu
2.
Odeberte všechny ace na konkrétní objekt, s výjimkou ACE specifické pro SELF.
Chceme zachovat výchozí oprávnění neporušená, pokud jde o SELF.
3.
Přiřaďte tato konkrétní oprávnění:

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
DistinguishedName účtu služby Active Directory, jehož oprávnění je třeba zpřísnit.
Obvykle se jedná o účet MSOL_nnnnnnnnnn nebo vlastní doménový účet, který je nakonfigurován v konektoru služby AD.

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
Pověření správce, které má potřebná oprávnění k omezení oprávnění k účtu ADConnectorAccountDN. Obvykle se jedná o správce rozlehlé sítě nebo domény. Chcete-li se vyhnout selhání vyhledávání účtu, použijte plně kvalifikovaný název domény účtu správce.
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
Při použití DisableCredentialValidation funkce nezkontroluje, zda jsou pověření uvedená v -Credential platná ve službě AD a zda má poskytnutý účet potřebná oprávnění k omezení oprávnění k účtu ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Oprávnění Zpětného zápisu set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializovat doménovou strukturu a doménu služby Active Directory pro zpětné zápisskupiny z Azure AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncUnifiedGroupWritebackPermissions bude udělovat požadovaná oprávnění k účtu synchronizace služby AD, která zahrnují následující:
1.
Obecný soubor Pro čtení/zápis, Odstranění, Odstranění stromu a Vytvořit\Odstranit podřízenou položku pro všechny typy objektů skupiny a podobjekty

Tato oprávnění jsou použita pro všechny domény v doménové struktuře.
Volitelně můžete zadat DistinguishedName v ADobjectDN parametr nastavit tato oprávnění pouze pro tento objekt AD (včetně dědičnosti na dílčí objekty).
V tomto případě ADobjectDN bude rozlišující název kontejneru, který chcete propojit s GroupWriteback funkce.

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

#### <a name="example-4"></a>PŘÍKLAD 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNázev_účtu
Název účtu služby Active Directory, který je nebo bude používat Azure AD Connect Sync ke správě objektů v adresáři.

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
Doména účtu služby Active Directory, který je nebo bude používán Azure AD Connect Sync ke správě objektů v adresáři.

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
DistinguishedName účtu služby Active Directory, který je nebo bude používán azure ad connect sync ke správě objektů v adresáři.

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
DistinguishedName cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr označující, zda by kontejner AdminSDHolder neměl být aktualizován pomocí těchto oprávnění.

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

## <a name="show-adsyncadobjectpermissions"></a>Oprávnění zobrazit adsyncadobjectpermissions

### <a name="synopsis"></a>SYNOPSE
Zobrazí oprávnění určeného objektu AD.

### <a name="syntax"></a>SYNTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tato funkce vrátí všechna oprávnění ad aktuálně nastavená pro daný objekt AD, která jsou k dispozici v parametru -ADobjectDN.
ADobjectDN musí být poskytnuta ve formátu DistinguishedName.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN Description}}

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .
