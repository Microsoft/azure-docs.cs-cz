---
title: Řešení potíží s testy dostupnosti služby Azure Application Insights
description: Řešení potíží s webovými testy v Azure Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318143"
---
# <a name="troubleshooting"></a>Řešení potíží

Tento článek vám pomůže vyřešit běžné problémy, ke kterým může dojít při použití monitorování dostupnosti.

## <a name="ssltls-errors"></a>Chyby SSL/TLS

|Příznak/chybová zpráva| Možné příčiny|
|--------|------|
|Nejde vytvořit zabezpečený kanál SSL/TLS.  | Verze SSL. Podporují se jenom TLS 1,0, 1,1 a 1,2. **Protokolu SSLv3 se nepodporuje.**
|Vrstva záznamu TLSv 1.2: výstraha (úroveň: závažná, popis: chybný záznam v počítači MAC)| [Další informace](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)najdete v tématu věnovaném vláknu stackexchange.
|Neúspěšná adresa URL je CDN (Content Delivery Network) | To může být způsobeno chybnou konfigurací v síti CDN. |  

### <a name="possible-workaround"></a>Možná alternativní řešení

* Pokud jsou adresy URL, u kterých dochází k problému, vždy závislé prostředky, doporučuje se zakázat **zpracování závislých požadavků** pro webový test.

## <a name="test-fails-only-from-certain-locations"></a>Test se nezdařil pouze z určitých umístění

|Příznak/chybová zpráva| Možné příčiny|
|----|---------|
|Pokus o připojení se nezdařil, protože připojená strana nereagovala po určitém časovém intervalu správně.  | Testovací agenti v určitých umístěních jsou blokovány bránou firewall.|
|    |K opětovnému směrování určitých IP adres dochází prostřednictvím (nástroje pro vyrovnávání zatížení, správci geografického provozu a Azure Express Route). 
|    |Pokud používáte Azure ExpressRoute, existují scénáře, kdy se pakety můžou vyřadit v případech, kdy [dojde k asymetrickému směrování](../../expressroute/expressroute-asymmetric-routing.md).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Selhání testu s chybou porušení protokolu

|Příznak/chybová zpráva| Možné příčiny| Možná řešení |
|----|---------|-----|
|Server potvrdil narušení protokolu. Oddíl = ResponseHeader detail = CR musí být následován znakem LF. | K tomu dochází, když jsou zjištěna chybná záhlaví. Konkrétně některá záhlaví nemusí používat znaky CRLF k označení konce řádku, což porušuje specifikaci protokolu HTTP. Application Insights vynucuje tuto specifikaci HTTP a neúspěšné odpovědi s nesprávně vytvořenými záhlavími.| a. Pokud chcete opravit chybné servery, obraťte se na poskytovatele hostitele webu nebo poskytovatele CDN. <br> b. V případě, že neúspěšné požadavky jsou prostředky (například soubory stylu, obrázky, skripty), můžete zvážit zakázání analýzy závislých požadavků. Mějte na paměti, že pokud to uděláte, ztratíte možnost sledovat dostupnost těchto souborů.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají odlehčené ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Běžné otázky k odstraňování potíží

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Web vypadá v pořádku, ale vidím selhání testu Proč mi Application Insights upozorňování?

   * Má váš test povolené **analyzovat závislé požadavky** ? Výsledkem je striktní kontroly nad prostředky, jako jsou skripty, obrázky atd. Tyto typy selhání nemusí být v prohlížeči patrné. Zkontrolujte všechny image, skripty, šablony stylů a všechny další soubory, které stránka načetla. Pokud některý z nich dojde k selhání, test se ohlásí jako neúspěšný, i když se hlavní HTML stránka načte bez problémů. Pro desenzibilizaci testu u takových selhání prostředků jednoduše zrušte kontrolu požadavků závislých na analýze z konfigurace testu.

   * Pokud chcete snížit lichá hluku z přechodné síťové výkyvů atd., zkontrolujte, že je zaškrtnuté políčko Povolit opakování pro konfiguraci selhání testu. Můžete také testovat z více umístění a spravovat prahovou hodnotu pravidla výstrahy, aby se zabránilo problémům specifickým pro konkrétní umístění, což způsobuje neoprávněné výstrahy.

   * Kliknutím na kteroukoli z těchto červených teček v možnosti dostupnosti nebo jakékoli chybě dostupnosti z Průzkumníka služby Search zobrazíte podrobnosti o tom, proč jsme chybu nahlásili. Výsledek testu, společně s korelační telemetrie na straně serveru (Pokud je povolen), by měl pomáhat pochopit, proč se test nezdařil. Běžné příčiny přechodných problémů jsou problémy se sítí nebo připojením.

   * Vypršel časový limit testu? Testy jsme přerušili po 2 minutách. Pokud váš test příkazů nebo testování více kroků trvá déle než 2 minuty, pošleme vám zprávu jako chybu. Zvažte rozdělení testu na násobky, které mohou být dokončeny v kratší dobu.

   * Nahlásila se všechna umístění jako neúspěšná nebo jenom některá z nich? Pokud se jedná o pouze některé hlášené chyby, může to být způsobeno problémy se sítí nebo CDN. Opětovným kliknutím na červené tečky by bylo užitečné pochopit, proč se v umístění nahlásilo selhání.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nedostali jsme mi e-mail, když se aktivuje výstraha, nebo se vyřešilo?

