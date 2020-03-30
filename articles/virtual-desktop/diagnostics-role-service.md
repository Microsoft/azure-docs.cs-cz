---
title: Problémy s diagnostikou virtuální plochy Windows – Azure
description: Jak diagnostikovat problémy pomocí funkce diagnostiky virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254258"
---
# <a name="identify-and-diagnose-issues"></a>Identifikace a diagnostika problémů

Windows Virtual Desktop nabízí diagnostickou funkci, která umožňuje správci identifikovat problémy prostřednictvím jediného rozhraní. Role virtuální plochy systému Windows zaznamenávají diagnostickou aktivitu vždy, když uživatel komunikuje se systémem. Každý protokol obsahuje relevantní informace, jako jsou role virtuální plochy systému Windows zapojené do transakce, chybové zprávy, informace o klientovi a informace o uživateli. Diagnostické aktivity jsou vytvářeny akcemi koncového uživatele i správy a lze je rozdělit do tří hlavních segmentů:

* Aktivity odběru informačního kanálu: koncový uživatel aktivuje tyto aktivity vždy, když se pokusí připojit k jejich informačnímu kanálu prostřednictvím aplikací vzdálené plochy společnosti Microsoft.
* Aktivity připojení: koncový uživatel aktivuje tyto aktivity při každém pokusu o připojení k ploše nebo aplikaci RemoteApp prostřednictvím aplikací vzdálené plochy společnosti Microsoft.
* Aktivity správy: Správce aktivuje tyto aktivity vždy, když provádí operace správy v systému, jako je vytváření fondů hostitelů, přiřazování uživatelů ke skupinám aplikací a vytváření přiřazení rolí.
  
Připojení, která nedosáhnou windows virtuální plochy se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuální plochy systému Windows. Problémy s připojením virtuální plochy systému Windows mohou nastat, když se u koncového uživatele vyskytnou problémy s připojením k síti.

