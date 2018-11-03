---
title: Pomocí vyhledávání ve službě Azure Application Insights | Dokumentace Microsoftu
description: Vyhledávání a filtrování nezpracovaná telemetrická data odesílaná aplikací pro web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: bf722756ed04349a06cbcb7b9686e6340e8d4b6c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958402"
---
# <a name="using-search-in-application-insights"></a>Pomocí vyhledávání ve službě Application Insights
Hledání je funkce [Application Insights](app-insights-overview.md) , který používáte k vyhledání a prozkoumejte telemetrická data jednotlivých položek, například zobrazení stránek, výjimky nebo webové požadavky. A můžete zobrazit trasování protokolů a událostí, které jste nakódovali.

(Pro složitější dotazy na data, použijte [Analytics](../log-analytics/query-language/get-started-analytics-portal.md).)

## <a name="where-do-you-see-search"></a>Kde se zobrazí hledání?

### <a name="in-the-azure-portal"></a>Na webu Azure Portal

Diagnostické vyhledávání můžete otevřít explicitně v okně Přehled služby Application Insights vaší aplikace:

![Otevřete diagnostické vyhledávání](./media/app-insights-diagnostic-search/001.png)

![Snímek obrazovky s grafy diagnostické vyhledávání](./media/app-insights-diagnostic-search/002.png)

Hlavní část diagnostické vyhledávání je seznam položek telemetrie – požadavky na server, stránka zobrazení, vlastní události, které jste nakódovali a tak dále. V horní části seznamu je souhrn graf zobrazující počet událostí v čase.

Klikněte na Aktualizovat zobrazíte nové události.

### <a name="in-visual-studio"></a>V nástroji Visual Studio

V sadě Visual Studio je také hledání Application Insights okno. To je zvláště užitečná pro zobrazení telemetrie události generované modulem aplikace, kterou ladíte. Ale můžete také zobrazit událostí shromažďovaných ze publikované aplikace na webu Azure portal.

Otevřete okno hledání v sadě Visual Studio:

![Visual Studio Otevřít hledání Application Insights](./media/app-insights-diagnostic-search/32.png)

V okně hledání má funkce podobně jako na webovém portálu:

![Okno hledání Visual Studio Application Insights](./media/app-insights-diagnostic-search/34.png)

Když otevřete žádost o nebo zobrazení stránky je k dispozici na kartě sledovat operaci. "Operace" je posloupnost událostí, který je přidružený k jedné žádosti nebo stránky zobrazení. Například volání závislostí, výjimek, protokoly trasování a vlastní události může být součástí jedné operace. Na kartě sledovat operaci zobrazí graficky načasování a doba trvání z těchto událostí ve vztahu k požadavku nebo stránky zobrazení. 

## <a name="inspect-individual-items"></a>Zkontrolujte jednotlivé položky

Vyberte všechny položky telemetrie zobrazíte pole klíčů a souvisejícími položkami.

![Snímek obrazovky s požadavkem na jednotlivých závislostí](./media/app-insights-diagnostic-search/003.png)

Tím se spustí zobrazení podrobností transakcí začátku do konce:

![Snímek obrazovky zobrazení podrobností transakcí začátku do konce.](./media/app-insights-diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtrovat typy událostí
Otevře se okno Filtr a vyberte typy událostí, které chcete zobrazit. (Pokud chcete později obnovit filtry, se kterými jste okno otevřeli, klikněte na resetovat.)

![Vyberte filtr a vyberte typy telemetrie](./media/app-insights-diagnostic-search/02-filter-req.png)

Typy událostí jsou:

* **Trasování** - [diagnostické protokoly](app-insights-asp-net-trace-logs.md) včetně TrackTrace, log4Net, NLog a System.Diagnostic.Trace volání.
* **Požádat o** -žádosti HTTP přijaté vaší serverové aplikace, včetně stránek, skripty, obrázky, soubory stylu a data. Tyto události se používají k vytvoření žádostí a odpovědí tabulkách Přehled.
* **Zobrazení stránky** - [Telemetrii zaslanou klientem webového](app-insights-javascript.md), která slouží k vytváření sestav zobrazení stránky. 
* **Vlastní události** - li vložit volání do funkce TrackEvent() za účelem [monitorovat využití](app-insights-api-custom-events-metrics.md), je zde můžete vyhledávat.
* **Výjimka** – nezachycené [výjimky na serveru](app-insights-asp-net-exceptions.md)a ty, kteří se přihlašují pomocí TrackException().
* **Závislost** - [volání z aplikace server](app-insights-asp-net-dependencies.md) k jiným službám, jako jsou rozhraní REST API nebo databáze a AJAX volá z vaší [klientský kód](app-insights-javascript.md).
* **Dostupnost** – výsledky [testy dostupnosti](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrovat podle hodnoty vlastnosti
Můžete filtrovat události na hodnoty jejich vlastností. Dostupné vlastnosti závisí na typy událostí, které jste vybrali. 

Například vyberte žádosti s kódem konkrétní odpověď. 

![Rozbalte vlastnost a zvolit hodnotu](./media/app-insights-diagnostic-search/03-response500.png)

Výběr žádné hodnoty konkrétní vlastnosti má stejný účinek jako volba všechny hodnoty. Ji vypne filtrování pro tuto vlastnost.

### <a name="narrow-your-search"></a>Zpřesnit hledání
Všimněte si, že počty napravo od hodnoty filtru zobrazit, kolik výskytů existuje jsou v aktuální filtrované sadě. 

V tomto příkladu je jasné, že "Záhlavím nebo zaměstnanců" požádat o výsledky ve většině "500" chyb:

![Rozbalte vlastnost a zvolit hodnotu](./media/app-insights-diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Najít akce se stejnou vlastnost
Vyhledejte všechny položky se stejnou hodnotou vlastnosti:

![Klikněte pravým tlačítkem na vlastnosti](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Prohledávat data.

> [!NOTE]
> Pokud chcete psát složitější dotazy, otevřete [ **Analytics** ](../log-analytics/query-language/get-started-analytics-portal.md) z horní části okna hledání.
> 

Můžete vyhledat výrazy v některém z hodnot vlastností. To je zvlášť užitečné, pokud jste napsali [vlastních událostí](app-insights-api-custom-events-metrics.md) s hodnotami vlastností. 

Můžete chtít nastavit čas rozsah, jako hledání nad kratší rozsah jsou rychlejší. 

![Otevřete diagnostické vyhledávání](./media/app-insights-diagnostic-search/appinsights-311search.png)

Hledat celá slova, ne podřetězců. Pomocí speciální znaky, uzavřete do uvozovek.

| řetězec | je *není* zjištěných aplikací | ale tyto ho najít |
| --- | --- | --- |
| HomeController.About |Domovská složka<br/>Kontroler<br/>navýšení kapacity | homecontroller<br/>o produktu<br/>"homecontroller.about"|
|Spojené státy|UNI<br/>videa TED|Spojené<br/>stavy<br/>Spojené státy a<br/>"USA"

Tady jsou hledaných výrazů, které můžete použít:

| Ukázkový dotaz | Efekt |
| --- | --- |
| `apple` |Najít všechny události v časovém rozsahu, jejichž pole obsahují slovo "apple" |
| `apple AND banana` <br/>`apple banana` |Najdete akce, které obsahují i slova. Použít kapitálu "a" Ne "a". <br/>Krátký tvar. |
| `apple OR banana` |Najdete akce, které obsahují buď aplikace word. Použití "Nebo", ne "nebo". |
| `apple NOT banana` |Najdete akce, které obsahují jedno slovo ale nikoli u druhého. |

## <a name="sampling"></a>Vzorkování
Pokud vaše aplikace generuje mnoho telemetrie (a vy používáte 2.0.0-beta3 verze sady SDK technologie ASP.NET nebo novější), modul adaptivního vzorkování automaticky sníží objem, který je odesílán na portál odesláním pouze reprezentativní vzorky událostí. Události, které se vztahují ke stejnému požadavku jsou však vybrané nebo nevybrány jako skupina, takže mohou procházet mezi souvisejícími událostmi. 

[Další informace o vzorkování](app-insights-sampling.md).

## <a name="create-work-item"></a>Vytvořit pracovní položku
Můžete vytvořit chybu v Githubu nebo Azure DevOps s podrobnostmi o z libovolné položky telemetrie. 

![Klikněte na novou pracovní položku, upravte pole a pak klikněte na tlačítko OK.](./media/app-insights-diagnostic-search/42.png)

Okamžiku, kdy to provedete, budete vyzváni ke konfiguraci propojení pro vaši organizaci Azure DevOps a projektu.

![Zadejte adresu URL služby Azure DevOps a název projektu a klikněte na tlačítko Ověřit](./media/app-insights-diagnostic-search/41.png)

(Můžete také nakonfigurovat na odkaz v okně pracovních položek).

## <a name="send-more-telemetry-to-application-insights"></a>Odeslat další telemetrická data do Application Insights
Kromě out-of-the-box telemetrická data odesílaná sadu SDK Application Insights můžete:

* Záznam trasování protokolu z vašeho oblíbeného rozhraní protokolování v [.NET](app-insights-asp-net-trace-logs.md) nebo [Java](app-insights-java-trace-logs.md). To znamená, že můžete prohledávat protokoly trasování a korelovat je s zobrazení stránek, výjimky a další události. 
* [Psaní kódu](app-insights-api-custom-events-metrics.md) k odesílání vlastních událostí, zobrazení stránek a výjimky. 

[Zjistěte, jak odesílat protokoly a vlastní telemetrii do Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>FUNKCE Q &AMP; A
### <a name="limits"></a>Jaká data se uchovávají?

Zobrazit [souhrn omezení](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak lze zobrazit data příspěvek v Moje žádosti serveru?
Automaticky jsme není protokolů následných dat ale můžete použít [TrackTrace nebo protokol volání](app-insights-asp-net-trace-logs.md). Vložte příspěvek data v parametru zprávy. Nelze filtrovat zprávy stejným způsobem, který můžete filtrovat podle vlastností, ale maximální velikost je delší.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Další kroky
* [Zápis složitých dotazů v Analytics](../log-analytics/query-language/get-started-analytics-portal.md)
* [Odeslání protokolů a vlastní telemetrii do Application Insights](app-insights-asp-net-trace-logs.md)
* [Nastavení dostupnosti a rychlosti odezvy testy](app-insights-monitor-web-app-availability.md)
* [Řešení potíží](app-insights-troubleshoot-faq.md)