Zkontrolujte konfiguraci klasických výstrah a potvrďte, že je váš e-mail přímo uveden, nebo distribuční seznam, na který jste v systému nakonfigurované, aby přijímal oznámení. Pokud je, zkontrolujte konfiguraci distribučního seznamu a potvrďte, že může přijímat externí e-maily. Také se podívejte, jestli správce pošty může mít nakonfigurované nějaké zásady, které by mohly způsobovat tento problém.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nedostali jsme oznámení Webhooku?

Zkontrolujte, zda je k dispozici aplikace, která přijímá oznámení Webhooku, a úspěšně zpracuje žádosti Webhooku. Další informace najdete v [tomto](../platform/alerts-log-webhook.md) tématu.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Zobrazují se chyby 403 zakázáno, co to znamená?

Tato chyba označuje, že je potřeba přidat výjimky brány firewall, aby mohli agenti dostupnosti otestovat cílovou adresu URL. Úplný seznam IP adres agenta, který se má použít, najdete v [článku věnovaném výjimkám IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Občasné selhání testu s chybou porušení protokolu

Chyba („Porušení protokolu... Znak CR musí být následován znakem LF.“) značí problémy se serverem (nebo závislostmi). To se stává, když jsou v odpovědi nastavena chybně vytvořená záhlaví. Příčinou mohou být nástroje pro vyrovnávání zatížení nebo CDN. Konkrétně některá záhlaví nepoužívají znaky CRLF k označení konce řádku, což porušuje specifikaci HTTP, a proto selže ověřování na úrovni .NET WebRequest. Zkontrolujte odpověď na místo, které může být v rozporu.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají odlehčené ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nezobrazuje se žádná související telemetrie na straně serveru pro diagnostiku selhání testu? *

Pokud máte pro aplikaci na straně serveru nastavenou službu Application Insights, může být důvodem to, že právě probíhá [vzorkování](./sampling.md). Vyberte jiný výsledek dostupnosti.

### <a name="can-i-call-code-from-my-web-test"></a>Mohu volat kód z mého webového testu?

Ne. Kroky testu musí být v souboru .webtest. A nemůžete volat jiné webové testy nebo používat smyčky. Existují různé zásuvné moduly, které se vám můžou hodit.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existuje rozdíl mezi "webovými testy" a "testy dostupnosti"?

Významy těchto dvou výrazů jsou zaměnitelné. Testy dostupnosti jsou obecnější označení, které kromě webových testů s více kroky zahrnuje taky jednorázové testy adres URL příkazem Ping.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chci používat testy dostupnosti na našem interním serveru, který se spouští za bránou firewall.

   Existují dvě možná řešení:

   * Nakonfigurujte bránu firewall, aby povolovala příchozí požadavky z [IP adres našich agentů webového testu](./ip-addresses.md).
   * Napište vlastní kód, který pravidelně testuje interní server. Spusťte kód na testovacím serveru jako proces na pozadí za vaší bránou firewall. Testovací proces můžete odesílat své výsledky do Application Insights pomocí rozhraní API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) v balíčku Core SDK. To vyžaduje, aby měl váš testovací server odchozí přístup ke koncovému bodu ingestování Application Insights, ale to je mnohem menší riziko zabezpečení než případné povolení příchozích požadavků. Výsledky se zobrazí v oknech webové testy dostupnosti, i když se prostředí mírně zjednoduší z toho, co je k dispozici pro testy vytvořené prostřednictvím portálu. Vlastní testy dostupnosti se také zobrazí jako výsledky dostupnosti v analýzách, vyhledávání a metrikách.

