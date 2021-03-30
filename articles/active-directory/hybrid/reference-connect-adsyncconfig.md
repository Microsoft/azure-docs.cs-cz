---
title: 'Azure AD Connect: Reference prostředí PowerShell ADSyncConfig | Microsoft Docs'
description: Tento dokument poskytuje referenční informace pro modul prostředí PowerShell ADSyncConfig. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850964"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: Reference prostředí PowerShell ADSyncConfig
Následující dokumentace obsahuje referenční informace pro modul ADSyncConfig. psm1 PowerShell, který je součástí nástroje Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SYNOPSE
Získá název a doménu účtu, který je nakonfigurovaný v jednotlivých konektorech služby AD.

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>POPIS
Tato funkce používá rutinu Get-ADSyncConnector, která je k dispozici v AAD Connect k načtení z parametrů připojení tabulka, která zobrazuje účet konektorů služby AD.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SYNOPSE
Získá objekty AD s děděním oprávnění zakázané.

### <a name="syntax"></a>SYNTAXE

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vyhledá ve službě AD počínaje parametrem SearchBase a vrátí všechny objekty filtrované podle parametru ObjectClass, který má aktuálně zakázanou dědičnost seznamu ACL.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
Najde v doméně contoso objekty s zakázanou dědičností (ve výchozím nastavení se vrátí jenom objekty organizationalUnit).
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>PŘÍKLAD 2
Vyhledá objekty User s zakázanou dědičností v doméně contoso.
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>PŘÍKLAD 3
Najde všechny typy objektů s zakázanou dědičností v organizační jednotce.
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
SearchBase pro dotaz protokolu LDAP, který může být rozlišující nebo plně kvalifikovaný název domény služby AD

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

#### <a name="-objectclass"></a>– ObjectClass
Třída objektů, které se mají hledat, může být "*" (pro libovolnou třídu objektů), "User", "Group", "Container" atd. Ve výchozím nastavení tato funkce vyhledá objektovou třídu ' organizationalUnit '.

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte doménovou strukturu a doménu služby Active Directory pro základní oprávnění ke čtení.

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
Funkce Set-ADSyncBasicReadPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Číst přístup k vlastnostem u všech atributů pro všechny objekty odvozeného počítače
2.
Číst přístup k vlastnostem u všech atributů pro všechny podřízené objekty zařízení
3.
Číst přístup k vlastnostem u všech atributů pro všechny objekty následníka foreignsecurityprincipal
5.
Číst přístup k vlastnostem u všech atributů pro všechny objekty potomků uživatele
6.
Číst přístup k vlastnostem u všech atributů pro všechny odvozené objekty InetOrgPerson
7.
Číst přístup k vlastnostem u všech atributů pro všechny objekty potomk Group
8.
Číst přístup k vlastnostem u všech atributů pro všechny objekty kontaktu potomků

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte doménovou strukturu a doménu služby Active Directory pro funkci hybridního systému Exchange.

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
Funkce Set-ADSyncExchangeHybridPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Přístup k vlastnostem pro čtení/zápis u všech atributů pro všechny objekty potomků uživatele
2.
Přístup k vlastnostem pro čtení/zápis u všech atributů pro všechny odvozené objekty InetOrgPerson
3.
Přístup k vlastnostem pro čtení/zápis u všech atributů pro všechny objekty potomk Group
4.
Přístup k vlastnostem pro čtení/zápis u všech atributů pro všechny objekty kontaktu potomků

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte svou doménovou strukturu a doménu služby Active Directory pro funkci veřejné složky Exchange pošty.

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
Funkce Set-ADSyncExchangeMailPublicFolderPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Číst přístup k vlastnostem u všech atributů pro všechny objekty následníka PublicFolder

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte doménovou strukturu a doménu služby Active Directory pro funkci mS-DS-ConsistencyGuid.

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
Funkce Set-ADSyncMsDsConsistencyGuidPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Přístup k vlastnostem pro čtení a zápis v atributu mS-DS-ConsistencyGuid pro všechny objekty potomků uživatelů

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte doménovou strukturu a doménu služby Active Directory pro synchronizaci hodnot hash hesel.

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
Funkce Set-ADSyncPasswordHashSyncPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Replikují se změny adresáře.
2.
Replikují se všechny změny adresáře.

