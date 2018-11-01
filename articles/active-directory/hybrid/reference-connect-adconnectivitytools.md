---
title: 'Azure AD Connect: Referenční informace prostředí PowerShell ADConnectivityTools | Dokumentace Microsoftu'
description: Tento dokument obsahuje referenční informace pro modul Powershellu ADConnectivityTools.psm1.
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b0827035cc63f40094ab7b9d760c636914c71ec0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633884"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: Referenční informace prostředí PowerShell ADConnectivityTools
Následující dokumentace obsahuje referenční informace pro modul Powershellu ADConnectivityTools.psm1, která je součástí služby Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Potvrďte DnsConnectivity

### <a name="synopsis"></a>SYNOPSE
Zjišťuje místní problémů s Dns.

### <a name="syntax"></a>SYNTAXE

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Spustí místní testy připojení Dns.
Aby bylo možné nakonfigurovat konektor služby Active Directory, uživatel musí mít obě překlad názvů pro he\she doménové struktury se pokouší připojit k stejně jako v řadičích domény přidružené k této doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Určuje název doménové struktury pro porovnání výsledků testů.

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

#### <a name="-dcs"></a>-Řadiče domény
Určení řadiče domény pro porovnání výsledků testů.

```yaml
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
Vrátí výsledek této diagnostiky ve formuláři PSObject.
Není nutné během ruční interakce s tímto nástrojem.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Potvrďte ForestExists

### <a name="synopsis"></a>SYNOPSE
Určuje, jestli existuje zadané doménové struktury.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Dotazy DNS serveru pro IP adresy přidružené k doménové struktuře.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Určuje název doménové struktury pro porovnání výsledků testů.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Potvrďte FunctionalLevel

### <a name="synopsis"></a>SYNOPSE
Ověří úroveň funkčnosti doménové struktury AD.

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
Ověřuje, že funkční úroveň doménové struktury AD se rovná nebo překračuje danou MinAdForestVersion (WindowsServer2003).
Mohou být vyžádány účet (doména\uživatelské jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Cílové doménové struktuře.
Výchozí hodnota je doménová struktura aktuálně přihlášeného uživatele.

```yaml
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

```yaml
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
Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášen k počítači, a ne žádost o vlastních přihlašovacích údajů od uživatele.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Potvrďte NetworkConnectivity

### <a name="synopsis"></a>SYNOPSE
Zjistí problémy s připojením k místní síti.

### <a name="syntax"></a>SYNTAXE

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Spustí testy, připojení k místní síti.

Pro místní sítě testy AAD Connect musí být schopen komunikovat s řadiči domény s názvem na portech 53 (DNS), 88 (Kerberos) a 389 (LDAP) většina organizací spouští DNS na svých řadičích domény, což je důvod, proč tento test je nyní integrována.
Port 53 by měl přeskočit, pokud nebyl zadán jiný server DNS.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-dcs"></a>-Řadiče domény
Určení řadiče domény pro porovnání výsledků testů.

```yaml
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
Pokud uživatel není pomocí služby DNS, které jsou poskytované sítě služby Active Directory / přihlášení řadiče domény a pak he\she chtít přeskočit kontroly port 53. Uživatel musí být stále nemůže přeložit _.ldap._tcp. \<forestfqdn\> v pořadí pro konfiguraci konektoru služby Active Directory proběhla úspěšně.

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
Vrátí výsledek této diagnostiky ve formuláři PSObject.
Není nutné během ruční interakce s tímto nástrojem.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Potvrďte TargetsAreReachable

### <a name="synopsis"></a>SYNOPSE
Určuje, zda zadané doménové struktury a její přidružené řadiče domény dostupný.

### <a name="syntax"></a>SYNTAXE

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>POPIS
Spuštění testů (zda počítač může dosáhnout cílového počítače prostřednictvím sítě nebo Internetu) "příkaz ping"

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Určuje název doménové struktury pro porovnání výsledků testů.

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

#### <a name="-dcs"></a>-Řadiče domény
Určení řadiče domény pro porovnání výsledků testů.

```yaml
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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Potvrďte ValidDomains

### <a name="synopsis"></a>SYNOPSE
Ověřte, že jsou dostupné domény v získané plně kvalifikovaný název domény doménové struktury

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
Ověřte, že všechny domény v získané plně kvalifikovaný název domény doménové struktury jsou dostupné v pokusu o načtení DomainGuid a DomainDN.
Mohou být vyžádány účet (doména\uživatelské jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PŘÍKLAD 3
```
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Cílové doménové struktuře.

