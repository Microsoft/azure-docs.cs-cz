---
title: Případy použití Gen2 – Azure Time Series Insights Gen2 | Microsoft Docs
description: Přečtěte si o Azure Time Series Insights případech použití Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606959"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Případy použití Azure Time Series Insights Gen2

Tento článek shrnuje několik běžných případů použití Azure Time Series Insights Gen2. Doporučení v tomto článku slouží jako výchozí bod pro vývoj aplikací a řešení pomocí Azure Time Series Insights Gen2.

Konkrétně tento článek obsahuje odpovědi na následující otázky:

* Jaké jsou běžné případy použití Azure Time Series Insights Gen2?
* Jaké jsou výhody použití Azure Time Series Insights Gen2 pro [zkoumání dat a detekci vizuální anomálií](#data-exploration-and-visual-anomaly-detection)?
* Jaké jsou výhody použití Azure Time Series Insights Gen2 pro [provozní analýzu a efektivitu procesu](#operational-analysis-and-driving-process-efficiency)?
* Jaké jsou výhody použití Azure Time Series Insights Gen2 pro [pokročilou analýzu](#advanced-analytics)?

Přehled těchto scénářů použití je popsaný v následujících částech.

## <a name="introduction"></a>Úvod

Azure Time Series Insights Gen2 je ucelená nabídka typu platforma jako služba. Používá se ke shromažďování, zpracování, ukládání, analýze a dotazování vysoce kontextových dat IoT škály, které jsou optimalizované pro časovou řadu. Je ideální pro zkoumání a provozní analýzu dat ad hoc. Azure Time Series Insights Gen2 je jedinečná rozšiřitelná a přizpůsobená nabídka služeb, která splňuje široké požadavky na nasazení v průmyslu IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Detekce zkoumání dat a vizuální anomálii

Okamžitě Prozkoumejte a analyzujte miliardy událostí a zjistěte anomálie a Objevte skryté trendy ve vašich datech. Azure Time Series Insights Gen2 zajišťuje výkon pro úlohy analýzy IoT a DevOps téměř v reálném čase.

[![Průzkumník dat](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Většina zákazníků souhlasí s tím, že minimální doba potřebná k získání přehledu je jednou z největší funkcí Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 nevyžaduje žádnou přípravu dat předem.
* Umožňuje vám rychle se připojit k miliardám událostí ve službě Azure IoT Hub nebo instancích Azure Event Hubs v řádu minut.
* Po připojení můžete vizualizovat a analyzovat miliardy událostí a odhalit anomálie a zjišťovat skryté trendy ve vašich datech.

Azure Time Series Insights Gen2 je intuitivní a snadno se používá. S daty můžete pracovat, aniž byste museli psát jediný řádek kódu. K dispozici není také žádný nový jazyk, který byste si museli učit, i když Azure Time Series Insights Gen2 poskytuje podrobný dotazovací jazyk založený na textu pro pokročilé uživatele obeznámené s SQL. Nabízí také průzkum vybrat a kliknout pro ně.

Zákazníci můžou využít rychlost a rychle diagnostikovat problémy související s assety. Můžou provádět analýzu DevOps a získat tak hlavní příčinu chyby v řešení IoT. Můžou také označovat oblasti, které je potřeba označit k dalšímu šetření v rámci svých iniciativ v oblasti datových věd.

Existují tři základní způsoby, jak pracovat s daty uloženými v Azure Time Series Insights Gen2:

* První a nejjednodušší způsob, jak začít, je Azure Time Series Insights Gen2 Explorer. Můžete ji použít k rychlému vizualizaci všech dat IoT na jednom místě. Poskytuje nástroje jako Heat mapu, které vám pomůžou odhalit anomálie ve vašich datech. Nabízí také pohled na perspektivu. Pomocí ní můžete porovnat až čtyři zobrazení z jednoho nebo více Azure Time Series Insights Gen2 prostředí na jednom řídicím panelu. Řídicí panel poskytuje zobrazení dat časových řad napříč všemi vašimi umístěními. Přečtěte si další informace o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md). Pokud chcete naplánovat své prostředí, přečtěte si téma [plánování Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).

* Druhým způsobem, jak začít, je použít sadu JavaScript SDK k rychlému vkládání výkonných grafů a grafů do vaší webové aplikace. Pouze pár řádků kódu vám umožní vytvářet výkonné dotazy. Použijte je k naplnění spojnicových grafů, výsečových grafů, pruhových grafů, heatch map, datových mřížek a dalších. Všechny tyto prvky existují předem v dialogovém okně pomocí sady SDK. Sada SDK také abstrakce Azure Time Series Insights rozhraní API pro dotazy Gen2. Můžete je použít k vytváření predikátů podobných SQL k dotazování na data, která chcete zobrazit na řídicím panelu. V případě hybridních řešení prezentační vrstvy nabízí Azure Time Series Insights Gen2 parametrizované adresy URL. Poskytují bezproblémové spojovací body s Azure Time Series Insights Gen2 Explorer pro hluboké komentáře na data.

  * Přečtěte si o [klientské knihovně js](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) a [ukázkové dokumentaci klienta](https://github.com/Microsoft/tsiclient) , kde se dozvíte další informace o sadě JavaScript SDK.

  * Další informace o sdílení adres URL a novém uživatelském rozhraní najdete [v části vizualizace dat v Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* Třetí způsob, jak začít, je použít výkonné rozhraní API k dotazování na data uložená v Azure Time Series Insights Gen2. Azure Time Series Insights Gen2 obsahuje dočasné operátory, jako například `from` , `to` , `first` a `last` . Obsahuje agregace a transformace, jako například `average` ,, `sum` , `min` `max` , `time-weighted average` , `time-weighted sum` atd. Umožňuje také filtrování, aritmetické a logické operátory, skalární funkce atd. Všechny tyto operátory umožňují aplikacím pro příjem dat rychle najít zajímavé trendy a vzory ve vašich datech. Použijte je k naplnění vizualizací Homegrown pro zjištění anomálií.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýza a proces řízení efektivity

Pomocí Azure Time Series Insights Gen2 můžete monitorovat stav, využití a výkon zařízení ve velkém měřítku a měřit provozní efektivitu. Azure Time Series Insights Gen2 pomáhá spravovat různorodé a nepředvídatelné úlohy IoT bez omezení ingestování nebo výkonu dotazů.

[![Snímek obrazovky zobrazuje I/o zařízení/data aplikací, zpracování datových proudů, provozní efektivitu, Intelligence/Insights a pokročilé analýzy v Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streamování a průběžné zpracování dat přicházejících z provozních procesů může úspěšně transformovat libovolnou firmu, pokud je v kombinaci s pravou technologií nebo řešením. Tato řešení jsou často kombinací více systémů. Umožňují prozkoumání a analýzu dat, která se průběžně mění, zejména ve sféře IoT, a sdílejí společný vzor.

Tyto vzory často začínají s platformami podporujícími IoT, které ingestují miliardy událostí ze zařízení a senzorů, které jsou v různých národních prostředích. Tyto systémy zpracovávají a analyzují streamovaná data pro odvození přehledů a akcí v reálném čase. Data se obvykle archivují do teplého a studeného úložiště téměř v reálném čase a v dávkových analýzách.

Shromážděná data procházejí řadou zpracování, která je čistí a dát pro scénáře dotazování a analýzy pro příjem dat. Azure nabízí obsáhlé služby, které se dají použít na scénáře IoT, jako je údržba a výroba prostředků. Mezi tyto služby patří Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Power BI.

Architekturu řešení lze dosáhnout následujícím způsobem:

* Ingestujte data prostřednictvím IoT Hub nebo Event Hubs pro nejlepší zabezpečení, propustnost a latenci v rámci své třídy.
* Provádění zpracování dat a výpočtů. Ingestovaná data prostřednictvím služby, jako jsou Stream Analytics, Logic Apps a Azure Functions. Služba, kterou použijete, závisí na konkrétních potřebách zpracování dat.
* Vypočítané signály z kanálu zpracování se odešlou do Azure Time Series Insights Gen2 pro ukládání a analýzu.

Azure Time Series Insights Gen2 nabízí v reálném čase zkoumání dat a přehledy založené na prostředcích nad historickými daty. V závislosti na vašich obchodních potřebách se úlohy MapReduce a podregistr můžou spouštět na datech uložených v Azure Time Series Insights Gen2 připojením Azure Time Series Insights Gen2 k Azure HDInsight. Data uložená v Azure Time Series Insights Gen2 jsou k dispozici pro Power BI a další zákaznické aplikace prostřednictvím rozhraní API pro dotazy na veřejné ploše Azure Time Series Insights Gen2. Tato data je možné využít ve scénářích obsáhlé podnikání a provozní logiky.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrujte s pokročilými analytickými službami, jako jsou Machine Learning a Azure Databricks. Azure Time Series Insights Gen2 příchozí data z milionů zařízení. Přidává kontextová data, která lze hladce spotřebovat sadou služeb Azure Analytics.

[![Analýzy](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Pokročilé analýzy a strojové učení využívají a zpracovávají velké objemy dat. Tato data se používají k provádění rozhodnutí řízených daty a k provádění prediktivní analýzy. V případech použití v IoT se algoritmy pro pokročilou analýzu učí od dat shromážděných z milionů zařízení. Tato zařízení odesílají data vícekrát každou sekundu. Data shromážděná ze zařízení IoT jsou nepracovaná. Chybí kontextové informace, jako je umístění zařízení a jednotka, ve které se senzor čte. V důsledku toho je nezpracovaných dat obtížné spotřebovat přímo pro pokročilé analýzy.

Azure Time Series Insights Gen2 mosty mezi daty IoT a pokročilými analýzami ve dvou jednoduchých a nákladově efektivních způsobech:

* Nejdřív Azure Time Series Insights Gen2 shromažďuje hrubá data telemetrie z milionů zařízení pomocí IoT Hub. Rozšiřuje data o kontextové informace a transformuje data do formátu Parquet. Tento formát se může snadno integrovat s jinými službami pokročilé analýzy, jako jsou Machine Learning, Azure Databricks a aplikace třetích stran.

    Azure Time Series Insights Gen2 může sloužit jako zdroj pravdy pro všechna data v celé organizaci. Vytvoří centrální úložiště pro využití úloh pro podřízenou analýzu. Vzhledem k tomu, že Azure Time Series Insights Gen2 je téměř v reálném čase služba úložiště, pokročilé analytické modely se můžou průběžně učit ze příchozích dat telemetrie IoT. V důsledku toho mohou modely dosáhnout přesnější předpovědi.

* Za druhé se dá výstup modelů strojového učení a předpovědí doplňovat do Azure Time Series Insights Gen2 k vizualizaci a uložení jejich výsledků. Tento postup pomáhá organizacím optimalizovat a vylepšit jejich modely. Azure Time Series Insights Gen2 usnadňuje vizualizaci streamování dat telemetrie na stejné rovině, jako jsou například vyškolené výstupy modelu. Tímto způsobem pomáhají týmy pro datové vědy odhalit anomálie a identifikovat vzory.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).
* Přečtěte si [Azure Time Series Insights osvědčené postupy](./how-to-plan-your-environment.md) pro naplánování prostředí.
* Přečtěte si [ukázkovou dokumentaci klienta](https://github.com/Microsoft/tsiclient) .