Chcete-li začít, [stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který chcete použít v relaci PowerShellu, pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnostika problémů s PowerShellem

Diagnostika virtuální plochy systému Windows používá pouze jednu rutinu prostředí PowerShell, ale obsahuje mnoho volitelných parametrů, které pomáhají zúžit a izolovat problémy. V následujících částech jsou uvedeny rutiny, které můžete spustit pro diagnostiku problémů. Většinu filtrů lze použít společně. Hodnoty uvedené v závorkách, například `<tenantName>`, by měly být nahrazeny hodnotami, které platí pro vaši situaci.

>[!IMPORTANT]
>Funkce diagnostiky je určen pro řešení potíží pro jednoho uživatele. Všechny dotazy používající PowerShell musí obsahovat parametry *-UserName* nebo *-ActivityID.* Pro monitorování možností použijte Log Analytics. Další informace o tom, jak odesílat diagnostická data do pracovního prostoru, najdete v [tématu Použití analýzy protokolů pro funkci diagnostiky.](diagnostics-log-analytics.md) 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrování diagnostických aktivit podle uživatele

Parametr **-UserName** vrátí seznam diagnostických aktivit iniciovaných zadaným uživatelem, jak je znázorněno v následující části rutiny příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametr **-UserName** lze také kombinovat s dalšími volitelnými parametry filtrování.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrování diagnostických aktivit podle času

Vrácený seznam diagnostických aktivit můžete filtrovat pomocí parametrů **-StartTime** a **-EndTime.** Parametr **-StartTime** vrátí seznam diagnostických aktivit počínaje určitým datem, jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Parametr **-EndTime** lze přidat do rutiny s parametrem **-StartTime** a určit konkrétní časové období, pro které chcete získat výsledky. Následující příklad rutiny vrátí seznam diagnostických aktivit od 1 srpna do 10 srpna.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametry **-StartTime** a **-EndTime** lze také kombinovat s dalšími volitelnými parametry filtrování.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrování diagnostických aktivit podle typu aktivity

Diagnostické aktivity můžete také filtrovat podle typu aktivity pomocí parametru **-ActivityType.** Následující rutina vrátí seznam připojení koncových uživatelů:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Následující rutina vrátí seznam úloh správy správce:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Rutina **Get-RdsDiagnosticActivities** aktuálně nepodporuje určení informačního kanálu jako typu ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrování diagnostických aktivit podle výsledku

Vrácený seznam diagnostických aktivit můžete filtrovat podle výsledku pomocí parametru **-Outcome.** Následující příklad rutiny vrátí seznam úspěšných diagnostických aktivit.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Následující příklad rutiny vrátí seznam neúspěšných diagnostických aktivit.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Parametr **-Outcome** lze také kombinovat s dalšími volitelnými parametry filtrování.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Načtení konkrétní diagnostické aktivity podle ID aktivity

Parametr **-ActivityId** vrátí konkrétní diagnostickou aktivitu, pokud existuje, jak je znázorněno v následující příklad rutiny.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Zobrazit chybové zprávy pro neúspěšnou aktivitu podle ID aktivity

Chcete-li zobrazit chybové zprávy pro neúspěšnou aktivitu, je nutné spustit rutinu s parametrem **-Detailed.** Seznam chyb můžete zobrazit spuštěním rutiny **Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Načíst podrobné diagnostické aktivity

**Parametr -Detailed** poskytuje další podrobnosti pro každou vrácenou diagnostickou aktivitu. Formát pro každou aktivitu se liší v závislosti na typu aktivity. **Parametr -Detailed** lze přidat do **libovolného dotazu Get-RdsDiagnosticActivities,** jak je znázorněno v následujícím příkladu.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Běžné chybové scénáře

Chybové scénáře jsou rozděleny do kategorií interní služby a externí windows virtual desktop.

* Vnitřní problém: Určuje scénáře, které nemůže být zmírněny správcem klienta a je třeba je vyřešit jako problém podpory. Při poskytování zpětné vazby prostřednictvím [technické komunity virtuálních desktopů windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)uveďte ID aktivity a přibližný časový rámec, kdy k problému došlo.
* Externí problém: vztahují se na scénáře, které mohou být zmírněny správcem systému. Jedná se o externí Windows Virtual Desktop.

V následující tabulce jsou uvedeny běžné chyby, na které mohou správci narazit.

>[!NOTE]
>Tento seznam obsahuje většinu běžných chyb a je aktualizován na pravidelné kadence. Chcete-li zajistit, že máte nejaktuálnější informace, nezapomeňte se na tento článek podívat alespoň jednou za měsíc.

### <a name="external-management-error-codes"></a>Kódy chyb externí správy

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|3|Neoprávněný přístup|Uživatel, který se pokusil spustit rutinu prostředí PowerShell pro správu, k tomu buď nemá oprávnění, nebo nesprávně zadal své uživatelské jméno.|
|1000|tenanta notfound|Zadaný název klienta se neshoduje s žádnými existujícími klienty. Zkontrolujte název klienta pro překlepy a akci opakujte.|
|1006|TenantCannotCannotRemovedHasSessionHostPools|Klienta nelze odstranit, pokud obsahuje objekty. Nejprve odstraňte fondy hostitelů relací a akci opakujte.|
|2000|HostPoolNotFound|Zadaný název fondu hostitelů neodpovídá žádnému existujícímu fondu hostitelů. Zkontrolujte název fondu hostitele pro překlepy a akci opakujte.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Fond hostitelů nelze odstranit, pokud obsahuje objekty. Nejdřív odeberte všechny skupiny aplikací ve fondu hostitelů.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Před odstraněním fondu hostitelů relací nejprve odeberte všechny hostitele relací.|
|5001|SessionHostNotFound|Dotazovaný hostitel relace může být offline. Zkontrolujte stav fondu hostitelů.|
|5008|SessionHostUserSessionsExist |Před provedením zamýšlené aktivity správy je nutné odhlásit všechny uživatele na hostiteli relace.|
|6000|AppGroupNotFound|Zadaný název skupiny aplikací neodpovídá žádné existující skupině aplikací. Zkontrolujte název skupiny aplikací pro překlepy a zkuste to znovu.|
|6022|RemoteAppNotFound|Zadaný název RemoteApp neodpovídá žádnému vzdálenému aplikaci. Zkontrolujte název aplikace RemoteApp pro překlepy a akci opakujte.|
|6010|PublishedItemsExist|Název prostředku, který se pokoušíte publikovat, je stejný jako prostředek, který již existuje. Změňte název prostředku a akci opakujte.|
|7002|NázevNotValidWhiteSpace|Nepoužívejte prázdné znaky v názvu.|
|8000|Neplatnýoboru oboru authorizationrolescope|Zadaný název role neodpovídá žádným existujícím názvům rolí. Zkontrolujte název role pro překlepy a akci opakujte. |
|8001|UserNotFound |Zadané uživatelské jméno se neshoduje s žádnými existujícími uživatelskými jmény. Zkontrolujte název překlepů a akci opakujte.|
|8005|Uživatelnotfoundinaad |Zadané uživatelské jméno se neshoduje s žádnými existujícími uživatelskými jmény. Zkontrolujte název překlepů a akci opakujte.|
|8008|TenantConsentRequired|Postupujte podle pokynů [zde](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) poskytnout souhlas pro svého nájemce.|

### <a name="external-connection-error-codes"></a>Kódy chyb externího připojení

|Číselný kód|Kód chyby|Navrhované řešení|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Hostitel relace není správně připojen ke službě Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnv pořádku|Připojení se nezdařilo, protože hostitel relace není k dispozici. Zkontrolujte stav hostitele relace.|
|-2146233088|ConnectionFailedClientDisconnect|Pokud se tato chyba zobrazuje často, zkontrolujte, zda je počítač uživatele připojen k síti.|
|-2146233088|ConnectionFailedNoHealthyRdshK dispozici|Relace, ke které se hostitelský uživatel pokusil připojit, není v pořádku. Ladění virtuálního počítače.|
|-2146233088|ConnectionFailedUserNotAuthorized|Uživatel nemá oprávnění k přístupu k publikované aplikaci nebo ploše. Chyba se může zobrazit po odebrání publikovaných prostředků správcem. Požádejte uživatele, aby aktualizoval informační kanál v aplikaci Vzdálená plocha.|
|2|Soubor nebyl nalezen.|Aplikace, ke které se uživatel pokusil získat přístup, je nesprávně nainstalována nebo nastavena na nesprávnou cestu.|
|3|Neplatná pověření|Uživatelské jméno nebo heslo, které uživatel zadal, se neshoduje s existujícími uživatelskými jmény nebo hesly. Zkontrolujte pověření pro překlepy a akci opakujte.|
|8|Přerušeno připojení|Připojení mezi klientem a bránou nebo serverem bylo přerušeno. Není nutná žádná akce, pokud se to nestane neočekávaně.|
|14|Neočekávané odpojení sítě|Připojení k síti bylo přerušeno. Požádejte uživatele, aby se znovu připojil.|
|24|ReverseConnectFailed|Virtuální počítač hostitele nemá žádnou přímou viditelnost do služby Brána VP. Ujistěte se, že lze přeložit IP adresu brány.|

## <a name="next-steps"></a>Další kroky

Další informace o rolích v rámci virtuální plochy Windows najdete v [tématu Prostředí Windows Virtual Desktop .](environment-setup.md)

Seznam dostupných rutin prostředí PowerShell pro virtuální plochu Windows naleznete v [odkazu na powershell](/powershell/windows-virtual-desktop/overview).
