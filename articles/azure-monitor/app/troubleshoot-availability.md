---
title: Řešení potíží s testy dostupnosti služby Azure Application Insights
description: Řešení potíží s webovými testy v Azure Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 02/14/2021
ms.reviewer: sdash
ms.openlocfilehash: 940a70de81df60f7b6c1545c992e1ee10e69fa9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728922"
---
# <a name="troubleshooting"></a>Poradce při potížích

Tento článek vám pomůže vyřešit běžné problémy, ke kterým může dojít při použití monitorování dostupnosti.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Řešení potíží s kroky sestav pro testy příkazů pro testování

Sestava Poradce při potížích umožňuje snadno diagnostikovat běžné problémy, které způsobují selhání **testů testu** .

![Animace navigace na kartě dostupnosti výběrem chyby v podrobnostech o chybě pro koncovou transakci zobrazíte zprávu o řešení potíží.](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. Na kartě Dostupnost prostředku Application Insights vyberte celkový nebo jeden z testů dostupnosti.
2. Vyberte **neúspěšné** a potom test pod položkou "přejít k" na levé straně nebo vyberte jeden z bodů v bodovém grafu.
3. Na stránce s podrobnostmi o transakci na konci vyberte událost v části "souhrn sestavy řešení potíží" vyberte **[přejít ke kroku]** . zobrazí se zpráva o odstraňování potíží.

> [!NOTE]
>  Pokud je k dispozici krok opětovného použití připojení, nebude k dispozici postup překladu DNS, vytváření připojení a přenos TLS.

|Krok | Chybová zpráva | Možná příčina |
|-----|---------------|----------------|
| Opakované použití připojení | Není k dispozici | Obvykle závisí na dřív navázaném připojení, což znamená, že je krok webového testu závislý. Proto by nebyl nutný žádný krok DNS, připojení nebo SSL. |
| Překlad DNS | Vzdálený název se nedal přeložit: "vaše adresa URL" | Proces překladu DNS se nezdařil, pravděpodobně v důsledku nesprávně konfigurovaných záznamů DNS nebo dočasných selhání serveru DNS. |
| Vytváření připojení | Pokus o připojení se nezdařil, protože připojená strana nereagovala po určitém časovém intervalu správně. | Obecně to znamená, že váš server neodpovídá na požadavek HTTP. Běžným řešením je, že naše testovací agenti jsou na serveru zablokované bránou firewall. Pokud byste chtěli testovat v rámci Virtual Network Azure, měli byste do svého prostředí přidat značku služby dostupnosti.|
| Přenos TLS  | Klient a server nemohou komunikovat, protože nemají společný algoritmus.| Podporují se jenom TLS 1,0, 1,1 a 1,2. Protokol SSL není podporován. Tento krok neověřuje certifikáty SSL a vytváří jenom zabezpečené připojení. Tento krok se zobrazí jenom v případě, že dojde k chybě. |
| Příjem hlavičky odpovědi | Nelze načíst data z přenosového připojení. Připojení bylo ukončeno. | Server potvrdil chybu protokolu v hlavičce odpovědi. Například připojení zavřené serverem, když odpověď není úplná. |
| Přijímání textu odpovědi | Nelze načíst data z přenosového připojení: připojení bylo ukončeno. | Server potvrdil chybu protokolu v těle odpovědi. Například připojení zavřené serverem, když odpověď není plně přečtená nebo je velikost bloku dat v těle odpovědi v bloku chybná. |
| Ověření limitu přesměrování | Tato webová stránka má příliš mnoho přesměrování. Tato smyčka se tady ukončí, protože tento požadavek překročil limit pro automatické přesměrování. | U každého testu je omezení 10 přesměrování. |
| Ověření kódu stavu | `200 - OK` neodpovídá očekávanému stavu `400 - BadRequest` . | Vrácený stavový kód, který se počítá jako úspěch. 200 je kód, který označuje, že byla vrácena normální webová stránka. |
| Ověření obsahu | Povinný text Hello se v odpovědi neobjevil. | Řetězec není v odpovědi přesně rozlišovat velká a malá písmena, například řetězec "Welcome!". Musí se jednat o prostý řetězec bez zástupných znaků (například hvězdička). Pokud se změní obsah stránky, možná budete muset řetězec aktualizovat. Shoda obsahu podporuje pouze anglické znaky. |
  
## <a name="common-troubleshooting-questions"></a>Běžné otázky k odstraňování potíží

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Web vypadá v pořádku, ale vidím selhání testu Proč mi Application Insights upozorňování?

   * Má váš test povolené **analyzovat závislé požadavky** ? Výsledkem je striktní kontroly nad prostředky, jako jsou skripty, obrázky atd. Tyto typy selhání nemusí být v prohlížeči patrné. Zkontrolujte všechny image, skripty, šablony stylů a všechny další soubory, které stránka načetla. Pokud některý z nich dojde k selhání, test se ohlásí jako neúspěšný, i když se hlavní HTML stránka načte bez problémů. Pro desenzibilizaci testu u takových selhání prostředků jednoduše zrušte kontrolu požadavků závislých na analýze z konfigurace testu.

   * Pokud chcete snížit lichá hluku z přechodné síťové výkyvů atd., zkontrolujte, že je zaškrtnuté políčko Povolit opakování pro konfiguraci selhání testu. Můžete také testovat z více umístění a spravovat prahovou hodnotu pravidla výstrahy, aby se zabránilo problémům specifickým pro konkrétní umístění, což způsobuje neoprávněné výstrahy.

   * Kliknutím na kteroukoli z těchto červených teček z prostředí bodového sezobrazení bodového grafu dostupnosti nebo jakékoli chybě dostupnosti z Průzkumníka služby Search zobrazíte podrobnosti o tom, proč jsme chybu nahlásili. Výsledek testu, společně s korelační telemetrie na straně serveru (Pokud je povolen), by měl pomáhat pochopit, proč se test nezdařil. Běžné příčiny přechodných problémů jsou problémy se sítí nebo připojením.

   * Vypršel časový limit testu? Testy jsme přerušili po 2 minutách. Pokud váš test příkazů nebo testování více kroků trvá déle než 2 minuty, pošleme vám zprávu jako chybu. Zvažte rozdělení testu na násobky, které mohou být dokončeny v kratší dobu.

   * Nahlásila se všechna umístění jako neúspěšná nebo jenom některá z nich? Pokud se jedná o pouze některé hlášené chyby, může to být způsobeno problémy se sítí nebo CDN. Opětovným kliknutím na červené tečky by bylo užitečné pochopit, proč se v umístění nahlásilo selhání.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nedostali jsme mi e-mail, když se aktivuje výstraha, nebo se vyřešilo?

Zkontrolujte konfiguraci skupiny akcí výstrahy a potvrďte, že je váš e-mail přímo uveden, nebo distribuční seznam, na který jste v systému nakonfigurované, aby přijímal oznámení. Pokud je, zkontrolujte konfiguraci distribučního seznamu a potvrďte, že může přijímat externí e-maily. Také se podívejte, jestli správce pošty může mít nakonfigurované nějaké zásady, které by mohly způsobovat tento problém.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nedostali jsme oznámení Webhooku?

Zkontrolujte, zda je k dispozici aplikace, která přijímá oznámení Webhooku, a úspěšně zpracuje žádosti Webhooku. Další informace najdete v [tomto](../alerts/alerts-log-webhook.md) tématu.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Zobrazují se chyby 403 zakázáno, co to znamená?

Tato chyba označuje, že je potřeba přidat výjimky brány firewall, aby mohli agenti dostupnosti otestovat cílovou adresu URL. Úplný seznam IP adres agenta, který se má použít, najdete v [článku věnovaném výjimkám IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Občasné selhání testu s chybou porušení protokolu

Chyba („Porušení protokolu... Znak CR musí být následován znakem LF.“) značí problémy se serverem (nebo závislostmi). To se stává, když jsou v odpovědi nastavena chybně vytvořená záhlaví. Příčinou mohou být nástroje pro vyrovnávání zatížení nebo CDN. Konkrétně některá záhlaví nepoužívají znaky CRLF k označení konce řádku, což porušuje specifikaci HTTP, a proto selže ověřování na úrovni .NET WebRequest. Zkontrolujte odpověď na místo, které může být v rozporu.

> [!NOTE]
> Adresa URL nemusí selhat v prohlížečích, které mají odlehčené ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nezobrazuje se žádná související telemetrie na straně serveru pro diagnostiku selhání testu? *

Pokud máte pro aplikaci na straně serveru nastavenou službu Application Insights, může být důvodem to, že právě probíhá [vzorkování](./sampling.md). Vyberte jiný výsledek dostupnosti.

### <a name="can-i-call-code-from-my-web-test"></a>Mohu volat kód z mého webového testu?

No. Kroky testu musí být v souboru .webtest. A nemůžete volat jiné webové testy nebo používat smyčky. Existují různé zásuvné moduly, které se vám můžou hodit.


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

## <a name="next-steps"></a>Další kroky

* [Testování webu ve více krocích](availability-multistep.md)
* [Testy adresy URL pro příkazy URL](monitor-web-app-availability.md)