---
title: Řešení potíží a protokolování v Azure AD hesla protection ve verzi preview
description: Vysvětlení ochrana hesel Azure AD ve verzi preview, protokolování a řešení běžných potíží
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1eea6380d4276644db0c7681f23a4b0c5e79ff09
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187345"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Ve verzi Preview: Azure AD hesla ochrany monitorování, vytváření sestav a řešení potíží

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po nasazení ochrany hesla Azure AD jsou základní úlohy monitorování a vytváření sestav. Tento článek obsahuje podrobnosti kterých víte, kde každá služba protokoluje informace a hlášení týkající se použití ochrany hesla Azure AD.

## <a name="on-premises-logs-and-events"></a>Místní protokoly a události

### <a name="dc-agent-service"></a>Služba agenta řadiče domény

V každém řadiči domény k softwaru agenta služby řadiče domény zapíše výsledky ověření jeho heslo (a další stav) do místního protokolu událostí: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin služby

Události jsou protokolovány podle různých komponent agenta řadiče domény pomocí následující rozsahy:

|Komponenta |Rozsah ID událostí|
| --- | --- |
|Knihovny dll filtru hesel agenta řadiče domény| 10000-19999|
|Řadič domény agenta služby hostitelského procesu| 20000 29999|
|Logiku ověřování zásad služby agenta řadiče domény| 30000 39999|

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

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Ukázková zpráva protokolu událostí pro sadu 10014 hesla se podařila ID události

Změnit heslo pro zadaného uživatele byl ověřen jako kompatibilní s aktuální zásady hesel služby Azure.

 Uživatelské jméno: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázka protokolu událostí zprávy pro ID události 10017 a 30003 se nezdařilo nastavení hesla

10017:

Resetovat heslo pro zadaného uživatele byl odmítnut, protože neodpovídá aktuální zásady hesel služby Azure. Podrobnosti najdete na zprávu korelační protokol událostí pro další podrobnosti.

 Uživatelské jméno: BPL_03283841185 FullName:

30003:

Resetovat heslo pro zadaného uživatele byl odmítnut, protože odpovídá alespoň jedné tokeny v seznamu zakázaných na tenanta heslo z aktuální zásady hesel služby Azure.

 Uživatelské jméno: BPL_03283841185 FullName:

Některé klíče zprávy protokolu událostí je potřeba vědět jsou:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Ukázka protokolu událostí zprávy pro ID události 30001

Heslo pro zadaného uživatele se přijal, protože zásady hesel služby Azure ještě není k dispozici

Uživatelské jméno: <user> jméno a příjmení: <user>

K tomuto stavu může být způsobeno jedním nebo více z následujících důvodů: % n

1. Doménová struktura ještě není zaregistrovaný s Azure.

   Postup řešení: Správce musí zaregistrovat pomocí rutinu Register-AzureADPasswordProtectionForest doménové struktury.

2. Ochrana heslem Azure AD proxy serveru ještě není k dispozici na alespoň jeden počítač v aktuální doménové struktuře.

   Postup řešení: správce musíte nainstalovat a zaregistrovat pomocí rutinu Register-AzureADPasswordProtectionProxy proxy server.

3. Tento řadič domény nemá síťové připojení k žádné instance služby Azure AD hesla ochrany proxy serveru.

   Postup řešení: Zkontrolujte připojení k síti existuje alespoň jedna instance Proxy ochranu služby Azure AD hesla.

4. Tento řadič domény nemá připojení k jiné řadiče domény v doméně.

   Postup řešení: Zkontrolujte existuje síťové připojení k doméně.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Ukázka protokolu událostí zprávy pro ID události 30006

Služba je nyní vynucuje tyto zásady hesel služby Azure.

 AuditOnly: 1

 Globální zásady datum: 2018-05-15T00:00:00.000000000Z

 Datum zásady klienta: 2018-06-10T20:15:24.432457600Z

 Vynucení zásad klienta: 1

#### <a name="dc-agent-log-locations"></a>Umístění protokolu agenta řadiče domény

Služba agenta řadiče domény do následující protokolu navíc zaznamená provozní události související se zabezpečením: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational služby

