---
title: Co je Azure web application firewall pro Azure branou? (Preview)
description: Zjistěte jak Azure firewallu webových aplikací pro službu Azure branou chrání webové aplikace před útoky se zlými úmysly.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 17cf6629aca6c73bc96e4cf0c172a2e87a7aafb8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799163"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door-preview"></a>Co je Azure web application firewall pro Azure branou? (Preview)

Firewall webových aplikací (WAF) (preview) nabízí centralizovanou ochranu webových aplikací, která jsou poskytována globálně pomocí Azure branou. Je navržena a provozované pro obranu před běžným zneužitím a ohrožení zabezpečení vašich webových služeb a zachování vaší služby s vysokou dostupností pro vaše uživatele kromě pomáhá splnit požadavky na dodržování předpisů.

> [!IMPORTANT]
> Firewall webových aplikací (WAF) pro Azure branou je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, jako jsou záplavy služby a útoky prostřednictvím injektáže SQL, útoky skriptování napříč weby s cílem odepření. Tyto útoky se zlými úmysly může způsobit, že služba výpadky a ztráty dat, představuje vážné hrozbě vlastníkům webové aplikace.

Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Kromě toho řešení WAF může reagovat na ohrožení zabezpečení rychleji protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací.

WAF pro branou je globální a centralizovaného řešení. Nasazuje se v síti Azure okrajových umístěních po celém světě a každou příchozí požadavek pro webovou aplikaci aktivovanou úrovní WAF od branou se prozkoumá na hranici sítě. To umožňuje WAF, aby se zabránilo útoky se zlými úmysly blízko zdroje útoku, před vstupem vaší virtuální sítě a nabízí globální ochranu ve velkém měřítku bez omezení výkonu. Zásady WAF můžete snadno propojit se žádný profil branou v rámci vašeho předplatného a nová pravidla je možné nasadit během několika minut díky tomu umožňuje rychle reagovat na měnící se vzorů ohrožení.

![Firewall webových aplikací](./media/waf-overview/web-application-firewall-overview.png)

