---
title: Případy použití Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Seznamte se s případy použití Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7625eb383beef2e86cc8a977362db09329a6ea1b
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556709"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Případy použití Azure čas Series Insights ve verzi Preview

Tento článek poskytuje přehled o několik běžných případů použití pro Azure čas Series Insights ve verzi Preview. Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikací a řešení s využitím Time Series Insights.

Po přečtení tohoto článku můžete odpovědět na následující otázky:

* Jaké jsou běžné případy použití pro Time Series Insights?
* Jaké jsou výhody používání služby Time Series Insights pro zkoumání dat a detekci anomálií visual?
* Jaké jsou výhody používání služby Time Series Insights pro provozní analýzy a efektivitu procesů?
* Jaké jsou výhody použití služby Time Series Insights pro pokročilou analýzu?

Tento dokument obsahuje přehled, které Azure čas Series Insights ve verzi Preview je navržená pro případy použití.

## <a name="introduction"></a>Úvod

Time Series Insights je nabídka platforma jako služba začátku do konce. Používá se k ingestování, zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas. Time Series Insights je ideální pro ad-hoc zkoumání dat a provozní analýzy. Time Series Insights je služba jednoznačně rozšiřitelný, přizpůsobené, nabídky, že splňuje širší potřebuje průmyslového IoT nasazení.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Zkoumání dat a vizuální detekce anomálií

Detekujte anomálie a odhalujte skryté trendy ve vašich datech díky okamžitému zkoumání a analýze miliard událostí. Time Series Insights poskytuje výkon pro zpracování vašich analytických úloh IoT a DevOps téměř v reálném čase.

![Průzkumník dat][1]

Většina zákazníků souhlasí s tím, že je čas insight mezi nejsilnější prostředky služby Time Series Insights. Time Series Insights nevyžaduje žádnou přípravu dat předem. Funguje to rychle do několika minut můžete připojit k miliardám událostí ve službě Azure IoT Hub nebo Azure Event Hubs. Po připojení můžete vizualizovat a analyzovat miliardy událostí k detekovat anomálie a odhalovat skryté trendy ve vašich datech. 

Time Series Insights je intuitivní a snadno se používá. Můžete pracovat s daty aniž byste museli napsat jediný řádek kódu. Je také se učit žádný nový jazyk. Time Series Insights poskytuje granulární textové dotazování pro pokročilé uživatele, kteří znají SQL. Poskytuje také zkoumání vybrat a kliknout pro naprosté nováčky.

Zákazníci využít rychlosti rychle diagnostikovat problémy související s asset. Mohou provádět DevOps se dostat k původní příčinu chyby v řešení IoT. Také můžou určit oblastí k prozkoumání pro datové vědy iniciativy.  

Existují tři hlavní způsoby, jak pracovat s daty uloženými v Time Series Insights:

- Je první a nejjednodušší způsob, jak začít s Průzkumníkem čas Series Insights ve verzi Preview. Můžete ji rychle vizualizovat všechna vaše data IoT na jednom místě. Poskytuje nástroje, jako je Heat mapě můžete detekovat anomálie v datech. Poskytuje také perspektivním zobrazení. Použije k porovnání až čtyři zobrazení z jednoho nebo více prostředí Time Series Insights v jednom řídicím panelu. Řídicí panel poskytuje přehled o datech časových řad napříč všemi umístěními. Další informace o [čas Series Insights ve verzi Preview explorer](./time-series-insights-update-explorer.md). Na plánování prostředí Time Series Insights, přečtěte si [Time Series Insights plánování](./time-series-insights-update-plan.md).

