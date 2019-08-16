---
title: Identifikujte problémy s funkcí diagnostiky ve verzi Preview virtuálních počítačů s Windows – Azure
description: Popisuje funkci diagnostiky ve verzi Preview pro Windows Virtual Desktop a její použití.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: d6cde04d122d20c4aad5920c29995d9d33165163
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515519"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifikace problémů pomocí diagnostické funkce

Virtuální počítač s Windows ve verzi Preview nabízí funkci diagnostiky, která umožňuje správcům identifikovat problémy přes jedno rozhraní. Role virtuálních klientů Windows protokolují diagnostické aktivity pokaždé, když uživatel komunikuje se systémem. Každý protokol obsahuje relevantní informace, jako jsou například role virtuálních klientských počítačů s Windows, které jsou součástí transakce, chybové zprávy, informace o tenantovi a informace o uživateli. Diagnostické aktivity jsou vytvářeny pomocí akcí koncového uživatele i správy a lze je rozdělit do tří hlavních intervalů:

* Aktivity předplatného informačního kanálu: koncový uživatel tyto aktivity aktivuje pokaždé, když se pokusí připojit k informačnímu kanálu prostřednictvím Vzdálená plocha Microsoftch aplikací.
* Aktivity připojení: koncový uživatel tyto aktivity aktivuje pokaždé, když se pokusí připojit k desktopu nebo k vzdálené aplikaci RemoteApp prostřednictvím aplikace Vzdálená plocha Microsoft.
* Správcovské aktivity: správce tyto aktivity aktivuje při provádění operací správy v systému, jako je vytváření fondů hostitelů, přiřazování uživatelů ke skupinám aplikací a vytváření přiřazení rolí.
  
Připojení, která nedosáhnou virtuálního klienta Windows, se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuálního klienta Windows. Problémy s připojením k virtuálnímu počítači s Windows se můžou vyskytnout, když koncový uživatel dochází k problémům se síťovým připojením.

