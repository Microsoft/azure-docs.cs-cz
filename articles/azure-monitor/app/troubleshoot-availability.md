---
title: Poradce při potížích s testy dostupnosti Azure Application Insights | Dokumenty společnosti Microsoft
description: Poradce při potížích s webovými testy v Azure Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671099"
---
# <a name="troubleshooting"></a>Řešení potíží

Tento článek vám pomůže vyřešit běžné problémy, které mohou nastat při použití monitorování dostupnosti.

## <a name="ssltls-errors"></a>Chyby SSL/TLS

|Zpráva o příznaku/chybě| Možné příčiny|
|--------|------|
|Nelze vytvořit zabezpečený kanál SSL/TLS.  | Verze SSL. Podporovány jsou pouze TLS 1.0, 1.1 a 1.2. **SSLv3 není podporován.**
|TLSv1.2 Record Layer: Výstraha (Úroveň: Fatální, Popis: Bad Record MAC)| Další [informace](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)naleznete v tématu StackExchange thread .
|Adresa URL, která selhává, je pro síť CDN (Content Delivery Network) | Příčinou může být nesprávnou konfigurací |  

### <a name="possible-workaround"></a>Možné řešení

* Pokud adresy URL, které dochází k problému jsou vždy závislé prostředky, je doporučeno zakázat **požadavky závislé na analýzách** pro webový test.

## <a name="test-fails-only-from-certain-locations"></a>Test se nezdaří pouze z určitých míst

|Zpráva o příznaku/chybě| Možné příčiny|
|----|---------|
|Pokus o připojení se nezdařil, protože připojená strana po určité době neodpověděla správně  | Testovací agenti v určitých umístěních jsou blokováni bránou firewall.|
|    |K přesměrování určitých IP adres dochází prostřednictvím (nástroje pro vyrovnávání zatížení, správci geografického provozu, Azure Express Route.) 
|    |Pokud používáte Azure ExpressRoute, existují scénáře, kde pakety mohou být vynechány v případech, kdy [dojde k asymetrickésměrování](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Selhání testu s chybou porušení protokolu

|Zpráva o příznaku/chybě| Možné příčiny| Možná řešení |
|----|---------|-----|
|Server se dopustil porušení protokolu. Section=ResponseHeader Detail=CR musí následovat LF | K tomu dochází, když jsou zjištěny poškozené hlavičky. Konkrétně některé hlavičky nemusí používat CRLF k označení konce řádku, což porušuje specifikaci HTTP. Application Insights vynucuje tuto specifikaci HTTP a selže odpovědi s poškozenými záhlaví.| a. Obraťte se na poskytovatele hostitele webových stránek / poskytovatele CDN opravit vadné servery. <br> b. V případě, že neúspěšné požadavky jsou prostředky (např. soubory stylů, obrázky, skripty), můžete zvážit zakázání analýzy závislých požadavků. Mějte na paměti, pokud tak učiníte, ztratíte možnost sledovat dostupnost těchto souborů).

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají uvolněné ověření záhlaví PROTOKOLU HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Běžné otázky k řešení potíží

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Web vypadá v pořádku, ale vidím selhání testu Proč mě Application Insights upozorňují?

   * Má váš test **analyzovat závislé požadavky povoleny?** To má za následek přísnou kontrolu zdrojů, jako jsou skripty, obrázky atd. Tyto typy selhání nemusí být patrné v prohlížeči. Zkontrolujte všechny image, skripty, šablony stylů a všechny další soubory, které stránka načetla. Pokud některý z nich selže, test se ohlásí jako neúspěšný, i když se hlavní stránka HTML načte bez problémů. Chcete-li znecitlivit test na takové selhání prostředků, jednoduše zrušit zaškrtnutí analýzy závislé požadavky z konfigurace testu.

   * Chcete-li snížit pravděpodobnost šumu z přechodných síťových výstřiků atd., ujistěte se, že je kontrolována možnost Povolit opakování pro selhání testu. Můžete také testovat z více míst a odpovídajícím způsobem spravovat prahovou hodnotu pravidla výstrahy, abyste zabránili problémům specifickým pro umístění, které způsobují nepatřičná upozornění.

   * Kliknutím na některou z červených tečů ze zkušenosti s dostupností nebo na jakékoli selhání dostupnosti z průzkumníka vyhledávání zobrazíte podrobnosti o tom, proč jsme chybu nahlásili. Výsledek testu spolu s korelační telemetrií na straně serveru (pokud je povolena) by měl pomoci pochopit, proč se test nezdařil. Běžné příčiny přechodných problémů jsou problémy se sítí nebo připojením.

   * Byl zkušební časový výtok? Testy přerušíme po dvou minutách. Pokud váš ping nebo vícestupňový test trvá déle než 2 minuty, nahlásíme to jako selhání. Zvažte rozdělení testu na více, které lze dokončit v kratšídobě trvání.

   * Hlásily všechny lokality selhání, nebo jen některé z nich? Pokud pouze některé hlášené chyby, může to být způsobeno problémy sítě /CDN. Opět platí, že kliknutím na červené tečky by měl y pomoci pochopit, proč umístění hlášeny chyby.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nedostal jsem e-mail, když se výstraha spustila, nebo vyřešena nebo obojí?

Zkontrolujte klasickou konfiguraci výstrah a potvrďte, že váš e-mail je přímo uveden, nebo je distribuční seznam, na který se nacházíte, nakonfigurován tak, aby přijímali oznámení. Pokud ano, zkontrolujte konfiguraci distribučního seznamu a potvrďte, že může přijímat externí e-maily. Zkontrolujte také, zda správce pošty může mít nakonfigurované zásady, které mohou způsobit tento problém.