### <a name="uploading-a-multi-step-web-test-fails"></a>Nahrávání vícekrokového webového testu se nezdaří

K tomu může dojít z některých důvodů:
   * Maximální velikost je 300 kB.
   * Smyčky nejsou podporovány.
   * Odkazy na jiné webové testy nejsou podporovány.
   * Zdroje dat nejsou podporovány.

### <a name="my-multi-step-test-doesnt-complete"></a>Můj vícekrokový test se nedokončil

Existuje limit 100 požadavků na test. Test se také zastaví, pokud běží déle než dvě minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak mohu spustit test pomocí certifikátů klientů?

To se v tuto chvíli nepodporuje.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo obdrží oznámení o výstrahách (Classic)?

Tato část platí jenom pro klasické výstrahy a pomůže vám optimalizovat oznámení o výstrahách, abyste zajistili, že oznámení budou dostávat jenom vaši dožádaný příjemce. Pokud chcete získat další informace o rozdílu mezi [klasickými výstrahami](../platform/alerts-classic.overview.md)a s novým prostředím výstrah, přečtěte si [článek Přehled výstrah](../platform/alerts-overview.md). Pro řízení upozornění na upozornění v novém prostředí výstrahy použijte [skupiny akcí](../platform/action-groups.md).

* Pro klasická oznámení o výstrahách doporučujeme používat konkrétní příjemce.

* Pro výstrahy týkající se selhání z umístění X z umístění Y, pokud je tato možnost zapnutá, posílá uživatelům s rolemi správce/spolusprávce možnost **Hromadná a skupinová** zaškrtávací políčka.  Oznámení budou dostávat v podstatě _Všichni_ správci _předplatného_ .

* U upozornění na metriky dostupnosti je možnost **Hromadná a skupinová** zaškrtávací políčko, pokud je povolena, odesílá uživatelům s rolemi vlastník, přispěvatel nebo čtenář v předplatném. V důsledku toho budou mít _Všichni_ uživatelé s přístupem k předplatnému Application Insights prostředek v oboru a budou dostávat oznámení. 

> [!NOTE]
> Pokud v tuto chvíli používáte možnost **hromadného a skupinového** zaškrtávacího políčka a zakážete ji, nebudete moct změnu vrátit.

Pokud potřebujete upozornit uživatele na základě jejich rolí, použijte nové výstrahy Výstrahy a možnosti téměř v reálném čase. Pomocí [skupin akcí](../platform/action-groups.md)můžete nakonfigurovat e-mailová oznámení uživatelům pomocí kterékoli role Přispěvatel/vlastník/čtenář (bez kombinace společně s jednou možností).

## <a name="next-steps"></a>Další kroky

* [Testování webu ve více krocích](availability-multistep.md)
* [Testy adresy URL pro příkazy URL](monitor-web-app-availability.md)

