---
title: 'Azure AD Connect: AdconnectivityTools PowerShell Reference | Dokumenty společnosti Microsoft'
description: Tento dokument obsahuje referenční informace pro modul PowerShell ADConnectivityTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66473784"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell Reference

Následující dokumentace obsahuje referenční informace pro modul PowerShell ADConnectivityTools.psm1, který je součástí služby Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Potvrdit připojení dns

### <a name="synopsis"></a>SYNOPSE

Detekuje místní problémy se službou Dns.

### <a name="syntax"></a>SYNTAXE

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí místní testy připojení DNS.
Aby bylo možné konfigurovat konektor služby Active Directory, musí mít uživatel překlad názvů pro doménovou strukturu, ke které se pokouší připojit, i v řadičích domény přidružených k této doménové struktuře.

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

#### <a name="-forest"></a>-Les

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

#### <a name="-dcs"></a>-DC

Zadejte řadiče domény, proti kterými se má testovat.

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

Vrátí výsledek této diagnózy ve formě objektu PSObject.
Není nutné při ruční interakci s tímto nástrojem.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-forestexists"></a>Potvrdit-ForestExists

### <a name="synopsis"></a>SYNOPSE

Určuje, zda existuje zadaná doménová struktura.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS

Dotazuje se serveru DNS na adresy IP přidružené k doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Les

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-functionallevel"></a>Potvrdit-funkčníúroveň

### <a name="synopsis"></a>SYNOPSE

Ověří úroveň funkčnosti doménové struktury služby AD.

### <a name="syntax"></a>SYNTAXE

#### <a name="samaccount"></a>SamÚčet

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Ověří, zda úroveň funkčnosti doménové struktury služby AD je stejná nebo větší než daná verze MinAdForestVersion (WindowsServer2003).
Může být požadován účet (doména\uživatelské jméno) a heslo.

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

#### <a name="-forest"></a>-Les

Cílový les.
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

#### <a name="-forestfqdn"></a>-ForestFQDN

Cílový objekt ForestFQDN.

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

Funkce bude používat pověření uživatele, který je aktuálně přihlášen v počítači, spíše než požadovat vlastní pověření od uživatele.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-networkconnectivity"></a>Potvrdit připojení k síti

### <a name="synopsis"></a>SYNOPSE

Detekuje problémy s připojením k místní síti.

### <a name="syntax"></a>SYNTAXE

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy připojení k místní síti.

Pro testy místní sítě musí být služba AAD Connect schopna komunikovat s pojmenovanými řadiči domény na portech 53 (DNS), 88 (Kerberos) a 389 (LDAP) Většina organizací spouštějí služby DNS na svých řadičích domény, což je důvod, proč je tento test aktuálně integrován.
Port 53 by měl být přeskočen, pokud byl zadán jiný server DNS.

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

#### <a name="-dcs"></a>-DC

Zadejte řadiče domény, proti kterými se má testovat.

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

Pokud uživatel nepoužívá služby DNS poskytované webem služby AD / řadičem domény přihlášení, může chtít přeskočit kontrolní port 53.
Uživatel musí být stále schopen vyřešit _.ldap._tcp. \<forestfqdn,\> aby byla konfigurace konektoru služby Active Directory úspěšná.

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

Vrátí výsledek této diagnózy ve formě objektu PSObject.
Není nutné při ruční interakci s tímto nástrojem.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-targetsarereachable"></a>Potvrdit-CíleJsou dosažitelné

### <a name="synopsis"></a>SYNOPSE

Určuje, zda je daná doménová struktura a přidružené řadiče domény dosažitelné.

### <a name="syntax"></a>SYNTAXE

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí testy ping (zda počítač může dosáhnout cílového počítače prostřednictvím sítě a / nebo internetu)

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

#### <a name="-forest"></a>-Les

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

#### <a name="-dcs"></a>-DC

Zadejte řadiče domény, proti kterými se má testovat.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-validdomains"></a>Potvrdit-platné domény

### <a name="synopsis"></a>SYNOPSE

Ověřte, zda jsou domény ve získaném doménovém právu domény doménové struktury domény dosažitelné

### <a name="syntax"></a>SYNTAXE

#### <a name="samaccount"></a>SamÚčet

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Ověřte, zda jsou všechny domény ve získaném doménovém souboru Forest FQDN dosažitelné pokusem o načtení doménových guid ů a domaindn.
Může být požadován účet (doména\uživatelské jméno) a heslo.

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