- Druhý způsob, jak začít, je použití sady SDK pro JavaScript rychle vkládat výkonné tabulky a grafy ve webové aplikaci. Pomocí několika řádků kódu můžete vytvářet výkonné dotazy. Použije je k naplnění spojnicové grafy, výsečové grafy, pruhové grafy, Heat mapy, datových mřížek a další. Všechny tyto prvky existovat out-of-the-box pomocí sady SDK. SDK také abstrahuje služby Time Series Insights dotaz rozhraní API. Můžete využít k vytváření predikáty podobném SQL k dotazování na data, které chcete zobrazit na řídicím panelu. Prezentační vrstva hybridní řešení služby Time Series Insights nabízí parametrizovaných adres URL. Poskytují body bezproblémové připojení s Průzkumníkem čas Series Insights ve verzi Preview pro podrobné komentáře na data. Další informace o sadě SDK JavaScript, [čas Series Insights JS Klientská knihovna](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) a [Time Series Insights klienta](https://github.com/Microsoft/tsiclient) dokumentaci. Další informace o parametrizovaných adres URL, přečtěte si [parametrizovaných adres URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- Je třetí způsob, jak začít používat Výkonné rozhraní API, provádět dotazy na data uložená v Time Series Insights. Time Series Insights je dočasné operátory, jako například, první a poslední. Má agregace a transformace, jako je průměr, min, max, rozdělit podle pořadí a DateHistogram. Zahrnuje také filtrování operátory, jako má, v a, nebo větší než počet a regulární VÝRAZ. Tyto operátory povolit příjem dat aplikace a rychle najít zajímavé trendů a vzorů ve vašich datech. Použije je k naplnění uživatelské vizualizace detekovat anomálie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýzy a zvýšení efektivity procesů

Time Series Insights umožňuje monitorovat stav, využití a výkon zařízení ve velkém měřítku. Time Series Insights poskytuje snadný způsob, jak měřit provozní efektivitu. Time Series Insights pomáhá spravovat různorodé a nepředvídatelné úlohy IoT bez omezení ingestování nebo výkonu dotazů.

![Přehled][2]

Vysílání datových proudů a průběžné zpracování dat přicházejících z provozních procesů lze úspěšně transformovat jakoukoli firmu Pokud se zkombinuje s správné technologie nebo řešení. Tato řešení jsou často kombinace více systémů. Umožňují zkoumání a analýzy dat, která změní neustále, zejména ve sféře IoT a běžně sdílí.

Tyto vzory často začínat platformy s podporou IoT, které ingestovat miliardy událostí ze zařízení a senzorů, které zahrnují různá národní prostředí. Tyto systémy zpracování a analýzu datových proudů dat k odvození přehledy v reálném čase a akce. Data se obvykle archivuje vyzkoušeli a studeného úložiště pro téměř v reálném čase a dávkové analýzy. 

Data sbírána prochází řadu zpracování čistí a contextualize pro příjem dat scénáře, dotazování a analýzy. Azure nabízí bohaté služby, které mohou být použity pro scénáře IoT, jako je například údržba asset a výrobu. Mezi tyto služby patří služby Time Series Insights, služby IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Power BI.

Architektura řešení můžete dosáhnout následujícím způsobem:

- Příjem dat prostřednictvím služby IoT Hub nebo Event Hubs ve své třídě nejlepší zabezpečení, latence a propustnosti. 
- Provádějte zpracování dat a výpočty. Trychtýře přijatých dat prostřednictvím služeb, jako je Stream Analytics, Logic Apps a Azure Functions. Službu, kterou použijete, závisí na specifické požadavky na zpracování dat. 
- Vypočítané signály z kanálu zpracování se nasdílejí do služby Time Series Insights pro ukládání a analýzu. 

Time Series Insights nabízí téměř zkoumání dat v reálném čase a přehledy založené na asset historická data. V závislosti na vaše obchodní potřeby můžete spustit úlohy MapReduce a Hivu s daty uloženými v Time Series Insights pomocí připojení k HDInsight Azure Time Series Insights. Data uložená v Time Series Insights je k dispozici pro Power BI a dalších zákaznických aplikací prostřednictvím veřejné surface dotazu Time Series Insights rozhraní API. Tato data je možné pro podrobné obchodní a provozní informace scénáře.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrace se službami pokročilou analýzu, jako je Machine Learning a Azure Databricks. Time Series Insights ingresses nezpracovaných dat z milionů zařízení. Přidá kontextové údaje, které mohou bez problémů využívat sadu analytické služby Azure.

![Analýzy][3]

Pokročilé analýzy a machine learningu spotřebovat a zpracovat velké objemy dat. Tato data slouží k datově řízená rozhodnutí a provádět prediktivní analýzy. V případech IoT pomocí pokročilých analytických algoritmů Učte se od data shromážděná z milionů zařízení. Tato zařízení přenášet data více než jednou za sekundu. Je nezpracovaná data shromážděná ze zařízení IoT. Postrádá kontextové informace, jako je umístění zařízení a jednotka čtení senzoru. V důsledku toho nezpracovaných dat je obtížné využívat přímo pro pokročilou analýzu.

Time Series Insights překlenuje propast mezi IoT dat a pokročilých analýz dva jednoduché a nákladově efektivní způsoby: 

- Nejprve Time Series Insights shromažďuje nezpracovaná telemetrická data z miliónů zařízení pomocí služby IoT Hub. Rozšíří dat pomocí kontextové informace a transformuje data do formátu parquet. Tento formát umožňuje snadnou integraci s další pokročilé analýzy služby, jako je Machine Learning, Azure Databricks a aplikací třetích stran. 

    Time Series Insights může sloužit jako zdroj pravdivých informací pro všechna data celé organizaci. Vytvoří centrální úložiště pro příjem dat analýzy úloh, které budete používat. Protože Time Series Insights je téměř v reálném čase úložiště služby, pokročilé analýzy, které modely můžete průběžně Učte se od příchozí telemetrická data IoT. Modely v důsledku toho může být přesnějších predikcí.

- Za druhé Time Series Insights může dodáni výstup modelů strojového učení a prediktivní vkládání vizualizovat a uložit jejich výsledky. Tento postup pomáhá organizacím k optimalizaci a upravit jejich modelů. Time Series Insights usnadňuje vizualizace, streamování telemetrická data v téže rovině jako výstupy trénovaného modelu. Tímto způsobem umožňuje datové vědy týmy detekovat anomálie a identifikovat vzory.  

## <a name="next-steps"></a>Další postup

- Další informace o [čas Series Insights ve verzi Preview explorer](./time-series-insights-update-explorer.md).
- Naplánujte si prostředí, přečtěte si téma [čas Series Insights ve verzi Preview plánování](./time-series-insights-update-plan.md).
- Čtení [Time Series Insights klienta](https://github.com/Microsoft/tsiclient) dokumentaci.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