Pokud jste to ještě neudělali, [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu.

## <a name="diagnose-issues-with-powershell"></a>Diagnostika problémů s prostředím PowerShell

Diagnostika virtuálních počítačů s Windows používá jenom jednu rutinu prostředí PowerShell, ale obsahuje mnoho volitelných parametrů, které vám pomůžou zúžit a izolovat problémy. V následujících částech jsou uvedeny rutiny, které můžete spustit pro diagnostiku problémů. Většinu filtrů lze použít společně. Hodnoty uvedené v závorkách, například `<tenantName>`, by měly být nahrazeny hodnotami, které se vztahují na vaši situaci.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Načtení diagnostických aktivit ve vašem tenantovi

Diagnostické aktivity můžete načíst zadáním rutiny **Get-RdsDiagnosticActivities** . Následující příklad rutiny vrátí seznam diagnostických aktivit seřazených z většiny na nejméně poslední.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Podobně jako jiné rutiny PowerShellu pro virtuální počítače s Windows musíte použít parametr **-tenant** a zadat tak název tenanta, kterého chcete pro dotaz použít. Název tenanta je použitelný pro téměř všechny dotazy na diagnostické aktivity.

### <a name="retrieve-detailed-diagnostic-activities"></a>Načtení podrobných diagnostických aktivit

Parametr **-detailed** poskytuje další podrobnosti pro každou vrácenou diagnostickou aktivitu. Formát každé aktivity se liší v závislosti na typu aktivity. Parametr **-detailed** lze přidat do jakéhokoli dotazu **Get-RdsDiagnosticActivities** , jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Načtení konkrétní diagnostické aktivity podle ID aktivity

Parametr **-ActivityId** vrátí konkrétní diagnostickou aktivitu, pokud existuje, jak je znázorněno v následujícím příkladu rutiny.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Zobrazit chybové zprávy pro neúspěšnou aktivitu podle ID aktivity

Chcete-li zobrazit chybové zprávy pro aktivitu, která selhala, je nutné spustit rutinu s parametrem **-detailed** . Seznam chyb můžete zobrazit spuštěním rutiny **Select-Object** .

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrovat diagnostické aktivity podle uživatele

Parametr **-username** vrátí seznam diagnostických aktivit iniciované zadaným uživatelem, jak je znázorněno v následujícím příkladu rutiny.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametr **-username** lze také kombinovat s dalšími volitelnými parametry filtrování.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrovat diagnostické aktivity podle času

Můžete filtrovat seznam vrácených diagnostických aktivit s parametry **-čas_spuštění** a **-čas_ukončení** . Parametr **-StartTime** vrátí seznam diagnostických aktivit od konkrétního data, jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Parametr **-čas_ukončení** lze přidat do rutiny s parametrem **-StartTime** pro určení konkrétního časového období, pro které chcete získat výsledky. Následující příklad rutiny vrátí seznam diagnostických aktivit od 1. srpna do 10.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametry **-čas_spuštění** a **-čas_ukončení** lze také kombinovat s jinými volitelnými parametry filtrování.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrovat diagnostické aktivity podle typu aktivity

Diagnostické aktivity můžete také filtrovat podle typu aktivity s parametrem **-ActivityType** . Následující rutina vrátí seznam připojení koncových uživatelů:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Následující rutina vrátí seznam úloh správy správců:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Rutina **Get-RdsDiagnosticActivities** v současné době nepodporuje určení kanálu jako ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrovat diagnostické aktivity podle výsledku

Seznam vrácených diagnostických aktivit můžete filtrovat podle výsledku s parametrem **-výsledek** . Následující příklad rutiny vrátí seznam úspěšných diagnostických aktivit.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Následující příklad rutiny vrátí seznam neúspěšných diagnostických aktivit.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Parametr **-výsledek** lze také kombinovat s jinými volitelnými parametry filtrování.

## <a name="common-error-scenarios"></a>Běžné chybové scénáře

Scénáře chyb jsou rozdělené do kategorií interní pro službu a externí pro virtuální počítače s Windows.

* Vnitřní problém: Určuje scénáře, které nelze zmírnit správcem tenanta a které je třeba vyřešit jako problém podpory. Při poskytování zpětné vazby prostřednictvím [odborné komunity pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)uveďte ID aktivity a přibližný časový rámec, kdy k problému došlo.
* Externí problém: vztah ke scénářům, které může správce systému zmírnit. Jedná se o externí funkce pro virtuální počítače s Windows.

V následující tabulce jsou uvedeny běžné chyby, ke kterým můžou správci běžet.

>[!NOTE]
>Tato verze Preview neobsahuje úplnou kategorizaci chyb a aktualizuje se pravidelně. Abyste měli jistotu, že máte nejaktuálnější informace, nezapomeňte se vrátit k tomuto článku aspoň jednou za měsíc.

### <a name="external-management-error-codes"></a>Kódy chyb externí správy

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|3|UnauthorizedAccess|Uživatel, který se pokusil spustit rutinu prostředí PowerShell pro správu, nemá oprávnění k tomu, aby to provedl nebo nedokázal zadat své uživatelské jméno.|
|1000|TenantNotFound|Název tenanta, který jste zadali, se neshoduje s žádnými stávajícími klienty. Zkontrolujte název tenanta pro překlepy a zkuste to znovu.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Tenanta nemůžete odstranit, pokud obsahuje objekty. Nejprve odstraňte fondy hostitelů relací a potom akci opakujte.|
|2000|HostPoolNotFound|Název fondu hostitelů, který jste zadali, se neshoduje s žádnými existujícími fondy hostitelů. Přečtěte si název fondu hostitelů pro překlepy a zkuste to znovu.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Fond hostitelů nemůžete odstranit, pokud obsahuje objekty. Odeberte nejprve všechny skupiny aplikací ve fondu hostitelů.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Před odstraněním fondu hostitele relace odeberte nejprve všechny hostitele relací.|
|5001|SessionHostNotFound|Hostitel relace, na který jste dotazováni, může být offline. Ověřte stav fondu hostitelů.|
|5008|SessionHostUserSessionsExist |Před provedením zamýšlené aktivity správy musíte odhlásit všechny uživatele na hostiteli relace.|
|6000|AppGroupNotFound|Název skupiny aplikací, který jste zadali, se neshoduje s žádnými stávajícími skupinami aplikací. Přečtěte si název skupiny aplikací pro překlepy a zkuste to znovu.|
|6022|RemoteAppNotFound|Název aplikace RemoteApp, který jste zadali, se neshoduje s žádnými aplikacemi RemoteApp. Zkontrolujte překlepy v názvu aplikace RemoteApp a akci opakujte.|
|6010|PublishedItemsExist|Název prostředku, který se pokoušíte publikovat, je stejný jako prostředek, který už existuje. Změňte název prostředku a zkuste to znovu.|
|7002|NameNotValidWhiteSpace|V názvu nepoužívejte prázdné znaky.|
|8000|InvalidAuthorizationRoleScope|Název role, kterou jste zadali, se neshoduje s žádnými stávajícími názvy rolí. Zkontrolujte název role pro překlepy a zkuste to znovu. |
|8001|UserNotFound |Uživatelské jméno, které jste zadali, neodpovídá žádnému existujícímu uživatelskému jménu. Přečtěte si název pro překlepy a zkuste to znovu.|
|8005|UserNotFoundInAAD |Uživatelské jméno, které jste zadali, neodpovídá žádnému existujícímu uživatelskému jménu. Přečtěte si název pro překlepy a zkuste to znovu.|
|8008|TenantConsentRequired|Podle [zde](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) uvedených pokynů poskytněte souhlas pro vašeho tenanta.|

### <a name="external-connection-error-codes"></a>Kódy chyb externího připojení

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Hostitel relace není správně připojen ke službě Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Připojení se nezdařilo, protože hostitel relace není k dispozici. Ověřte stav hostitele relace.|
|-2146233088|ConnectionFailedClientDisconnect|Pokud se tato chyba zobrazí často, ujistěte se, že je počítač uživatele připojený k síti.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Relace, ke které se hostitelský uživatel pokusil připojit, není v pořádku. Ladit virtuální počítač.|
|-2146233088|ConnectionFailedUserNotAuthorized|Uživatel nemá oprávnění pro přístup k publikované aplikaci nebo k ploše. Tato chyba se může zobrazit po odebrání publikovaných prostředků správcem. Požádejte uživatele, aby tento informační kanál aktualizoval v aplikaci Vzdálená plocha.|
|2|FileNotFound|Aplikace, ke které se uživatel pokusil získat přístup, je buď nesprávně nainstalovaná, nebo nastavená na nesprávnou cestu.|
|3|InvalidCredentials|Uživatelské jméno nebo heslo, které uživatel zadal, neodpovídá žádnému z existujících uživatelských jmen a hesel. Zkontrolujte přihlašovací údaje pro překlepy a zkuste to znovu.|
|8|ConnectionBroken|Spojení mezi klientem a bránou nebo serverem bylo vyřazeno. Není nutná žádná akce, pokud dojde k neočekávanému chování.|
|14|UnexpectedNetworkDisconnect|Připojení k síti bylo vyřazeno. Požádejte uživatele, aby se připojil znovu.|
|24|ReverseConnectFailed|Hostitelský virtuální počítač nemá žádný přímý dohled, který by Brána VP. Zajistěte, aby se IP adresa brány mohla přeložit.|

## <a name="next-steps"></a>Další postup

Další informace o rolích v rámci virtuálních počítačů s Windows najdete v tématu [prostředí pro virtuální počítače s Windows ve verzi Preview](environment-setup.md).

Seznam dostupných rutin PowerShellu pro virtuální počítač s Windows najdete v referenčních informacích k [prostředí PowerShell](/powershell/windows-virtual-desktop/overview).