#### <a name="-forest"></a>-Les

Cílový les.

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

#### <a name="-forestfqdn"></a>-ForestFQDN

Cílový objekt ForestFQDN.

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

Funkce bude používat pověření uživatele, který je aktuálně přihlášen v počítači, spíše než požadovat vlastní pověření od uživatele.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="confirm-validenterpriseadmincredentials"></a>Potvrdit validaptoznamenání podnik

### <a name="synopsis"></a>SYNOPSE

Ověří, zda má uživatel pověření enterprise admin.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Vyhledávání, pokud má zadaný uživatel pověření enterprise admin.
Může být požadován účet (doména\uživatelské jméno) a heslo.

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

Funkce bude používat pověření uživatele, který je aktuálně přihlášen v počítači, spíše než požadovat vlastní pověření od uživatele.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SYNOPSE

Načte DomainFQDN z kombinace účtu a hesla.

### <a name="syntax"></a>SYNTAXE

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Pokusí se získat objekt domainFQDN z poskytnutých pověření.
Pokud domainFQDN je platný, DomainFQDNName nebo RootDomainName bude vrácena, v závislosti na volbě uživatele.
Může být požadován účet (doména\uživatelské jméno) a heslo.

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

#### <a name="-domainfqdndatatype"></a>-DomainFQDNTyp dat

Požadovaný druh dat, která budou načtena.
V současné době omezena na "DomainFQDNName" nebo "RootDomainName".

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

Funkce bude používat pověření uživatele, který je aktuálně přihlášen v počítači, spíše než požadovat vlastní pověření od uživatele.

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

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError ._-returnExceptionOnError .-returnExceptionOn

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="get-forestfqdn"></a>Získat-ForestFQDN

### <a name="synopsis"></a>SYNOPSE

Načte ForestFQDN z kombinace účtu a hesla.

### <a name="syntax"></a>SYNTAXE

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS

Pokusí se získat ForestFQDN z zadaných pověření.
Může být požadován účet (doména\uživatelské jméno) a heslo.

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

#### <a name="-forest"></a>-Les

Cílový les. Výchozí hodnota je doména aktuálně přihlášeného uživatele.

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

Funkce bude používat pověření uživatele, který je aktuálně přihlášen v počítači, spíše než požadovat vlastní pověření od uživatele.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="start-connectivityvalidation"></a>Ověřování start-connectivityValidation

### <a name="synopsis"></a>SYNOPSE

Hlavní funkce.

### <a name="syntax"></a>SYNTAXE

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS

Spustí všechny dostupné mechanismy, které ověřují pověření služby AD, jsou platné.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Les

Cílový les.

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

Pro vlastní instalace: Příznak, který je $True, pokud uživatel zvolil "Vytvořit nový účet služby AD" v okně účtu doménové struktury služby AADConnect průvodce aadconnect.
$False, pokud uživatel zvolil "Použít existující účet ad".
Pro expresní instalace: Hodnota této proměnné musí být $True pro expresní instalace.

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

Parametr, který předem vyplní pole Uživatelské jméno, když jsou požadována pověření uživatele.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SYNOPSE

Hlavní funkce pro testy připojení k síti.

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

#### <a name="-forest"></a>-Les

Určuje název doménové struktury, proti které se má testovat.

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

#### <a name="-credentials"></a>-Pověření

Uživatelské jméno a heslo uživatele, který je spuštěn test.
Vyžaduje stejnou úroveň oprávnění, která je vyžadována ke spuštění Průvodce připojením Azure AD.

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

#### <a name="-logfilelocation"></a>-LogFile Umístění

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

#### <a name="-dcs"></a>-DC

Zadejte řadiče domény, proti kterými se má testovat.

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

#### <a name="-displayinformativemessage"></a>-DisplayInformativníZpráva

Příznak, který umožňuje zobrazení zprávy o účelu této funkce.

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

Vrátí výsledek této diagnózy ve formě objektu PSObject.
Není nutné specifikovat během ruční interakce s tímto nástrojem.

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

Označuje, zda jsou pověření, která uživatel zadali, platná.
Není nutné specifikovat během ruční interakce s tímto nástrojem.

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
Další informace naleznete vhttps://go.microsoft.com/fwlink/?LinkID=113216)tématu about_CommonParameters ( .