Waf služby Azure se dá povolit i pomocí služby Application Gateway. Další informace najdete v tématu [firewallu webových aplikací](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>WAF zásad a pravidel

Můžete nakonfigurovat zásadu WAF a přidružit tuto zásadu na jeden nebo více branou front endů pro ochranu. Zásady WAF se skládá ze dvou typů pravidel zabezpečení:
- vlastní pravidla, které jsou vytvořeny příslušného zákazníka.
- sady pravidel spravovaná, které jsou kolekce spravovanými Azure předem nakonfigurované sady pravidel. Pokud jsou obě přítomny, vlastní pravidla jsou spuštěny před spuštěním pravidla v sadě pravidel spravovaná. Pravidlo tvoří podmínku shody, prioritu a akce. Jsou podporované typy akcí: POVOLIT, blok protokolu a PŘESMĚROVÁNÍ. Můžete vytvořit plně přizpůsobené zásady, který splňuje vaše požadavky na ochranu konkrétní aplikaci díky kombinaci spravovaných a vlastních pravidel.

Pravidla v rámci zásady jsou provedeny v seřazené podle priority, kde priorita je jedinečné celé číslo, které definuje pořadí zpracování pravidla. Menší celé číslo označuje s vyšší prioritou a ty se vyhodnocují před pravidel s vyšší hodnotou celé číslo. Jakmile pravidlo je nalezena shoda, je pro žádost použity odpovídající akce, která byla definována v pravidle. Po zpracování těchto shoda pravidla s nižší prioritou nejsou další zpracování.

Webové aplikace od branou může mít pouze jednu zásadu WAF s ním spojená v čase. Však může mít branou konfigurace bez všechny zásady WAF s ním spojená. Pokud zásady WAF je k dispozici, se replikují do všech našich okrajových umístěních po celém světě zajistit konzistenci v rámci zásad zabezpečení.

## <a name="waf-modes"></a>Režimy WAF

WAF zásad může být konfigurované pro běh v těchto dvou režimech:

- **Režim detekce:** Při spuštění v režimu detekce, WAF nepřijímá žádné další akce, než monitorování a zaznamená žádost a její odpovídající pravidlo WAF do protokolů WAF. Můžete zapnout protokolování diagnostiky pro branou (když používáte portál, jde tohoto dosáhnout tak, že přejdete **diagnostiky** části webu Azure Portal).

- **Režim ochrany před únikem informací:** Při nakonfigurované na spouštění v režimu ochrany před únikem informací, WAF přejde na zadanou akci, pokud požadavek odpovídá pravidlu, a pokud se najde shoda, jsou vyhodnocovány žádná další pravidla s nižší prioritou. Žádné odpovídající požadavky jsou také zaznamenána protokolů WAF.

## <a name="waf-actions"></a>Akce WAF

Zákazníci WAF můžete spustit z jednoho z akcí, když požadavek odpovídá podmínky pravidla:
- **Povolte:**  Požadavek prochází WAF a je předán do back endu. Žádné další nižší prioritu pravidla můžete zablokovat tuto žádost.
- **Blok:** Požadavek je blokován a WAF odešle odpověď do klienta bez předání požadavku do back endu.
- **Log:**  Žádost o protokoluje do protokolů WAF a WAF pokračuje vyhodnocování pravidel nižší prioritou.
- **Přesměrování:** WAF přesměruje požadavek na zadaný identifikátor URI. Zadaný identifikátor URI je nastavení úrovně zásad. Po nakonfigurování všech požadavků, které odpovídají **přesměrování** akce se pošle na tento identifikátor URI.


## <a name="waf-rules"></a>Pravidla firewallu webových aplikací

Zásady WAF se může skládat ze dvou typů pravidel zabezpečení – vlastní pravidla, autorem zákazníka a spravovaných sady pravidel, spravované v Azure předem nakonfigurované sady pravidel.

### <a name="custom-authored-rules"></a>Vlastní pravidla vytvořené
Vlastní pravidla WAF můžete nakonfigurovat následujícím způsobem:

- **IP povolit seznamu a seznam blokovaných položek:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě seznamu klientských IP adres nebo rozsahy IP adres. Typy adres IPv4 a IPv6 jsou podporovány. Tento seznam lze nakonfigurovat buď blokovat nebo povolit tyto požadavky, pokud zdrojová IP adresa shoduje s IP adresy v seznamu.

- **Řízení geografické přístupu na základě:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k vaší webové aplikace založené na kód země spojené s IP adresou klienta.

- **Řízení přístupu na základě parametrů HTTP:** Můžete nakonfigurovat vlastní pravidla na základě řetězce odpovídající parametrům požadavku HTTP/HTTPS například řetězce dotazu, args POST, identifikátor URI požadavku, hlavička požadavku a text žádosti.

- **Požádat o řízení přístupu založených na volání metody:** Můžete nakonfigurovat vlastní pravidla založená na metoda požadavku HTTP požadavku třeba GET, PUT ani HEAD.

- **Omezení velikosti:** Můžete nakonfigurovat vlastní pravidla založená na délky konkrétní části požadavku, například řetězce dotazu, identifikátor Uri, nebo text žádosti.

- **Rychlost omezení pravidel:** Pravidlo řízení míry je omezit neobvyklé vysoký provoz z jakékoli IP adresy klienta. Můžete nakonfigurovat prahové hodnoty počtu webových žádostí z IP adresa klienta povolené po dobu jedné minuty. To se liší od IP založený na seznamu povolených nebo blokovaných vlastní pravidlo, které buď povoluje všechny nebo bloky všech žádostí z IP adresy klienta. Omezení rychlosti zkombinovat s podmínkami další shody, jako je například porovnávání pro ovládací prvek podrobné míra parametry HTTP (S).


### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované v Azure

Sady pravidel spravovanými Azure poskytují snadný způsob, jak nasadit ochranu proti společnou sadu bezpečnostních hrozeb. Vzhledem k tomu, že tyto sady pravidel spravuje Azure, pravidla se aktualizují podle potřeby pro ochranu před nové podpisy útoku. Ve verzi public preview spravovanými Azure výchozí sada pravidel obsahuje pravidla pro následující kategorie hrozby:

- Skriptování napříč weby
- Útoky Java
- Místní soubor zahrnutí
- Útoky vkládáním kódu PHP
- Spuštění vzdálených příkazů
- Zahrnutí vzdáleného souboru
- Záznam relace
- Ochrana před útoky prostřednictvím injektáže SQL.

Číslo verze sady výchozích pravidel se zvýší, když nové podpisy útoku se přidají do sady pravidel.
Výchozí pravidlo nastavené je povolené ve výchozím nastavení v režimu detekce v zásadách pro WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozí pravidlo sady podle požadavků vaší aplikace. Můžete také nastavit konkrétní akce (povolit nebo BLOKOVAT/PŘESMĚROVÁNÍ/přihlášení) v jednotlivém pravidle. Výchozí akce je blok. Kromě toho vlastní pravidla můžete konfigurovat ve stejných zásadách WAF, pokud chcete obejít některý z předem nakonfigurovaná pravidla v sadě výchozí pravidlo.
Vlastní pravidla se vždy použijí předtím, než se vyhodnocují pravidla v sadě výchozí pravidlo. Pokud požadavek odpovídá vlastní pravidlo, příslušné opatření. pravidlo se použije, a požadavek je blokován nebo předává do back-end bez vyvolání jakékoli další vlastní pravidla nebo pravidla v sadě výchozí pravidlo. Kromě toho máte možnost pro odebrání výchozí pravidlo nastavené na základě zásad WAF.


## <a name="configuration"></a>Konfigurace
Ve verzi Public Preview:
- Konfigurace a nasazení všechny typy pravidel WAF se plně podporuje použití rozhraní REST API, šablonách Azure Resource Manageru a Azure Powershellu.
- Pomocí webu Azure portal, můžete nakonfigurovat nebo zobrazit pouze spravované Azure výchozí sada pravidel.

## <a name="monitoring"></a>Monitorování

Monitorování WAF za branou je integrovaná s Azure Monitor pro sledování výstrah a snadnou identifikaci trendů provoz.

## <a name="pricing"></a>Ceny

Veškeré využití související s WAF pro branou ve verzi Public Preview je zdarma a nebude nic účtovat.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).