Služba agenta řadiče domény můžete také podrobné úroveň ladění trasování událostí v protokolu do následující protokol: \Applications a Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace služby

> [!WARNING]
> Ve výchozím nastavení je zakázaný protokol trasování. Při povolení tohoto protokolu přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

### <a name="azure-ad-password-protection-proxy-service"></a>Služba Azure AD hesla protection proxy

Ochrany heslem služba Proxy vysílá minimální sadu událostí do protokolu událostí následující: \Applications a Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational služby

Ochrany heslem služba Proxy můžete také podrobné úroveň ladění trasování událostí v protokolu do následující protokol: \Applications a Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace služby

> [!WARNING]
> Ve výchozím nastavení je zakázaný protokol trasování. Pokud povolená, obdrží tento protokol k velkému počtu událostí a to může mít vliv na výkon hostitele proxy serveru. Proto tento protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

### <a name="dc-agent-discovery"></a>Zjišťování agentů DC

`Get-AzureADPasswordProtectionDCAgent` Rutiny lze zobrazit základní informace o různých agentů DC spuštěných v doméně nebo doménové struktuře. Tyto informace získány z serviceConnectionPoint objekty registrované ve spuštěné službě agenta řadiče domény. Ukázkový výstup této rutiny je následujícím způsobem:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Různé vlastnosti se aktualizují každou službu agenta DC přibližně hodinu. Data se stále latenci replikace služby Active Directory.

Rozsah rutiny dotazu může jím můžou být buď pomocí – doménová struktura nebo – doména parametry.

### <a name="emergency-remediation"></a>Nouzový nápravy

Pokud unfortunate situace nastane, pokud služba agenta DC způsobuje problémy, službu agenta řadiče domény může okamžitě ukončena. Dll filtru hesel agenta DC pokusí volat službu bez spuštění a budou protokolovat události upozornění (10012, 10013), ale během této doby jsou přijímány příchozí všechna hesla. Služba agenta řadiče domény může také být nakonfigurována prostřednictvím Windows správce řízení služeb s typem spuštění "Zakázáno" podle potřeby.

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
> Je potřeba provést tyto kroky v pořadí. Pokud je ponecháno žádné instance ochrany heslem služba Proxy s se bude pravidelně znovu vytvořit jeho objekt serviceConnectionPoint i pravidelně znovu vytvořit stav sysvol.

1. Ochrana heslem Proxy software odinstalujte ze všech počítačů. Tento krok provádí **není** vyžadují restartování.
2. Odinstalace softwaru agenta řadiče domény ze všech řadičů domény. Tento krok **vyžaduje** restartovat počítač.
3. Ručně odeberte všechny body připojení proxy server služby v každé doméně názvový kontext. Umístění tyto objekty mohou být zjištěny pomocí následujícího příkazu Powershellu pro Active Directory:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Není vynechat hvězdičku ("*") na konci $keywords hodnotu proměnné.

   Výsledný objekt vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně. 

4. Ručně odeberte všechny body připojení agenta řadiče domény v každé doméně názvový kontext. Může jich být tyto objekty na řadič domény v doménové struktuře, v závislosti na tom, jak často byl nasazen software ve verzi public preview. Umístění tohoto objektu může být nalezeny pomocí následujícího příkazu Powershellu pro Active Directory:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledný objekt vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně.

5. Ručně odeberte stavu konfigurace na úrovni doménové struktury. Stav konfigurace doménové struktuře se udržuje v kontejneru v názvovém kontextu konfigurace služby Active Directory. Lze zjistit a odstranit následujícím způsobem:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Ručně odeberte všechny adresáře sysvol související s stavu tím, že ručně odstranit následující složku a veškerý jeho obsah:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   V případě potřeby tuto cestu můžete také získat přístup na místně na daný řadič domény; Výchozí umístění by měl vypadat následující cestu:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Tato cesta se liší, pokud není nakonfigurovaná sdílená složka sysvol v jiné než výchozí umístění.

## <a name="next-steps"></a>Další postup

Další informace o seznamech globálních a vlastních zakázaných hesel, najdete v článku [zakázat chybná hesla](concept-password-ban-bad.md)
