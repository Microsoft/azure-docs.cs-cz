---
title: 'Azure AD Connect: Reference prostředí PowerShell ADConnectivityTools | Microsoft Docs'
description: Tento dokument poskytuje referenční informace pro modul prostředí PowerShell ADConnectivityTools. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070798"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: Reference prostředí PowerShell ADConnectivityTools

Následující dokumentace obsahuje referenční informace pro modul ADConnectivityTools. psm1 PowerShell, který je součástí nástroje Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SYNOPSE

Zjistí místní problémy DNS.

### <a name="syntax"></a>SYNTAXE

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy místních připojení DNS.
Aby bylo možné konfigurovat konektor služby Active Directory, musí mít uživatel název ResolutionThe pro doménovou strukturu, ke které se pokouší připojit, a také v řadičích domény přidružených k této doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Určuje název doménové struktury, proti které se má testovat.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Řadiče domény

Zadejte řadiče domény, pro které chcete testovat.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Vrátí výsledek této diagnostiky ve formě PSObject.
Není nutné během ruční interakce s tímto nástrojem.

```yml
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

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>SYNOPSE

Určuje, zda zadaná doménová struktura existuje.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS

Zadá dotaz na server DNS pro IP adresy přidružené k doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Určuje název doménové struktury, proti které se má testovat.

```yml
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

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SYNOPSE

Ověří funkční úroveň doménové struktury AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Ověřuje, jestli je funkční úroveň doménové struktury služby AD stejná nebo větší než daná MinAdForestVersion (WindowsServer2003).
Může být požadován účet (doména \ uživatelské_jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PŘÍKLAD 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Cílová doménová struktura.
Výchozí hodnota je doménová struktura aktuálně přihlášeného uživatele.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Cílový objekt ForestFQDN

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášený k počítači, místo vyžádání vlastních přihlašovacích údajů od uživatele.

```yml
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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SYNOPSE

Detekuje problémy s připojením k místní síti.

### <a name="syntax"></a>SYNTAXE

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy připojení k místní síti.

V případě testů místních sítí musí být AAD Connect schopná komunikovat s pojmenovanými řadiči domény na portech 53 (DNS), 88 (Kerberos) a 389 (LDAP) Většina organizací spouští DNS na svých řadičích domény, což je důvod, proč je tento test aktuálně integrovaný.
Pokud je zadaný jiný server DNS, měl by se přeskočit port 53.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-dcs"></a>– Řadiče domény

Zadejte řadiče domény, pro které chcete testovat.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Pokud uživatel nepoužívá služby DNS poskytované lokalitou nebo přihlašovacím řadičem domény služby Active Directory, může chtít přeskočit kontrolu portu 53.
Uživatel musí být stále schopný vyřešit _.ldap._tcp.\<forestfqdn\>
aby byla konfigurace konektoru služby Active Directory úspěšná.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Vrátí výsledek této diagnostiky ve formě PSObject.
Není nutné během ruční interakce s tímto nástrojem.

```yml
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

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SYNOPSE

Určuje, jestli je dostupná zadaná doménová struktura a její přidružené řadiče domény.

### <a name="syntax"></a>SYNTAXE

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy příkazu "testovat" (jestli se počítač může připojit k cílovému počítači přes síť nebo Internet).

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Určuje název doménové struktury, proti které se má testovat.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Řadiče domény

Zadejte řadiče domény, pro které chcete testovat.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SYNOPSE

Ověřte, jestli jsou domény v získaném plně kvalifikovaném názvu domény doménové struktury dostupné.

### <a name="syntax"></a>SYNTAXE

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Ověřte, že všechny domény v získaném plně kvalifikovaném názvu domény doménové struktury jsou dosažitelné při pokusu o načtení DomainGuid a DomainDN.
Může být požadován účet (doména \ uživatelské_jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PŘÍKLAD 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Cílová doménová struktura.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Cílový objekt ForestFQDN

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášený k počítači, místo vyžádání vlastních přihlašovacích údajů od uživatele.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SYNOPSE

Ověří, jestli má uživatel přihlašovací údaje podnikového správce.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Vyhledá, jestli má zadaný uživatel přihlašovací údaje podnikového správce.
Může být požadován účet (doména \ uživatelské_jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášený k počítači, místo vyžádání vlastních přihlašovacích údajů od uživatele.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SYNOPSE

Načte DomainFQDN z kombinace účtu a hesla.

### <a name="syntax"></a>SYNTAXE

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Pokusí se získat objekt domainFQDN z poskytnutých přihlašovacích údajů.
Pokud je domainFQDN platný, vrátí se DomainFQDNName nebo RootDomainName v závislosti na volbě uživatele.
Může být požadován účet (doména \ uživatelské_jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Požadovaný druh dat, která budou načtena.
V současné době omezené na "DomainFQDNName" nebo "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášený k počítači, místo vyžádání vlastních přihlašovacích údajů od uživatele.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Pomocný parametr používaný funkcí Start-NetworkConnectivityDiagnosisTools

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SYNOPSE

Načte ForestFQDN z kombinace účtu a hesla.

### <a name="syntax"></a>SYNTAXE

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Pokusí se získat ForestFQDN ze zadaných přihlašovacích údajů.
Může být požadován účet (doména \ uživatelské_jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Cílová doménová struktura. Výchozí hodnota je doména aktuálně přihlášeného uživatele.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášený k počítači, místo vyžádání vlastních přihlašovacích údajů od uživatele.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SYNOPSE

Main – funkce

### <a name="syntax"></a>SYNTAXE

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí všechny dostupné mechanismy, které ověřují přihlašovací údaje služby AD.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Cílová doménová struktura.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Pro vlastní instalace: příznak, který se $True, pokud uživatel zvolil možnost "vytvořit nový účet služby AD" v průvodci účtem doménové struktury služby Active Directory v okně AADConnect.
$False, jestli se uživatel rozhodl použít existující účet služby AD.
Pro Expresní instalace: hodnota této proměnné musí být $True pro Expresní instalace.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parametr, který předem vyplní pole username při vyžádání přihlašovacích údajů uživatele.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SYNOPSE

Hlavní funkce pro testy síťového připojení.

### <a name="syntax"></a>SYNTAXE

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy připojení k místní síti.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>– Doménová struktura

Určuje název doménové struktury, pro kterou se má testovat.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>– Pověření

Uživatelské jméno a heslo uživatele, který spouští test.
Vyžaduje stejnou úroveň oprávnění, jaká je nutná ke spuštění Průvodce Azure AD Connect.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Určuje umístění souboru protokolu, který bude obsahovat výstup této funkce.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Řadiče domény

Zadejte řadiče domény, pro které chcete testovat.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Příznak, který umožňuje zobrazit zprávu o účelu této funkce.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Vrátí výsledek této diagnostiky ve formě PSObject.
Není nutné zadávat během ruční interakce s tímto nástrojem.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Určuje, zda jsou pověření zadaná uživatelem platná.
Není nutné zadávat během ruční interakce s tímto nástrojem.

```yml
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