```yaml
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

```yaml
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
Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášen k počítači, a ne žádost o vlastních přihlašovacích údajů od uživatele.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Potvrďte ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SYNOPSE
Ověří, pokud má uživatel přihlašovací údaje podnikového správce.

### <a name="syntax"></a>SYNTAXE

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Vyhledá, pokud za předpokladu, že uživatel má přihlašovací údaje podnikového správce.
Mohou být vyžádány účet (doména\uživatelské jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášen k počítači, a ne žádost o vlastních přihlašovacích údajů od uživatele.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SYNOPSE
Načte DomainFQDN z účtu a hesla kombinaci.

### <a name="syntax"></a>SYNTAXE

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Pokusí se získat objekt domainFQDN ze zadaných přihlašovacích údajů.
Pokud domainFQDN je platný, že domainfqdnname nebo RootDomainName bude vrácen, v závislosti na výběru uživatele.
Mohou být vyžádány účet (doména\uživatelské jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType
Požadovaný typ dat, která budou načítat.
Aktuálně se omezuje na "DomainFQDNName" nebo "RootDomainName".

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

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášen k počítači, a ne žádost o vlastních přihlašovacích údajů od uživatele.

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

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError
Pomocný parametr použije funkce spuštění NetworkConnectivityDiagnosisTools

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SYNOPSE
Načte ForestFQDN z účtu a hesla kombinaci.

### <a name="syntax"></a>SYNTAXE

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Pokusí se získat ForestFQDN ze zadaných přihlašovacích údajů.
Mohou být vyžádány účet (doména\uživatelské jméno) a heslo.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Cílové doménové struktuře. Výchozí hodnota je doméně aktuálně přihlášeného uživatele.

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

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
Funkce použije přihlašovací údaje uživatele, který je aktuálně přihlášen k počítači, a ne žádost o vlastních přihlašovacích údajů od uživatele.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SYNOPSE
Hlavní funkci.

### <a name="syntax"></a>SYNTAXE

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>POPIS
Spuštění k dispozici mechanismy, které ověřují přihlašovací údaje služby AD jsou platné.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Cílové doménové struktuře.

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

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount
Pro vlastní instalace: Příznak, který je $True pokud uživatel vybral "Vytvořit nový účet AD" v okně Průvodce AADConnect účet doménové struktury služby AD.
$False Pokud uživatel vybral "Použít existující účet AD".
Hodnota této proměnné pro expresní instalace: Musí být $True pro expresní instalace.

```yaml
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
Parametr, který předem naplní pole uživatelské jméno při požadavku na přihlašovací údaje uživatele.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SYNOPSE
Testuje, hlavní funkci pro připojení k síti.

### <a name="syntax"></a>SYNTAXE

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>POPIS
Spustí testy, připojení k místní síti.

### <a name="examples"></a>PŘÍKLADY

#### <a name="example-1"></a>PŘÍKLAD 1
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>PŘÍKLAD 2
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Doménové struktury
Určuje název doménové struktury pro porovnání výsledků testů.

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

#### <a name="-credentials"></a>-Credentials
Uživatelské jméno a heslo uživatele, který spustil test.
Vyžaduje stejnou úroveň oprávnění, která se vyžaduje pro spuštění Průvodce připojení Azure AD.

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

#### <a name="-logfilelocation"></a>-Parametrů LogFileLocation
Určuje, umístění souboru protokolu, který bude obsahovat výstup této funkce.

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

#### <a name="-dcs"></a>-Řadiče domény
Určení řadiče domény pro porovnání výsledků testů.

```yaml
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
Příznak, který umožňuje zobrazení zprávy o účelu této funkce.

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
Vrátí výsledek této diagnostiky ve formuláři PSObject.
Není potřeba zadávat během ruční interakce s tímto nástrojem.

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

#### <a name="-validcredentials"></a>-ValidCredentials
Určuje, jestli uživatel zadal přihlašovací údaje jsou platné.
Není potřeba zadávat během ruční interakce s tímto nástrojem.

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
Další informace najdete v části about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
