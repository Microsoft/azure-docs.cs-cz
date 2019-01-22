---
title: Řešení potíží a protokolování v Azure AD hesla protection ve verzi preview
description: Vysvětlení ochrana hesel Azure AD ve verzi preview, protokolování a řešení běžných potíží
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 008fba2fd6052ac9043de6ec217cb71d17f3ecce
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427096"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Verze Preview: Azure AD hesla ochrany monitorování, vytváření sestav a řešení potíží

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po nasazení ochrany hesla Azure AD monitorování a vytváření sestav jsou základní úlohy. Tento článek obsahuje podrobnosti kterých víte, kde každá služba protokoluje informace a hlášení týkající se použití ochrany hesla Azure AD.

## <a name="on-premises-logs-and-events"></a>Místní protokoly a události

### <a name="dc-agent-admin-log"></a>Protokol správce agenta řadiče domény

V každém řadiči domény k softwaru agenta služby řadiče domény zapíše výsledky ověření jeho heslo (a další stav) do místního protokolu událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Události jsou protokolovány podle různých komponent agenta řadiče domény pomocí následující rozsahy:

|Komponenta |Rozsah ID událostí|
| --- | --- |
|Knihovny dll filtru hesel agenta řadiče domény| 10000-19999|
|Řadič domény agenta služby hostitelského procesu| 20000-29999|
|Logiku ověřování zásad služby agenta řadiče domény| 30000-39999|

Pro operace ověření hesla se podařila je obvykle jednu událost se zaznamená z knihovny dll filtru hesel agenta řadiče domény. Pro neúspěšného operace ověření hesla, obecně existují dvě události protokolované, jeden z řadiče domény služba agent a jeden z knihovny dll filtru hesel agenta DC.

Diskrétní události zaznamenat tyto situace se protokolují, podle následující faktory:

* Určuje, zda se dané heslo nastavit nebo změnit.
* Určuje, zda ověřování daného hesla úspěšné nebo neúspěšné.
* Určuje, zda ověření se nezdařilo z důvodu Microsoft globální zásady vs zásad organizace.
* Určuje, zda režim jenom pro auditování je momentálně zapnuto nebo vypnuto pro aktuální zásady hesel.

Klíč události související s hesly ověření jsou následující:

|   |Změna hesla |Nastavení hesla|
| --- | :---: | :---: |
|Úspěch |10014 |10015|
|Selhání (neprošel zákazníka zásady hesel)| 10016, 30002| 10017, 30003|
|Selhání (nebyla úspěšná. zásady hesel Microsoft)| 10016, 30004| 10017, 30005|
|Jenom pro auditování Pass (by se nezdařil zásady hesel zákazníka)| 10024, 30008| 10025, 30007|
|Jenom pro auditování Pass (by se nezdařil zásady hesel Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Příchozí hesla jsou ověřena seznamu globální heslo Microsoft. Pokud se nezdaří, se neprovádí žádné další zpracování. Toto je stejné chování jako provedla změny hesel v Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Ukázka protokolu událostí zprávy pro ID události 10014 (hesla se podařila set)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázka protokolu událostí zprávy pro ID události 10017 a 30003 (nastavení hesel)

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Ukázka protokolu událostí zprávy pro ID události 30001 (hesla přijata z důvodu žádné zásady, které jsou k dispozici)

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Ukázka protokolu událostí zprávy pro ID události 30006 (nové zůstala vynucená zásada)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Řadič domény agenta operační protokol

Služba agenta řadiče domény navíc zaznamená provozní související události do protokolu následující:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>Protokol trasování agenta řadiče domény

Služba agenta řadiče domény můžete také podrobné úroveň ladění trasování událostí v protokolu do následující protokol:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokolování trasování je ve výchozím nastavení zakázána.

> [!WARNING]
>  Při povolení protokolu trasování přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

#### <a name="dc-agent-text-logging"></a>Řadič domény agenta protokolování textu

Řadič domény agenta služby lze nastavit k zápisu do protokolu text tak, že nastavíte následující hodnotu registru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Protokolování textu je standardně zakázáno. Restartovat službu agenta řadiče domény je požadovaná pro tuto hodnotu, než se projeví změny. Pokud povolena řadiče domény Služba agenta bude zapisovat do souboru protokolu, která je umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textový protokol obdrží stejnou úroveň ladění položky, které může být zaznamenány do protokolu trasování, ale je obvykle ve formátu snadněji zkontrolovat a analyzovat.

> [!WARNING]
> Při povolení tohoto protokolu přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

### <a name="azure-ad-password-protection-proxy-service"></a>Služba Azure AD hesla protection proxy

#### <a name="proxy-service-event-logs"></a>Protokoly událostí service proxy

Proxy služba generuje minimální sadu událostí k následující protokoly událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Službu Proxy můžete také podrobné úroveň ladění trasování událostí v protokolu do následující protokol:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Protokolování trasování je ve výchozím nastavení zakázána.

> [!WARNING]
> Při povolení protokolu trasování přijímá k velkému počtu událostí a to může mít vliv na výkon hostitele proxy serveru. Proto tento protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

#### <a name="proxy-service-text-logging"></a>Protokolování textu service proxy

Proxy služby mohou být konfigurovány pro zápis do protokolu text tak, že nastavíte následující hodnotu registru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Protokolování textu je standardně zakázáno. Restartování služby serveru Proxy je požadovaná pro tuto hodnotu, než se projeví změny. Pokud povolena proxy serveru, kdy bude služba zapisovat do souboru protokolu, která je umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textový protokol obdrží stejnou úroveň ladění položky, které může být zaznamenány do protokolu trasování, ale je obvykle ve formátu snadněji zkontrolovat a analyzovat.

> [!WARNING]
> Při povolení tohoto protokolu přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

#### <a name="powershell-cmdlet-logging"></a>Protokolování rutiny prostředí PowerShell

Většinu rutin prostředí Powershell Azure AD hesla ochrany bude zapisovat do protokolu textu umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Pokud dojde k chybě rutiny a příčina a řešení není snadno pozná, tyto textové protokoly mohou také brán ohled.

### <a name="emergency-remediation"></a>Nouzový nápravy

Pokud dojde k situaci, kdy služba agenta DC způsobuje problémy, službu agenta řadiče domény může okamžitě ukončena. Dll filtru hesel agenta DC stále pokusí volat službu bez spuštění a budou protokolovat události upozornění (10012, 10013), ale během této doby jsou přijímány příchozí všechna hesla. Služba agenta řadiče domény může také být nakonfigurována prostřednictvím Windows správce řízení služeb s typem spuštění "Zakázáno" podle potřeby.

### <a name="performance-monitoring"></a>Sledování výkonu

Software služby agenta řadiče domény nainstaluje objekt čítače výkonu s názvem **ochrana hesel Azure AD**. Následující čítače výkonu jsou aktuálně k dispozici:

|Název čítače výkonu | Popis|
| --- | --- |
|Hesla zpracování |Tento čítač zobrazuje celkový počet zpracovaných hesla (přijímat nebo odmítat) od minulého restartování.|
|Hesla přijata |Tento čítač zobrazuje celkový počet hesel, která byla přijata od posledního restartování.|
|Hesla zamítnuto |Tento čítač zobrazuje celkový počet hesel, které byly odmítnuty od posledního restartování.|
|Heslo filtrovat žádosti v průběhu |Tento čítač zobrazuje počet požadavků filtr heslo, které aktuálně probíhá.|
|Požadavky filtru hesel ve špičce |Tento čítač zobrazuje nejvyšší počet souběžných heslo filtrovat žádosti od minulého restartování.|
|Heslo filtrování požadavku chyb |Tento čítač zobrazuje celkový počet požadavků filtr heslo, které se nezdařilo z důvodu chyby od posledního restartování. Pokud není spuštěna Služba agenta Azure AD hesla ochranu řadič domény, může dojít k chybám.|
|Heslo filtru požadavků za sekundu |Tento čítač zobrazuje míru, ve kterém jsou zpracovávány hesla.|
|Doba zpracování požadavku filtru hesel |Tento čítač zobrazuje průměrný čas potřebný ke zpracování požadavku filtru hesel.|
|Doba zpracování požadavku filtru hesel ve špičce |Tento čítač zobrazuje čas od minulého restartování zpracování požadavků filtru hesel ve špičce.|
|Hesla přijata z důvodu režimu auditování |Tento čítač zobrazuje celkový počet hesel, která by obvykle byly odmítnuty, ale byla přijata, protože zásady hesel nastavený tak, aby se v režimu auditování (od minulého restartování).|

## <a name="directory-services-repair-mode"></a>Režimu oprav adresářových služeb

Pokud řadič domény, který naběhne do režimu oprav adresářových služeb, službu agenta DC zjistí tento způsobí všechna ověření hesla nebo vynucení aktivity se deaktivuje, bez ohledu na aktuálně aktivní zásady konfigurace.

## <a name="domain-controller-demotion"></a>Snížení úrovně řadiče domény

Podporuje se degradujete řadič domény, na kterém běží pořád softwaru agenta řadiče domény. Správci by měli vědět ale, že software agenta řadiče domény běžel a pokračuje v vynucování aktuální zásady hesel během postupu snížení úrovně. Nové heslo místního správce účtu (zadaná jako součást operace degradace) se ověří stejně jako jakékoli jiné heslo. Společnost Microsoft doporučuje zvolit zabezpečeného hesla pro místní účty správců jako součást postupu snížení úrovně řadiče domény; ověření nové heslo místního správce účtu agenta softwarem řadiče domény ale může být rušivé existující provozní postupy snížení úrovně.

Po degradování proběhla úspěšně, a po restartování řadiče domény a je znovu spuštěna jako normální členský server, k softwaru agenta řadiče domény se vrátí ke spouštění v pasivním režimu. Může být pak odinstalovali kdykoli.

## <a name="removal"></a>Odebrání

Pokud je se rozhodli odinstalovat software ve verzi public preview a vyčištění všech souvisejících stavu z domény a doménové struktury, můžete tento úkol provést pomocí následujících kroků:

> [!IMPORTANT]
> Je potřeba provést tyto kroky v pořadí. Pokud všechny instance služby serveru Proxy, zůstane spuštěn pravidelně znovu vytvoří jeho serviceConnectionPoint objekt. Pokud všechny instance služby agenta řadiče domény, zůstane spuštěn pravidelně znovu vytvoří jeho objekt serviceConnectionPoint a stav sysvol.

1. Ochrana heslem Proxy software odinstalujte ze všech počítačů. Tento krok provádí **není** vyžadují restartování.
2. Odinstalace softwaru agenta řadiče domény ze všech řadičů domény. Tento krok **vyžaduje** restartovat počítač.
3. Ručně odeberte všechny body připojení proxy server služby v každé doméně názvový kontext. Umístění tyto objekty mohou být zjištěny pomocí následujícího příkazu Powershellu pro Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Není vynechat hvězdičku ("*") na konci $keywords hodnotu proměnné.

   Výsledné objekty vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně. 

4. Ručně odeberte všechny body připojení agenta řadiče domény v každé doméně názvový kontext. Může jich být tyto objekty na řadič domény v doménové struktuře, v závislosti na tom, jak často byl nasazen software ve verzi public preview. Umístění tohoto objektu může být nalezeny pomocí následujícího příkazu Powershellu pro Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledné objekty vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně.

5. Ručně odeberte stavu konfigurace na úrovni doménové struktury. Stav konfigurace doménové struktuře se udržuje v kontejneru v názvovém kontextu konfigurace služby Active Directory. Lze zjistit a odstranit následujícím způsobem:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectonConfigContainer
   ```

6. Ručně odeberte všechny adresáře sysvol související s stavu tím, že ručně odstranit následující složku a veškerý jeho obsah:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   V případě potřeby tuto cestu můžete také získat přístup na místně na daný řadič domény; Výchozí umístění by měl vypadat následující cestu:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Tato cesta se liší, pokud není nakonfigurovaná sdílená složka sysvol v jiné než výchozí umístění.

## <a name="next-steps"></a>Další postup

Další informace o seznamech globálních a vlastních zakázaných hesel, najdete v článku [zakázat chybná hesla](concept-password-ban-bad.md)