Tato oprávnění jsou udělena pro všechny domény v doménové struktuře.

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
Název účtu služby Active Directory, který se bude používat Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která se bude používat Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte doménovou strukturu a doménu služby Active Directory pro zpětný zápis hesla ze služby Azure AD.

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
Funkce Set-ADSyncPasswordWritebackPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Resetování hesla u uživatelských objektů potomků
2.
Přístup k vlastnostem zápisu v atributu lockoutTime pro všechny objekty potomků uživatele
3.
Přístup k vlastnostem zápisu v atributu pwdLastSet pro všechny objekty potomků uživatele

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SYNOPSE
Umožňuje posílit oprávnění objektu AD, který není v žádné skupině zabezpečení s ochranou AD zahrnutý jinak.
Typickým příkladem je účet služby AD Connect (MSOL) vytvořený pomocí AAD Connect automaticky.
Tento účet má oprávnění k replikaci u všech domén, ale dá se snadno ohrozit, protože není chráněný.

### <a name="syntax"></a>SYNTAXE

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Funkce Set-ADSyncRestrictedPermissions sebude sestupně oprávnění až po poskytnutý účet.
Oprávnění k zpřísnění zahrnuje následující kroky:
1. Zakázat dědění pro zadaný objekt
2. Odebere všechny položky ACE u konkrétního objektu s výjimkou položek ACE specifických pro sebe.
Chceme, aby výchozí oprávnění zůstala beze změny, když se dostane do sebe.
3. Přiřaďte tato konkrétní oprávnění:

   | Typ | Name | Access | Platí pro |
   |------|------|--------|------------|
   | Povolit | SYSTEM | Úplné řízení | Tento objekt |
   | Povolit | Enterprise Admins | Úplné řízení | Tento objekt |
   | Povolit | Domain Admins | Úplné řízení | Tento objekt | 
   | Povolit | Administrators | Úplné řízení | Tento objekt |
   | Povolit | Podnikové řadiče domény | Vypsat obsah <br> Číst všechny vlastnosti <br> Oprávnění ke čtení | Tento objekt |
   | Povolit | Authenticated Users | Vypsat obsah <br> Číst všechny vlastnosti <br> Oprávnění ke čtení | Tento objekt |

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Rozlišující pole účtu služby Active Directory, jehož oprávnění je potřeba zvýšit
Obvykle se jedná o účet MSOL_nnnnnnnnnn nebo vlastní účet domény, který je nakonfigurovaný v konektoru služby AD.

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
Pověření správce, které má potřebná oprávnění k omezení oprávnění účtu ADConnectorAccountDN. Většinou se jedná o podnik nebo správce domény. Použijte plně kvalifikovaný název domény účtu správce, aby nedocházelo k chybám při vyhledávání účtů.
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
Když se použije DisableCredentialValidation, funkce se neověří, jestli jsou přihlašovací údaje zadané v přihlašovacích údajích platné ve službě AD a jestli má poskytnutý účet potřebná oprávnění k omezení oprávnění pro účet ADConnectorAccountDN.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SYNOPSE
Inicializujte svou doménovou strukturu a doménu služby Active Directory pro zpětný zápis skupin z Azure AD.

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
Funkce Set-ADSyncUnifiedGroupWritebackPermissions poskytne požadovaná oprávnění účtu synchronizace AD, který zahrnuje následující:
1.
Obecný pro čtení, zápis, odstranění, odstranění stromu a Create\Delete podřízenou položku pro všechny typy objektů skupiny a podobjekty

Tato oprávnění se vztahují na všechny domény v doménové struktuře.
Volitelně můžete zadat rozlišující parametr v parametru ADobjectDN pro nastavení těchto oprávnění pouze pro daný objekt AD (včetně dědičnosti na dílčí objekty).
V tomto případě bude ADobjectDN rozlišující název kontejneru, který si přejete propojit s funkcí GroupWriteback.

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Název účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Doména účtu služby Active Directory, která je nebo bude používána Azure AD Connect synchronizace ke správě objektů v adresáři.

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
Rozlišujícímu účtu služby Active Directory, který je nebo bude používán Azure AD Connect synchronizace pro správu objektů v adresáři.

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
Rozlišující pole cílového objektu AD pro nastavení oprávnění (volitelné)

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
Volitelný parametr, který označuje, jestli by se neměl aktualizovat kontejner AdminSDHolder pomocí těchto oprávnění

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SYNOPSE
Zobrazí oprávnění zadaného objektu AD.

### <a name="syntax"></a>SYNTAXE

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Tato funkce vrací všechna oprávnění služby AD aktuálně nastavená pro daný objekt služby Active Directory zadané v parametru-ADobjectDN.
ADobjectDN musí být k dispozici ve formátu rozlišujícího.

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
Další informace najdete v tématu about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
