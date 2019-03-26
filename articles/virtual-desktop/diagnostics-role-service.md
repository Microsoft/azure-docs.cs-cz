---
title: Identifikujte problémy s funkcí diagnostiky Windows Virtual Desktop Preview – Azure
description: Popisuje funkce Diagnostika Preview virtuální plochy Windows a jak ji používat.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418631"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifikace problémů pomocí diagnostické funkce

Virtuální Desktop Preview Windows nabízí diagnostické funkce, která umožňuje správci umožní identifikovat problémy s pomocí jednoho rozhraní. Role virtuálního klienta Windows protokolování diagnostiky činnosti, pokaždé, když uživatel komunikuje s systému. Každý protokol obsahuje důležité informace, třeba virtuální plochy Windows role zapojené do transakce, chybové zprávy, informace o tenantovi a informace o uživateli. Diagnostické aktivity se vytvoří s koncovým uživatelem i akce správy a lze rozdělit do tří hlavních bloků:

* Informační kanál aktivit předplatného: koncový uživatel spustí tyto aktivity při každém pokusu o připojení k jejich kanálu pomocí aplikace Vzdálená plocha od Microsoftu.
* Připojení aktivity: koncový uživatel spustí tyto aktivity pokaždé, když se pokusí připojit k počítači nebo vzdálené aplikace RemoteApp prostřednictvím aplikace Vzdálená plocha od Microsoftu.
* Aktivity správy: správce spustí tyto aktivity pokaždé, když se provádějí operace správy systému, jako je například vytvoření fondů hostitele, přiřazování uživatelů do skupiny aplikací a vytváření přiřazení rolí.
  
Připojení, která nedostanou virtuální plochy Windows nezobrazí ve výsledcích diagnostiky protože samotnou službu role Diagnostika je součástí virtuální plochy Windows. Problémy s připojením k virtuálnímu klientovi Windows může dojít, když koncový uživatel dochází k problémům se síťovým připojením.

Abyste mohli začít, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

## <a name="diagnose-issues-with-powershell"></a>Diagnostikujte problémy pomocí Powershellu

Diagnostika virtuální plochy Windows používá pouze jeden rutiny Powershellu, ale obsahuje mnoho volitelné parametry, které vám pomůžou zúží a izolace potíží s. Rutiny můžete použít k diagnostice problémů v následujících částech. Většina filtrů lze použít společně. Hodnoty uvedené v závorkách, například `<tenantName>`, by měla být nahrazena hodnoty, které platí pro konkrétní situaci.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Získání diagnostiky aktivit ve vašem tenantovi

Diagnostické aktivity můžete načíst tak, že zadáte **Get-RdsDiagnosticActivities** rutiny. Tuto ukázkovou rutinu, vrátí se seznam diagnostických aktivit, seřazené od nejvíce nejstarší.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Stejně jako ostatní rutiny Windows Powershellu virtuální plochy musíte použít **- TenantName** parametr k určení názvu tenanta, kterou chcete použít pro váš dotaz. Název tenanta platí pro téměř všechny dotazy diagnostických aktivity.

### <a name="retrieve-detailed-diagnostic-activities"></a>Načíst podrobné diagnostické aktivity

**-Podrobné** parametr poskytuje další podrobnosti pro každou diagnostických aktivitu vrátila. Formát pro každé aktivity se liší v závislosti na jeho typu aktivity. **-Podrobné** parametr je přidat k libovolnému **Get-RdsDiagnosticActivities** zjistit, jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Načtení konkrétní diagnostické ID aktivity podle aktivity

**- ActivityId** vrátí parametr konkrétní diagnostické aktivitu, pokud existuje, jak je znázorněno v následujícím příkladu rutiny.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrovat diagnostických aktivity podle uživatele

**- UserName** parametr vrátí seznam diagnostických aktivity iniciované příslušným uživatelem zadaný, jak je znázorněno v následujícím příkladu rutiny.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**- UserName** parametr může být spojen s další volitelné parametry filtrování.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrování podle času diagnostických aktivity

Můžete filtrovat seznam vrácených diagnostických aktivit s **- StartTime** a **- EndTime** parametry. **- StartTime** parametr vrátí seznam diagnostických aktivit od konkrétní data, jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

**- EndTime** můžete přidat parametr do rutiny s **- StartTime** parametr určit konkrétní dobu, kterou chcete zobrazit výsledky pro. Tuto ukázkovou rutinu, vrátí se seznam diagnostických aktivit, které srpna 1 a 10. srpna.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**- StartTime** a **- EndTime** parametrů se dají kombinovat taky s další volitelné parametry filtrování.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrovat podle typu aktivity diagnostických aktivity

Můžete také filtrovat diagnostických aktivity podle typu aktivity s **- ActivityType** parametru. Následující rutina vrátí seznam připojení koncového uživatele:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Následující rutina vrátí seznam úloh správy pro správce:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** rutiny v současné době nepodporuje zadání kanálu jako ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrovat podle výsledku diagnostických aktivity

Seznam vrácených diagnostických aktivit můžete filtrovat podle výsledku s **– výsledek** parametru. Tuto ukázkovou rutinu, vrátí se seznam úspěšné diagnostických aktivity.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Tuto ukázkovou rutinu, vrátí se seznam neúspěšných aktivit diagnostiky.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**– Výsledek** parametr může být spojen s další volitelné parametry filtrování.

## <a name="common-error-scenarios"></a>Běžné scénáře chyba

Chyba scénáře jsou rozdělené do interní ke službě a externí virtuální plochy Windows.

