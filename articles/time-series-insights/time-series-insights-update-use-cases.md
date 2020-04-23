---
title: Případy použití ve verzi Preview – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o případech použití ve verzi Preview Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087389"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Případy použití ve verzi Preview pro Azure Time Series Insights

Tento článek shrnuje několik běžných případů použití pro Azure Time Series Insights Preview. Doporučení v tomto článku slouží jako výchozí bod pro vývoj aplikací a řešení pomocí Time Series Insights.

Konkrétně tento článek obsahuje odpovědi na následující otázky:

* Jaké jsou běžné případy použití pro Time Series Insights?
* Jaké jsou výhody používání Time Series Insights pro [zkoumání dat a detekci vizuální anomálií](#data-exploration-and-visual-anomaly-detection)?
* Jaké jsou výhody používání Time Series Insights pro [provozní analýzu a efektivitu procesů](#operational-analysis-and-driving-process-efficiency)?
* Jaké jsou výhody použití Time Series Insights pro [pokročilou analýzu](#advanced-analytics)?

Přehled těchto scénářů použití je popsaný v následujících částech.

## <a name="introduction"></a>Úvod

Azure Time Series Insights je ucelená nabídka typu platforma jako služba. Používá se ke shromažďování, zpracování, ukládání, analýze a dotazování vysoce kontextových dat IoT škály, které jsou optimalizované pro časovou řadu. Time Series Insights je ideální pro zkoumání a provozní analýzu dat ad hoc. Time Series Insights je jedinečná rozšiřitelná a přizpůsobená nabídka služeb, která splňuje široké požadavky na nasazení v průmyslu IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Detekce zkoumání dat a vizuální anomálii

Okamžitě Prozkoumejte a analyzujte miliardy událostí a zjistěte anomálie a Objevte skryté trendy ve vašich datech. Time Series Insights zajišťuje výkon pro úlohy analýzy IoT a DevOps téměř v reálném čase.

[![Průzkumník dat](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Většina zákazníků souhlasí s tím, že minimální doba potřebná k získání přehledu je jednou z největší funkcí Time Series Insights:

* Time Series Insights nevyžaduje žádnou přípravu dat předem. 
* Umožňuje vám rychle se připojit k miliardám událostí ve službě Azure IoT Hub nebo instancích Azure Event Hubs v řádu minut. 
* Po připojení můžete vizualizovat a analyzovat miliardy událostí a odhalit anomálie a zjišťovat skryté trendy ve vašich datech.

Time Series Insights je intuitivní a snadno se používá. S daty můžete pracovat, aniž byste museli psát jediný řádek kódu. K dispozici není také žádný nový jazyk, který byste si museli učit, i když Time Series Insights poskytuje podrobný dotazovací jazyk založený na textu pro pokročilé uživatele, kteří znají SQL. Nabízí také průzkum vybrat a kliknout pro ně.

Zákazníci můžou využít rychlost a rychle diagnostikovat problémy související s assety. Můžou provádět analýzu DevOps a získat tak hlavní příčinu chyby v řešení IoT. Můžou také označovat oblasti, které je potřeba označit k dalšímu šetření v rámci svých iniciativ v oblasti datových věd. 

Existují tři základní způsoby, jak pracovat s daty uloženými v Time Series Insights:

* První a nejjednodušší způsob, jak začít, je pomocí Průzkumníka Time Series Insights Preview. Můžete ji použít k rychlému vizualizaci všech dat IoT na jednom místě. Poskytuje nástroje jako Heat mapu, které vám pomůžou odhalit anomálie ve vašich datech. Nabízí také pohled na perspektivu. Pomocí ní můžete porovnat až čtyři zobrazení z jednoho nebo více Time Series Insightsch prostředí na jednom řídicím panelu. Řídicí panel poskytuje zobrazení dat časových řad napříč všemi vašimi umístěními. Přečtěte si další informace o [aplikaci Time Series Insights Preview](./time-series-insights-update-explorer.md). Pokud chcete naplánovat Time Series Insights prostředí, přečtěte si téma [plánování Time Series Insights](./time-series-insights-update-plan.md).

* Druhým způsobem, jak začít, je použít sadu JavaScript SDK k rychlému vkládání výkonných grafů a grafů do vaší webové aplikace. Pouze pár řádků kódu vám umožní vytvářet výkonné dotazy. Použijte je k naplnění spojnicových grafů, výsečových grafů, pruhových grafů, heatch map, datových mřížek a dalších. Všechny tyto prvky existují předem v dialogovém okně pomocí sady SDK. Sada SDK také abstrakce Time Series Insights rozhraní API pro dotazy. Můžete je použít k vytváření predikátů podobných SQL k dotazování na data, která chcete zobrazit na řídicím panelu. Pro hybridní řešení prezentační vrstvy nabízí Time Series Insights jako parametrizované adresy URL. Poskytují hladce spojovací body pomocí Průzkumníka Time Series Insights Preview pro rozsáhlou komentářei dat.

  * Přečtěte si o [klientské knihovně Time Series Insights js](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) a dokumentaci pro [Time Series Insights klienta](https://github.com/Microsoft/tsiclient) , kde najdete další informace o sadě JavaScript SDK.

  * Další informace o sdílení adres URL a novém uživatelském rozhraní najdete [v části vizualizace dat v Průzkumníkovi ve službě Azure Time Series Insights Preview](time-series-insights-update-explorer.md).

* Třetí způsob, jak začít, je použít výkonné rozhraní API k dotazování na data uložená v Time Series Insights. Time Series Insights obsahuje `from`dočasné operátory, jako například `to`, `first`, a `last`. Obsahuje agregace a transformace, jako `average`jsou, `min` `max`,, `split by`, `order by`a. `DateHistogram` Má také operátory `has`filtrování, jako jsou, `in`, `and`, `or`, `greater than`a. `REGEX` Všechny tyto operátory umožňují aplikacím pro příjem dat rychle najít zajímavé trendy a vzory ve vašich datech. Použijte je k naplnění vizualizací Homegrown pro zjištění anomálií.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýza a proces řízení efektivity

Pomocí Time Series Insights můžete monitorovat stav, využití a výkon zařízení ve velkém měřítku. Time Series Insights poskytuje snadný způsob, jak změřit provozní efektivitu. Time Series Insights pomáhá spravovat různorodé a nepředvídatelné úlohy IoT bez omezení ingestování nebo výkonu dotazů.

[![Přehled](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streamování a průběžné zpracování dat přicházejících z provozních procesů může úspěšně transformovat libovolnou firmu, pokud je v kombinaci s pravou technologií nebo řešením. Tato řešení jsou často kombinací více systémů. Umožňují prozkoumání a analýzu dat, která se průběžně mění, zejména ve sféře IoT, a sdílejí společný vzor.

Tyto vzory často začínají s platformami podporujícími IoT, které ingestují miliardy událostí ze zařízení a senzorů, které jsou v různých národních prostředích. Tyto systémy zpracovávají a analyzují streamovaná data pro odvození přehledů a akcí v reálném čase. Data se obvykle archivují do teplého a studeného úložiště téměř v reálném čase a v dávkových analýzách.

Shromážděná data procházejí řadou zpracování, která je čistí a dát pro scénáře dotazování a analýzy pro příjem dat. Azure nabízí obsáhlé služby, které se dají použít na scénáře IoT, jako je údržba a výroba prostředků. Mezi tyto služby patří Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Power BI.

Architekturu řešení lze dosáhnout následujícím způsobem:

* Ingestujte data prostřednictvím IoT Hub nebo Event Hubs pro nejlepší zabezpečení, propustnost a latenci v rámci své třídy.
* Provádění zpracování dat a výpočtů. Ingestovaná data prostřednictvím služby, jako jsou Stream Analytics, Logic Apps a Azure Functions. Služba, kterou použijete, závisí na konkrétních potřebách zpracování dat.
* Vypočítané signály z kanálu zpracování jsou vloženy do Time Series Insights pro ukládání a analýzu.

Time Series Insights nabízí zkoumání dat v reálném čase a přehledy založené na prostředcích nad historickými daty. V závislosti na potřebách vaší firmy můžete úlohy MapReduce a podregistru spouštět na datech uložených v Time Series Insights připojením Time Series Insights k Azure HDInsight. Data uložená v Time Series Insights jsou k dispozici pro Power BI a další zákaznické aplikace prostřednictvím rozhraní API pro dotazy na veřejné ploše Time Series Insights. Tato data je možné využít ve scénářích obsáhlé podnikání a provozní logiky.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrujte s pokročilými analytickými službami, jako jsou Machine Learning a Azure Databricks. Time Series Insights příchozí data z milionů zařízení. Přidává kontextová data, která lze hladce spotřebovat sadou služeb Azure Analytics.

[![Analýzy](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Pokročilé analýzy a strojové učení využívají a zpracovávají velké objemy dat. Tato data se používají k provádění rozhodnutí řízených daty a k provádění prediktivní analýzy. V případech použití v IoT se algoritmy pro pokročilou analýzu učí od dat shromážděných z milionů zařízení. Tato zařízení odesílají data vícekrát každou sekundu. Data shromážděná ze zařízení IoT jsou nepracovaná. Chybí kontextové informace, jako je umístění zařízení a jednotka, ve které se senzor čte. V důsledku toho je nezpracovaných dat obtížné spotřebovat přímo pro pokročilé analýzy.

VyTime Series Insights mosty mezi daty IoT a pokročilými analýzami ve dvou jednoduchých a nákladově efektivních způsobech:

* Nejdřív Time Series Insights shromažďuje hrubá data telemetrie z milionů zařízení pomocí IoT Hub. Rozšiřuje data o kontextové informace a transformuje data do formátu Parquet. Tento formát se může snadno integrovat s jinými službami pokročilé analýzy, jako jsou Machine Learning, Azure Databricks a aplikace třetích stran.

    Time Series Insights může sloužit jako zdroj pravdy pro všechna data v celé organizaci. Vytvoří centrální úložiště pro využití úloh pro podřízenou analýzu. Vzhledem k tomu, že Time Series Insights je téměř v reálném čase služba úložiště, pokročilé analytické modely se můžou průběžně učit ze příchozích dat telemetrie IoT. V důsledku toho mohou modely dosáhnout přesnější předpovědi.

* Za druhé se dá výstup modelů strojového učení a předpovědí doplňovat do Time Series Insights k vizualizaci a uložení jejich výsledků. Tento postup pomáhá organizacím optimalizovat a vylepšit jejich modely. Time Series Insights usnadňuje vizualizaci streamování dat telemetrie na stejné rovině, jako jsou například vyškolené výstupy modelu. Tímto způsobem pomáhají týmy pro datové vědy odhalit anomálie a identifikovat vzory.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [aplikaci Time Series Insights Preview](./time-series-insights-update-explorer.md).
* Přečtěte si téma [plánování Time Series Insights Preview](./time-series-insights-update-plan.md) a naplánujte si prostředí.
* Přečtěte si dokumentaci ke [klientovi Time Series Insights](https://github.com/Microsoft/tsiclient) .
