---
title: Náhled případů použití – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si o případech použití přehledů Azure Time Series Insights Preview.
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
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights Náhled případy použití

Tento článek shrnuje několik běžných případů použití pro Azure Time Series Insights Preview. Doporučení v tomto článku slouží jako výchozí bod pro vývoj aplikací a řešení s Time Series Insights.

Konkrétně tento článek odpovídá na následující otázky:

* Jaké jsou běžné případy použití pro Time Series Insights?
* Jaké jsou výhody používání Time Series Insights pro [zkoumání dat a detekci vizuálních anomálií?](#data-exploration-and-visual-anomaly-detection)
* Jaké jsou výhody používání Time Series Insights pro [provozní analýzu a efektivitu procesů?](#operational-analysis-and-driving-process-efficiency)
* Jaké jsou výhody používání time series insights pro [pokročilé analýzy?](#advanced-analytics)

Přehled těchto scénářů použití je popsán v následujících částech.

## <a name="introduction"></a>Úvod

Azure Time Series Insights je komplexní nabídka platformy jako služby. Používá se ke shromažďování, zpracování, ukládání, analýze a dotazování vysoce kontextualizovaných dat optimalizovaných pro časové řady IoT. Time Series Insights je ideální pro průzkum ad hoc dat a provozní analýzu. Time Series Insights je jedinečně rozšiřitelná, přizpůsobená nabídka služeb, která splňuje široké potřeby průmyslových nasazení IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Zkoumání dat a detekce vizuálních anomálií

Okamžitě prozkoumejte a analyzujte miliardy událostí, abyste odhalili anomálie a objevili skryté trendy ve vašich datech. Time Series Insights poskytuje téměř v reálném čase výkon pro vaše úlohy analýzy IoT a DevOps.

[![Průzkumník dat](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Většina zákazníků souhlasí s tím, že minimální množství času potřebného k získání přehledu je jednou z výjimečných funkcí Time Series Insights:

* Time Series Insights nevyžaduje žádnou přípravu předem na data. 
* Funguje rychle a během několika minut vás připojí k miliardám událostí ve vašich instancích Azure IoT Hub nebo Azure Event Hubs. 
* Po připojení si můžete vizualizovat a analyzovat miliardy událostí, abyste odhalili anomálie a objevili skryté trendy ve vašich datech.

Time Series Insights je intuitivní a snadno ovladatelný. S daty můžete pracovat bez psaní jediného řádku kódu. K dispozici je také žádný nový jazyk, který je třeba se učit, i když Time Series Insights poskytuje podrobný text založený dotazovací jazyk pro pokročilé uživatele, kteří jsou obeznámeni s SQL. Poskytuje také průzkum pro nováčky a výběr.

Zákazníci mohou využít rychlost i k rychlé diagnostice problémů souvisejících s aktivy. Mohou provést analýzu DevOps, aby se dostali k hlavní příčině chyby v řešení IoT. Mohou také identifikovat oblasti, které mají být označeny příznakem pro další vyšetřování v rámci svých iniciativ v oblasti datové vědy. 

Existují tři primární způsoby interakce s daty uloženými v přehledech time series:

* První a nejjednodušší způsob, jak začít, je pomocí průzkumníka Náhled přehledů time series. Můžete ji použít k rychlé vizualizaci všech dat IoT na jednom místě. Poskytuje nástroje, jako je tepelná mapa, které vám pomohou odhalit anomálie ve vašich datech. Poskytuje také perspektivní pohled. Použijte ji k porovnání až čtyř zobrazení z jednoho nebo více prostředí Time Series Insights na jednom řídicím panelu. Řídicí panel poskytuje zobrazení dat časových řad ve všech lokalitách. Přečtěte si další informace o [průzkumníku náhledu přehledů time series](./time-series-insights-update-explorer.md). Chcete-li naplánovat prostředí Time Series Insights, přečtěte si [plánování přehledů time series](./time-series-insights-update-plan.md).

* Druhým způsobem, jak začít, je použít sadu JavaScript SDK k rychlému vložení výkonných grafů a grafů do webové aplikace. Pomocí několika řádků kódu můžete vytvářet výkonné dotazy. Použijte je k naplnění spojnicových grafů, výsečových grafů, pruhových grafů, tepelných map, datových mříží a dalších. Všechny tyto prvky existují out-of-the-box pomocí sady SDK. Sada SDK také abstrahuje api dotazu Time Series Insights. Můžete je použít k vytváření predikátů podobných SQL k dotazování na data, která chcete zobrazit na řídicím panelu. Pro hybridní řešení prezentačnívrstvy nabízí Time Series Insights parametrizované adresy URL. Poskytují bezproblémové spojovací body s průzkumníkem Náhled Time Series Insights pro hluboké ponoření do dat.

  * Další informace o sadě JavaScript SDK najdete v [části OS Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) a dokumentaci [klienta Time Series Insights.](https://github.com/Microsoft/tsiclient)

  * Další informace o sdílení adres URL a nového uhlavního rozhraní najdete v průzkumníku Náhled [přehledů Azure Time Series](time-series-insights-update-explorer.md).

* Třetím způsobem, jak začít, je použití výkonných api k dotazování dat uložených v Přehledech časových řad. Time Series Insights má časové `from` `to`operátory, jako jsou , , `first`a `last`. Má agregace a transformace, `average`jako `min` `max`jsou `split by` `order by`, `DateHistogram`, , , a . Má také filtrování operátory, `has`jako `or` `greater than`jsou `REGEX`, `in`, `and`, , a . Všechny tyto operátory umožňují následným aplikacím rychle najít zajímavé trendy a vzory ve vašich datech. Použijte je k naplnění domácích vizualizací, abyste namístějí anomálie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýza a efektivita jízdních procesů

Pomocí přehledů time series můžete sledovat stav, využití a výkon zařízení ve velkém měřítku. Time Series Insights poskytuje snadný způsob měření provozní efektivity. Time Series Insights pomáhá spravovat různé a nepředvídatelné úlohy IoT bez obětování ingestování nebo výkonu dotazů.

[![Přehled](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streamování a průběžné zpracování dat pocházejících z provozních procesů může úspěšně transformovat jakoukoli firmu, pokud je kombinováno se správnou technologií nebo řešením. Často jsou tato řešení kombinací více systémů. Umožňují zkoumání a analýzu dat, která se neustále mění, zejména ve sféře IoT, a sdílejí společný vzorec.

Tyto vzory často začínají platformami s podporou IoT, které ingestují miliardy událostí ze zařízení a senzorů, které pokrývají různá národní prostředí. Tyto systémy zpracovávají a analyzují streamovaná data za účelem odvození přehledů a akcí v reálném čase. Data se obvykle archivují do teplého a studeného úložiště pro analýzu téměř v reálném čase a dávkovou analýzu.

Shromážděná data procházejí řadou zpracování, aby je očistila a zaosávala do kontextu pro scénáře následného dotazování a analýzy. Azure nabízí bohaté služby, které se dá použít na scénáře IoT, jako je údržba aktiv a výroba. Mezi tyto služby patří Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Power BI.

Architekturu řešení lze dosáhnout následujícím způsobem:

* Ingestování dat prostřednictvím služby IoT Hub nebo centra událostí pro nejlepší zabezpečení, propustnost a latenci ve své třídě.
* Provádějte zpracování dat a výpočty. Daty na cesty prostřednictvím služeb, jako je Stream Analytics, Logic Apps a Azure Functions. Služba, kterou používáte, závisí na konkrétních potřebách zpracování dat.
* Vypočítané signály z kanálu zpracování jsou zasunuty do time series insights pro ukládání a analýzy.

Time Series Insights nabízí téměř průzkum dat v reálném čase a přehledy založené na aktivech nad historickými daty. V závislosti na vašich obchodních potřebách můžou úlohy MapReduce a Hive běžet na datech uložených v Přehledech časové řady propojení pomocí přehledů Time Series Insights s Azure HDInsight. Data uložená v Time Series Insights jsou dostupná pro Power BI a další zákaznické aplikace prostřednictvím rozhraní API pro dotazy na veřejné povrchové dotazy Time Series Insights. Tato data lze použít pro scénáře hluboké obchodní a provozní inteligence.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrujte s pokročilými analytickými službami, jako je Machine Learning a Azure Databricks. Time Series Insights pronájde nezpracovaná data z milionů zařízení. Přidává kontextová data, která můžou bez problémů spotřebovávat sada analytických služeb Azure.

[![Analýza](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Pokročilá analytika a strojové učení spotřebovávají a zpracovávají velké objemy dat. Tato data se používají k rozhodování založenému na datech a provádění prediktivní analýzy. V případech použití IoT se pokročilé analytické algoritmy učí z dat shromážděných z milionů zařízení. Tato zařízení přenášejí data několikrát za sekundu. Data shromážděná ze zařízení IoT jsou nezpracovaná. Postrádá kontextové informace, jako je umístění zařízení a jednotka snímače čtení. V důsledku toho je obtížné využívat nezpracovaná data přímo pro pokročilou analýzu.

Time Series Insights překlene propast mezi daty IoT a pokročilou analýzou dvěma jednoduchými a nákladově efektivními způsoby:

* Za prvé Time Series Insights shromažďuje nezpracovaná telemetrická data z milionů zařízení pomocí služby IoT Hub. Obohacuje data o kontextové informace a transformuje data do formátu parket. Tento formát lze snadno integrovat s dalšími pokročilými analytickými službami, jako je Machine Learning, Azure Databricks a aplikace třetích stran.

    Time Series Insights může sloužit jako zdroj pravdy pro všechna data v celé organizaci. Vytvoří centrální úložiště pro úlohy navazující analýzy spotřebovávat. Vzhledem k tomu, že Time Series Insights je služba úložiště téměř v reálném čase, pokročilé analytické modely se mohou neustále učit z příchozích telemetrických dat IoT. V důsledku toho mohou modely provádět přesnější předpovědi.

* Za druhé, výstup modelů strojového učení a predikce lze připnuje do Time Series Insights vizualizovat a ukládat jejich výsledky. Tento postup pomáhá organizacím optimalizovat a vyladit své modely. Time Series Insights usnadňuje vizualizaci streamovaných telemetrických dat ve stejné rovině jako výstupy trénovaného modelu. Tímto způsobem pomáhá týmům datových věd rozpoznat anomálie a identifikovat vzory.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [průzkumníku náhledu přehledů time series](./time-series-insights-update-explorer.md).
* Přečtěte si [plánování náhledu Time Series Insights a](./time-series-insights-update-plan.md) naplánujte si prostředí.
* Přečtěte si dokumentaci [klienta Time Series Insights.](https://github.com/Microsoft/tsiclient)
