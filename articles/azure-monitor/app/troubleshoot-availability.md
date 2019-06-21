---
title: Řešení potíží s testy dostupnosti Azure Application Insights | Dokumentace Microsoftu
description: Řešení potíží s testy webu ve službě Azure Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305134"
---
# <a name="troubleshooting"></a>Řešení potíží

Tento článek vám pomůže při řešení běžných problémů, které mohou nastat při používání sledování dostupnosti.

## <a name="ssltls-errors"></a>Chyby protokolu SSL/TLS

|Příznak/chybová zpráva| Možné příčiny|
|--------|------|
|Nelze vytvořit zabezpečený kanál SSL/TLS.  | Verze protokolu SSL. Podporují se jenom TLS 1.0, 1.1 a 1.2. **Protokol SSLv3 se nepodporuje.**
|TLSv1.2 záznamu vrstvy: Upozornění (úroveň: Závažná, popis: Bad Record MAC)| Viz StackExchange vlákno k provedení [informace](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|Adresa URL, která selhává je do sítě CDN (Content Delivery Network) | Příčinou může být chybná konfigurace na vaší sítě CDN |  

### <a name="possible-workaround"></a>Možná řešení

* Pokud se adresy URL, které hlásí problém jsou vždy závislé prostředky, doporučuje se zakázat **analyzovat závislé požadavky** pro webový test.

## <a name="test-fails-only-from-certain-locations"></a>Test se nezdaří pouze z určitých umístění

|Příznak/chybová zpráva| Možné příčiny|
|----|---------|
|Pokus o připojení se nezdařila, protože připojená strana neodpověděla řádně po určité době  | Testovací agenty v určitých oblastech je blokovaná bránou firewall.|
|    |Přesměrování pro určité IP adresy probíhá přes (Nástroje pro vyrovnávání zatížení, geografické traffic Manager, Azure Express Route.) 
|    |Pokud používáte Azure ExpressRoute, jsou scénáře, ve kterém můžete Vynechané pakety v případech, kdy [Asymetrickému směrování dochází](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Občasné selhání testu s chybou porušení protokolu

|Příznak/chybová zpráva| Možné příčiny|
|----|---------|
porušení protokolu znak CR musí být následován znakem LF | K tomu dojde, když se zjistí chybně vytvořená záhlaví. Konkrétně některé hlavičky možná nepoužívají znaky CRLF k označení konce řádku, které porušují specifikaci protokolu HTTP a proto se nezdaří ověření na úrovni třídy .NET WebRequest.
 || To může taky způsobovat nástroje pro vyrovnávání zatížení nebo CDN.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají rozvolněné ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Běžné otázky k řešení problémů

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Web vypadá v pořádku, ale vidím selhání testu? Proč je Application Insights upozorňování mě?

   * Váš test má **analyzovat závislé požadavky** povolené? Který má za následek striktní kontrolu na prostředky, jako jsou skripty, obrázků apod. Tyto typy selhání nemusí být patrné v prohlížeči. Zkontrolujte všechny image, skripty, šablony stylů a všechny další soubory, které stránka načetla. Pokud některý z nich selže, test se ohlásí jako neúspěšný i v případě, že se hlavní HTML stránka načte bez problému. Pokud chcete snížit citlivost testu vůči selháním těchto prostředků, jednoduše zrušte zaškrtnutí políčka analyzovat závislé požadavky v konfiguraci testu.

   * Chcete-li snížili riziko výskytu krátkodobých síťových výkyvů atd., ujistěte se povolit opakované pokusy pro neúspěšné testy, které se kontroluje konfigurace. Můžete také testy z více umístění a spravovat pravidla upozornění prahové hodnoty odpovídajícím způsobem zabránit způsobí zbytečným výstrahám, které vyhledávají chyby specifické pro umístění.

   * Kliknutím na kteroukoli z červené tečky z prostředí dostupnosti nebo jakékoli neúspěchy dostupnosti z Průzkumníka služby Search můžete zobrazit podrobnosti o proč jsme hlášené chyby. Výsledek testu, spolu s korelační telemetrická data na straně serveru (Pokud je povoleno) by měly pomoci pochopit, proč test selhal. Běžné příčiny přechodné problémy jsou problémy s sítě nebo připojení.

   * Nebyla časový limit testu? Jsme přerušit testy po 2 minuty. Pokud váš příkaz ping nebo vícekrokový test trvá déle než 2 minuty, jsme budou hlásit jako selhání. Zvažte rozdělení do několika ty, které lze provést v kratších dob trvání testu.

   * Nahlášená všechna místa selhání, nebo jenom některé z nich? Pokud jen některé hlásí chyby, může to být kvůli problémům se síti nebo CDN. Kliknutím na červené tečky znovu, by měly pomoci pochopit, proč umístění hlášené chyby.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Můžu nezískaly v e-mailu, když výstraha aktivuje nebo vyřešené nebo obojí?

Kontrola konfigurace klasických upozornění pro potvrzení e-mailu je uveden přímo, nebo distribuční seznam, ke kterému jste na je nastavena k přijímání oznámení. Pokud se jedná, zkontrolujte konfiguraci distribučního seznamu abychom potvrdili, že může přijímat externí e-mailů. Také kontrolovat, jestli může správce e-mailu mají konfigurace zásad, které může způsobit potíže.

### <a name="i-did-not-receive-the-webhook-notification"></a>Mi nedošel webhooku oznámení?

Zkontrolujte aplikace obdrží oznámení webhooku je k dispozici a úspěšně zpracovává požadavky webhooku. Zobrazit [to](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) Další informace.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Občasné selhání testu s chybou porušení protokolu?

Chyba („Porušení protokolu... Znak CR musí být následován znakem LF.“) značí problémy se serverem (nebo závislostmi). To se stává, když jsou v odpovědi nastavena chybně vytvořená záhlaví. Příčinou mohou být nástroje pro vyrovnávání zatížení nebo CDN. Konkrétně některé hlavičky možná nepoužívají znaky CRLF k označení konce řádku, které porušují specifikaci protokolu HTTP a neúspěšné ověření na úrovni třídy .NET WebRequest. Zkontrolujte odpovědi, zda obsahují chybná záhlaví může být v rozporu.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají rozvolněné ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nevidím všechna související telemetrii na straně serveru k diagnostikovala neúspěšné testy? *

Pokud máte pro aplikaci na straně serveru nastavenou službu Application Insights, může být důvodem to, že právě probíhá [vzorkování](../../azure-monitor/app/sampling.md). Vyberte příslušný výsledek různých dostupnosti.

### <a name="can-i-call-code-from-my-web-test"></a>Mohu volat kód z mého webového testu?

Ne. Kroky testu musí být v souboru .webtest. A nemůžete volat jiné webové testy nebo používat smyčky. Existují různé zásuvné moduly, které se vám můžou hodit.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existuje rozdíl mezi "webovými testy" a "testy dostupnosti"?

Významy těchto dvou výrazů jsou zaměnitelné. Testy dostupnosti jsou obecnější označení, které kromě webových testů s více kroky zahrnuje taky jednorázové testy adres URL příkazem Ping.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chci používat testy dostupnosti na našem interním serveru, který se spouští za bránou firewall.

   Existují dvě možná řešení:

   * Nakonfigurujte bránu firewall, aby povolovala příchozí požadavky z [IP adres našich agentů webového testu](../../azure-monitor/app/ip-addresses.md).
   * Napište vlastní kód, který pravidelně testuje interní server. Spusťte kód na testovacím serveru jako proces na pozadí za vaší bránou firewall. Testovací proces můžete odesílat své výsledky do Application Insights pomocí rozhraní API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) v balíčku Core SDK. To vyžaduje, aby měl váš testovací server odchozí přístup ke koncovému bodu ingestování Application Insights, ale to je mnohem menší riziko zabezpečení než případné povolení příchozích požadavků. Výsledky se nebudou zobrazovat v oknech webových testů dostupnosti, ale zobrazí se jako výsledky dostupnosti ve službě Analytics, ve službě Hledání a v Průzkumníku metrik.

### <a name="uploading-a-multi-step-web-test-fails"></a>Nahrávání vícekrokového webového testu se nezdaří

Několik důvodů, proč že k tomu může dojít:
   * Maximální velikost je 300 kB.
   * Smyčky nejsou podporovány.
   * Odkazy na jiné webové testy nejsou podporovány.
   * Zdroje dat nejsou podporovány.

### <a name="my-multi-step-test-doesnt-complete"></a>Můj vícekrokový test se nedokončil

Existuje limit 100 požadavků na test. Také test bude zastaven, pokud poběží déle než dvě minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak mohu spustit test pomocí certifikátů klientů?

To není aktuálně podporováno.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo přijímá oznámení výstrah (klasické)?

V této části pouze platí pro upozornění classic a pomůže vám optimalizovat vaše oznámení o výstrahách Ujistěte se, že pouze požadované příjemci dostávat oznámení. Bližší informace o rozdílech mezi [klasických upozornění](../platform/alerts-classic.overview.md)a předložit nové prostředí upozornění [výstrahy přehledovém článku](../platform/alerts-overview.md). K řízení oznámení oznámení v nové výstrahy prostředí použijte [skupiny akcí](../platform/action-groups.md).

* Doporučujeme vám používat konkrétním příjemcům classic oznámení výstrah.

* Pro výstrahy týkající se selhání z X z Y umístění **hromadné/skupiny** zaškrtávací políčko, pokud je povoleno, odešle uživatelům s rolí správce nebo spolusprávce.  V podstatě _všechny_ správci _předplatné_ obdrží oznámení.

* Upozornění na metriky dostupnosti **hromadné/skupiny** zaškrtávací políčko Pokud je povoleno, odešle uživatelům s rolí vlastník, Přispěvatel nebo Čtenář v rámci předplatného. V důsledku toho _všechny_ uživatelé s přístupem k předplatnému prostředku Application Insights jsou v rozsahu a budou dostávat oznámení. 

> [!NOTE]
> Pokud aktuálně používáte službu **hromadné/skupiny** zaškrtávací políčko a zakázat, nebude možné vrátit zpět změny.

Pomocí nové výstrahy prostředí/v – v reálném čase výstrahy, pokud je potřeba upozornit uživatele na základě jejich rolí. S [skupiny akcí](../platform/action-groups.md), můžete nakonfigurovat e-mailová oznámení pro uživatele s libovolnou z role přispěvatele nebo vlastníka/reader (ne zkopírovat dohromady jako jednu možnost).

## <a name="next-steps"></a>Další postup

* [Testování vícekrokového webů](availability-multistep.md)
* [Testování ping adresy URL](monitor-web-app-availability.md)
