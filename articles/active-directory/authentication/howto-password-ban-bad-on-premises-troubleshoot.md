---
title: Řešení potíží a protokolování v Azure AD heslo protection verze preview
description: Ochrana heslem Azure AD pochopit náhled protokolování a řešení běžných potíží
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295656"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Ve verzi Preview: Azure AD heslo ochrany monitorování, vytváření sestav a řešení potíží

|     |
| --- |
| Ochrana heslem Azure AD a seznamu zakázaných vlastního hesla jsou funkce verze public preview služby Azure Active Directory. Další informace o verze Preview najdete v tématu [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po nasazení ochrany hesel Azure AD jsou nezbytné úlohy monitorování a vytváření sestav. Tento článek obsahuje podrobnosti pomohou, že rozumíte kam protokoluje každé služby informace a jak vytvářet sestavu na použití ochrany Azure AD pomocí hesla.

## <a name="on-premises-logs-and-events"></a>Místní protokolů a událostí

### <a name="dc-agent-service"></a>Služba agenta řadiče domény

V každém řadiči domény softwaru služby agenta řadič domény zapíše výsledky ověření jeho heslo (a další stav) do místního protokolu událostí: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin služby

Různé komponenty agenta řadič domény pomocí následující rozsahy jsou zaznamenány události:

|Komponenta |Rozsah ID událostí|
| --- | --- |
|Knihovny dll filtru hesel řadiče domény agenta| 19999 10000.|
|Řadič domény agenta služby hostitelského procesu| 20000 29999|
|Logika ověřování zásad služby agenta řadiče domény| 30000 39999|

Operace ověření úspěšné heslo není obvykle jednu událost se zaznamená z knihovny dll filtru hesel agenta řadiče domény. Pro selhání operace ověření hesla, existují obecně dvě události protokolované, jeden ze služby agenta řadič domény a jeden z knihovny dll filtru hesel agenta řadiče domény.

Diskrétní události pro zachycení těchto situacích se protokolují, na základě kolem následující faktory:

* Zda se dané heslo nastavit nebo změnit.
* Jestli ověření daného hesla předán nebo se nezdařilo.
* Jestli ověření se nezdařilo z důvodu vs globální zásady Microsoft zásady organizace.
* Jestli jenom režim auditování je aktuálně zapnout nebo vypnout pro aktuální zásady hesel.

Klíč události související s hesly ověření jsou následující:

|   |Změna hesla |Nastavení hesla|
| --- | :---: | :---: |
|Úspěch |10014 |10015|
|Selhání (neprošel zákazníka zásady hesel)| 10016, 30002| 10017, 30003|
|Selhání (neprošel zásady hesel Microsoft)| 10016, 30004| 10017, 30005|
|Jenom pro auditování průchodu (by se nezdařil zásady hesel zákazníka)| 10024, 30008| 10025, 30007|
|Jenom pro auditování průchodu (by se nezdařil zásady hesel Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Příchozí hesla jsou ověřený proti seznamu globální heslo Microsoft nejprve; Pokud to nepomůže, je provést žádné další zpracování. Toto je stejné chování jako provádět na změny hesel v Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Ukázka zprávy protokolu událostí pro sadu 10014 úspěšné heslo ID události

Změněné heslo pro zadaného uživatele potvrzená jako kompatibilní s aktuální zásady hesel Azure.

 Uživatelské jméno: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázka zprávy protokolu událostí pro sadu heslo ID události 10017 a 30003 se nezdařilo

10017:

Resetovat heslo pro zadaného uživatele byl odmítnut, protože neodpovídá aktuální zásady hesel Azure. Podrobnosti viz zprávu korelační protokol událostí pro další podrobnosti.

 Uživatelské jméno: BPL_03283841185 FullName:

30003:

Resetovat heslo pro zadaného uživatele byl odmítnut, protože mu odpovídala alespoň jeden z tokenů uvedena v seznamu zakázaných za klienta heslo aktuální zásady hesel Azure.

 Uživatelské jméno: BPL_03283841185 FullName:

Některé klíče zprávy protokolu událostí zajímat jsou:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Ukázka zprávy protokolu událostí pro ID události 30001

Heslo pro zadaného uživatele byl přijat, protože zásady hesel Azure ještě není k dispozici

Uživatelské jméno: <user> FullName: <user>

Tento stav může být způsobeno jeden nebo více z následujících důvodů: % n

1. Doménová struktura ještě nebyl zaregistrován v Azure.

   Kroky řešení: Správce musí zaregistrovat pomocí rutinu Register-AzureADPasswordProtectionForest doménové struktuře.

2. Ochrana heslem Azure AD proxy serveru ještě není k dispozici na alespoň jeden počítač v aktuální doménové struktuře.

   Kroky řešení: správce musíte nainstalovat a použít rutinu Register-AzureADPasswordProtectionProxy proxy zaregistrovat.

3. Tento řadič domény nemá připojení k síti na všech instancí služby Azure AD heslo ochrany proxy serveru.

   Kroky řešení: Zkontrolujte síťové připojení do alespoň jedné instance Proxy ochrany Azure AD heslo.

4. Tento řadič domény nemá připojení k jiným řadičům domény v doméně.

   Kroky řešení: Zkontrolujte síťové připojení k doméně.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Ukázka zprávy protokolu událostí pro ID události 30006

Služba je nyní vynucování tyto zásady hesel Azure.

 AuditOnly: 1

 Globální zásady datum: 2018-05-15T00:00:00.000000000Z

 Datum zásady klienta: 2018-06-10T20:15:24.432457600Z

 Vynutit zásady klienta: 1

#### <a name="dc-agent-log-locations"></a>Umístění protokolu agenta řadiče domény

Služba agenta řadič domény bude také protokolu provozní související události následující: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational služby

Služba agenta řadič domény můžete také protokolu události podrobného trasování ladění úrovni následující: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace služby

> [!WARNING]
> Ve výchozím nastavení vypnutá protokolu trasování. Když je povolené, tento protokol přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento rozšířené protokol by měl lze povolit pouze při problém vyžaduje hlubší rozbor a potom pouze pro co nejkratším čase.

### <a name="azure-ad-password-protection-proxy-service"></a>Služba Azure AD heslo ochrany proxy

Ochrana heslem služba Proxy vysílá minimální sadu události do protokolu událostí následující: \Applications a Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational služby

Ochrana heslem službu proxy serveru můžete také protokolu události podrobného trasování ladění úrovni následující: \Applications a Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace služby

> [!WARNING]
> Ve výchozím nastavení vypnutá protokolu trasování. Když je povolené, tento protokol přijímá k velkému počtu událostí, a to může mít vliv na výkon proxy hostitele. Proto tento protokol by měl povolit pouze pokud problém vyžaduje hlubší rozbor a potom pouze pro co nejkratším čase.

### <a name="dc-agent-discovery"></a>Zjišťování agentů řadiče domény

`Get-AzureADPasswordProtectionDCAgent` Rutiny lze zobrazit základní informace o různých řadič domény agentů spuštěných v doméně nebo doménové struktury. Tyto informace se načítají objekty serviceConnectionPoint registrovaných služeb agenta spuštěného řadiče domény. Příklad výstup této rutiny je následující:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Různé vlastnosti se aktualizují v každý řadič domény služby agenta na přibližnou hodinu. Data se stále mohou latenci replikace služby Active Directory.

Může mít vliv rozsahu rutiny dotazu, buď pomocí – doménová struktura nebo – doména parametry.

### <a name="emergency-remediation"></a>Nouzový nápravy

Pokud dojde k velice nepříjemná situaci, kdy služba agenta DC způsobuje problémy, službu agent řadič domény může být okamžitě vypněte. Dll filtru hesel řadiče domény agenta pokusí volání služby není spuštěna a bude protokolovat události upozornění (10012, 10013), ale všechny příchozí hesla jsou podmínky přijaty během této doby. Službu agent řadič domény pak také lze nakonfigurovat pomocí Windows správce řízení služeb s typem spuštění "Disabled" podle potřeby.

### <a name="performance-monitoring"></a>Sledování výkonu

Softwaru služby agenta řadiče domény nainstaluje objekt počítadla výkonu s názvem **ochrana heslem Azure AD**. Aktuálně k dispozici jsou následující čítače výkonu:

|Název čítače výkonu | Popis|
| --- | --- |
|Hesla zpracovat |Čítač zobrazí celkový počet zpracovaných hesla (přijetí nebo odmítnutí) od minulého restartování.|
|Hesla přijato |Čítač zobrazí celkový počet hesel, která byla přijata od minulého restartování.|
|Hesla odmítnut |Čítač zobrazí celkový počet hesel, která byla odmítnuta od minulého restartování.|
|Heslo filtrovat žádosti v průběhu |Tento čítač zobrazuje počet heslo filtru požadavků, které aktuálně probíhá.|
|Filtrovat žádosti heslo ve špičce |Tento čítač zobrazuje počet ve špičce souběžných heslo filtru požadavků od minulého restartování.|
|Heslo filtru požadavek chyby |Čítač zobrazí celkový počet požadavků filtru heslo, které se nezdařily z důvodu chyby od minulého restartování. Pokud není spuštěna Služba agenta Azure AD heslo ochrany řadiče domény, může dojít k chybám.|
|Heslo filtru požadavků za sekundu |Tento čítač zobrazuje rychlost, jakou jsou zpracovávány hesla.|
|Doba zpracování požadavku filtru heslo |Tento čítač zobrazuje průměrný čas potřebný k zpracovat žádost o filtru hesla.|
|Doba zpracování požadavku filtru heslo ve špičce |Tento čítač zobrazuje čas od posledního restartování zpracování požadavků filtru ve špičce heslo.|
|Hesla přijatá z důvodu režimu auditování |Čítač zobrazí celkový počet hesel, která by za normálních okolností byly odmítnuty, ale byla přijata, protože zásady hesel byl nakonfigurován jako v režimu auditování (od posledního restartování).|

## <a name="directory-services-repair-mode"></a>Režimu obnovení adresářových služeb

Pokud řadič domény, který naběhne do režimu obnovení adresářových služeb, službu agent řadič domény rozpozná, tento způsobuje všechny ověření hesla nebo vynucení aktivity se zakáže, bez ohledu na aktuálně aktivní zásady konfigurace.

## <a name="domain-controller-demotion"></a>Snížení úrovně řadiče domény

Podporuje se snížení úrovně řadiče domény, který je stále spuštěná software agenta řadiče domény. Správci by měli být vědomi ale, že software agenta DC neustále běží a pokračuje vynucování aktuálním zásady hesel během procesu snížení úrovně. Nové heslo místního správce účtu (zadané v rámci operace degradace) byl ověřen jako jiné heslo. Společnost Microsoft doporučuje, aby se zabezpečených hesel zvolené pro místní účty správců jako součást postupu snížení úrovně řadiče domény; ověření nové heslo místního správce účtu agenta softwarem řadič domény může být ale rušivý existující provozní postupy snížení úrovně.
Jakmile degradování proběhla úspěšně, a po restartování řadiče domény a znovu běží jako normální členský server, software agenta řadiče domény se vrátí do spuštěna v pasivním režimu. Je možné odinstalována pak kdykoli.

## <a name="removal"></a>Odebrání

Pokud se rozhodne odinstalace softwaru ve verzi public preview a vyčistit všechny související stavu z doménách a doménové struktury, můžete tento úkol provést pomocí následujících kroků:

> [!IMPORTANT]
> Je důležité provést tyto kroky v pořadí. Pokud je ponecháno žádné instanci ochrana heslem služba Proxy spuštěním bude pravidelně znovu vytvořit jeho serviceConnectionPoint objekt a také pravidelně znovu vytvořit stavu adresáře sysvol.

1. Ochrana heslem Proxy softwaru odinstalujte ze všech počítačů. Tento krok nebude **není** vyžadují restart počítače.
2. Odinstalujte software agenta řadiče domény ze všech řadičů domény. Tento krok **vyžaduje** restartování.
3. Ručně odeberte všechny spojovací body služby serveru proxy v každé názvovém kontextu domény. Umístění tyto objekty mohou být zjištěny pomocí následujícího příkazu Powershellu služby Active Directory:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Není vynechejte hvězdička ("*") na konci $keywords hodnotu proměnné.

   Výsledný objekt nalezen prostřednictvím `Get-ADObject` příkaz může být pak přesměruje do `Remove-ADObject`, nebo ručně odstraněna. 

4. Ručně odeberte všechny body připojení agenta řadiče domény v každém názvovém kontextu domény. Může být jeden tyto objekty pro každý řadič domény v doménové struktuře, v závislosti na tom, jak často byl nasazen softwaru ve verzi public preview. Umístění tohoto objektu může být zjištěny pomocí následujícího příkazu Powershellu služby Active Directory:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Výsledný objekt nalezen prostřednictvím `Get-ADObject` příkaz může být pak přesměruje do `Remove-ADObject`, nebo ručně odstraněna.

5. Ručně odeberte stav konfigurace úroveň doménové struktury. Stav konfigurace doménové struktury se udržuje v kontejneru v názvovém kontextu konfigurace služby Active Directory. Lze zjistit a odstranit takto:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Ručně odeberte všechny adresáře sysvol související s stavu tím, že ručně odstraňování následující složku a veškerý jeho obsah:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   V případě potřeby tuto cestu můžete také získat přístup místně na daný řadič domény; Výchozí umístění bude, že se něco podobného jako následující cestě:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Tato cesta je jiné, pokud byl nakonfigurován v jiné než výchozí umístění sdílené složky sysvol.

## <a name="next-steps"></a>Další postup

Další informace o seznamech globální a vlastní zakázaného heslo, najdete v článku [zakázat chybná hesla](concept-password-ban-bad.md)