* Vnitřní problém: Určuje scénáře, které není možné řešit správcem tenanta a musí být vyřešeny automatickým problém podpory. Kdy lístku zadejte ID aktivity, název klienta a přibližné časový rámec problém došlo k chybě.
* Externí problém: se vztahují na scénáře, které se dají zmírnit na správce systému. Toto jsou externí vzhledem k virtuálnímu klientovi Windows.

V následující tabulce najdete běžné chyby, které vaši správci tyto problémy.

>[!NOTE]
>Tato verze preview neobsahuje kompletní kategorizace chyb a budou aktualizovány v pravidelných intervalech. K zajištění, že máte nejnovější informace, nezapomeňte se podívat na tento článek alespoň jednou za měsíc.

### <a name="external-management-error-codes"></a>Kódy chyb externí správu

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|3|UnauthorizedAccess|Uživatel, který se pokouší spustit rutinu prostředí PowerShell pro správu nemá oprávnění k tomu nebo chybně své uživatelské jméno.|
|1000|TenantNotFound|Název tenanta, které jste zadali, neodpovídá žádné stávající klienty. Zkontrolujte název klienta pro překlepy a zkuste to znovu.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Klienta nejde odstranit, protože dlouho obsahuje objekty. Nejprve odstraňte hostitele fondy relaci a akci opakujte.|
|2000|HostPoolNotFound|Zadaný název hostitele fondu neodpovídá všechny existující fondy hostitele. Zkontrolujte název hostitele fondu typo a zkuste to znovu.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Fond hostitele nelze odstranit, tak dlouho, dokud obsahuje objekty. Nejprve odeberte všechny skupiny aplikací ve fondu hostitele.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Odebrání všech hostitelů relací nejprve před odstraněním fondu hostitele relace.|
|5001|SessionHostNotFound|Hostitel relace, které jste pomocí dotazu může být offline. Zkontrolujte stav fondu hostitele.|
|5008|SessionHostUserSessionsExist |Před spuštěním vašich aktivit zamýšlený správy musí odhlásit všechny uživatele na hostiteli relace.|
|6000|AppGroupNotFound|Název skupiny aplikací, které jste zadali, neodpovídá žádné existující skupiny aplikací. Zkontrolujte název skupiny aplikací pro překlepy a zkuste to znovu.|
|6022|RemoteAppNotFound|Zadaný název vzdálené aplikace RemoteApp neodpovídá žádné aplikace RemoteApp. Název vzdálené aplikace RemoteApp typo kontroly a zkuste to znovu.|
|6010|PublishedItemsExist|Název prostředku, který se snažíte publikování je stejný jako prostředek, který již existuje. Změnit název prostředku a zkuste to znovu.|
|7002|NameNotValidWhiteSpace|Nepoužívejte prázdné znaky v názvu.|
|8000|InvalidAuthorizationRoleScope|Název role, které jste zadali neodpovídá žádné existující názvy rolí. Zkontrolujte název role pro překlepy a zkuste to znovu. |
|8001|UserNotFound |Uživatelské jméno, které jste zadali, neodpovídá žádné existující uživatelská jména. Zkontrolujte název překlepy a zkuste to znovu.|
|8005|UserNotFoundInAAD |Uživatelské jméno, které jste zadali, neodpovídá žádné existující uživatelská jména. Zkontrolujte název překlepy a zkuste to znovu.|
|8008|TenantConsentRequired|Postupujte podle pokynů [tady](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) k poskytnutí souhlasu pro vašeho tenanta.|

### <a name="external-connection-error-codes"></a>Kódy chyb externí připojení

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Hostitel relace není správně připojený ke službě Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Připojení se nezdařila, protože hostitel relace není k dispozici. Zkontrolujte stav hostitele relace.|
|-2146233088|ConnectionFailedClientDisconnect|Pokud tuto chybu vidíte často, ujistěte se, že počítač uživatele je připojen k síti.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Relace, které hostitele uživatel se pokusil připojit k není v pořádku. Ladění virtuálního počítače.|
|-2146233088|ConnectionFailedUserNotAuthorized|Uživatel nemá oprávnění pro přístup k publikované aplikaci nebo plochy. Chyba může zobrazit po odebrání správce publikovaných prostředků. Požádejte uživatele, aby aktualizace informačního kanálu ve vzdálené plochy aplikace.|
|2|FileNotFound|Aplikace, kterou uživatel se pokusil o přístup je buď nesprávně nainstalovaná, nebo nastavte do nesprávné cesty.|
|3|InvalidCredentials|Uživatelské jméno nebo heslo zadané uživatelem zadané informace neodpovídají žádné existující uživatelská jména a hesla. Zkontrolujte přihlašovací údaje pro překlepy a zkuste to znovu.|
|8|ConnectionBroken|Připojení mezi klienta a brány nebo Server vyřadit. Pokud se stane neočekávaně vyžadována žádná akce.|
|14|UnexpectedNetworkDisconnect|Připojení k síti vyřadit. Požádejte uživatele znovu připojit.|
|24|ReverseConnectFailed|Hostitel virtuálního počítače nemá žádné přímé dohled Brána VP. Zkontrolujte, jestli se že dá vyřešit adresu IP brány.|

## <a name="next-steps"></a>Další postup

Další informace o rolích v rámci virtuální plochy Windows najdete v tématu [prostředí Preview virtuální plochy Windows](environment-setup.md).

Pokud chcete zobrazit seznam dostupných rutin Powershellu pro virtuální plochy Windows, najdete v článku [referenční informace prostředí PowerShell](/powershell/windows-virtual-desktop/overview).