### <a name="i-did-not-receive-the-webhook-notification"></a>Neobdržel(a) jsem oznámení o webhooku?

Zkontrolujte, zda aplikace přijímající oznámení webhooku je k dispozici a úspěšně zpracovává požadavky webhooku. Další informace naleznete v [tomto](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) tématu.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Občasné selhání testu s chybou porušení protokolu

Chyba („Porušení protokolu... Znak CR musí být následován znakem LF.“) značí problémy se serverem (nebo závislostmi). To se stává, když jsou v odpovědi nastavena chybně vytvořená záhlaví. Příčinou mohou být nástroje pro vyrovnávání zatížení nebo CDN. Konkrétně některé hlavičky nemusí používat CRLF k označení konce řádku, což porušuje specifikaci PROTOKOLU HTTP a proto se nezdaří ověření na úrovni .NET WebRequest. Zkontrolujte odpověď na záhlaví bodů, která mohou být v rozporu.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají uvolněné ověření záhlaví PROTOKOLU HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nevidím žádné související telemetrie na straně serveru pro diagnostiku selhání testu?*

Pokud máte pro aplikaci na straně serveru nastavenou službu Application Insights, může být důvodem to, že právě probíhá [vzorkování](../../azure-monitor/app/sampling.md). Vyberte jiný výsledek dostupnosti.

### <a name="can-i-call-code-from-my-web-test"></a>Mohu volat kód z mého webového testu?

Ne. Kroky testu musí být v souboru .webtest. A nemůžete volat jiné webové testy nebo používat smyčky. Existují různé zásuvné moduly, které se vám můžou hodit.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existuje rozdíl mezi "webovými testy" a "testy dostupnosti"?

Významy těchto dvou výrazů jsou zaměnitelné. Testy dostupnosti jsou obecnější označení, které kromě webových testů s více kroky zahrnuje taky jednorázové testy adres URL příkazem Ping.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chci používat testy dostupnosti na našem interním serveru, který se spouští za bránou firewall.

   Existují dvě možná řešení:

   * Nakonfigurujte bránu firewall, aby povolovala příchozí požadavky z [IP adres našich agentů webového testu](../../azure-monitor/app/ip-addresses.md).
   * Napište vlastní kód, který pravidelně testuje interní server. Spusťte kód na testovacím serveru jako proces na pozadí za vaší bránou firewall. Testovací proces můžete odesílat své výsledky do Application Insights pomocí rozhraní API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) v balíčku Core SDK. To vyžaduje, aby měl váš testovací server odchozí přístup ke koncovému bodu ingestování Application Insights, ale to je mnohem menší riziko zabezpečení než případné povolení příchozích požadavků. Výsledky se zobrazí v listech webových testů dostupnosti, i když zkušenosti budou mírně zjednodušeny z toho, co je k dispozici pro testy vytvořené prostřednictvím portálu. Vlastní testy dostupnosti se také zobrazí jako výsledky dostupnosti v Analytics, Vyhledávání a metriky.

### <a name="uploading-a-multi-step-web-test-fails"></a>Nahrávání vícekrokového webového testu se nezdaří

Některé důvody k tomu může dojít:
   * Maximální velikost je 300 kB.
   * Smyčky nejsou podporovány.
   * Odkazy na jiné webové testy nejsou podporovány.
   * Zdroje dat nejsou podporovány.

### <a name="my-multi-step-test-doesnt-complete"></a>Můj vícekrokový test se nedokončil

Existuje limit 100 požadavků na test. Test je také zastaven, pokud běží déle než dvě minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak mohu spustit test pomocí certifikátů klientů?

Tato tato podpora není v současné době podporována.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo obdrží (klasická) upozornění?

Tato část se vztahuje pouze na klasická upozornění a pomůže vám optimalizovat upozornění tak, aby oznámení dostávali pouze požadovaní příjemci. Další informace o rozdílu mezi [klasickými výstrahami](../platform/alerts-classic.overview.md)a novým prostředím výstrah naleznete v [článku s přehledem výstrah](../platform/alerts-overview.md). Chcete-li řídit upozornění v novém prostředí výstrah, použijte [skupiny akcí](../platform/action-groups.md).

* Doporučujeme používat konkrétní příjemce pro klasická upozornění.

* U výstrah týkajících se chyb z umístění X mimo y odesílá možnost zaškrtávacího **políčka hromadné/skupinové,** pokud je povolena, uživatelům s rolemi správce/spolusprávce.  V podstatě _všichni_ správci _předplatného_ obdrží oznámení.

* U upozornění na metriky dostupnosti je možnost zaškrtávacího políčka **hromadné/skupinové,** pokud je povolena, odesílá uživatelům s rolemi vlastníka, přispěvatele nebo čtenáře v předplatném. Ve skutečnosti _všichni_ uživatelé s přístupem k předplatnému prostředku Application Insights jsou v oboru a budou dostávat oznámení. 

> [!NOTE]
> Pokud aktuálně používáte možnost **hromadného/skupinového** zaškrtávacího políčka a zakážete ji, nebudete moci změnu vrátit zpět.

Pokud potřebujete upozornit uživatele na základě jejich rolí, použijte nové prostředí výstrah y/výstrahy téměř v reálném čase. Pomocí [skupin akcí](../platform/action-groups.md)můžete nakonfigurovat e-mailová oznámení uživatelům s některou z rolí přispěvatele/vlastníka/čtenáře (není kombinováno dohromady jako jedna možnost).

## <a name="next-steps"></a>Další kroky

* [Vícestupňové webové testování](availability-multistep.md)
* [Testy příkazu URL](monitor-web-app-availability.md)
