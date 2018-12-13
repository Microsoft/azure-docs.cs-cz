---
title: Případy použití služby Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Případy použití Principy Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 67be21ae7f0cb997563f17130b9d5ecb7d359b31
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873865"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Případy použití služby Azure Time Series Insights (Preview)

Tento článek poskytuje přehled o několik běžných případů použití pro Azure Time Series Insights (TSI). Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikací a řešení s využitím služby TSI.

Po přečtení tohoto článku, budete moci odpovědět na následující otázky:

* Jaké jsou běžné případy použití pro Azure TSI?
* Jaké jsou výhody používání Azure TSI pro zkoumání dat a detekci anomálií visual?
* Jaké jsou výhody používání Azure TSI provozní analýzy a efektivitu procesů?
* Jaké jsou výhody používání Azure TSI pro pokročilou analýzu?

Tento dokument obsahuje přehled, které Azure TSI Private Preview je navržená pro případy použití.

## <a name="introduction"></a>Úvod

Azure TSI je začátku do konce Platform-As-A-Service k ingestování, zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas. V důsledku toho je ideální pro ad-hoc zkoumání dat, jakož i provozní analýzy Azure TSI. TSI je jednoznačně rozšiřitelný, přizpůsobené, služba, že splňuje širší potřebuje průmyslového IoT nasazení.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Zkoumání dat a vizuální detekce anomálií

Detekujte anomálie a odhalujte skryté trendy ve vašich datech díky okamžitému zkoumání a analýze miliard událostí. Azure TSI poskytuje téměř v reálném čase výkonu pro úlohy analýzy IoT a DevOps.

![Průzkumník dat][1]

Všech síly Azure TSI většina zákazníků souhlasí s tím, že čas insight je mezi silnějšího. TSI nevyžaduje žádnou přípravu dat předem a funguje rychle a připojí vás k miliardám událostí ve službě Azure IoT Hub nebo Event Hub během několika minut.  Po připojení můžete vizualizovat a analyzovat miliardy událostí k detekovat anomálie a odhalovat skryté trendy ve vašich datech.  Protože TSI je intuitivní a snadno se používá, můžete začít zpracovávat s daty, aniž byste museli napsat jediný řádek kódu. Nemusíte se učit žádný nový jazyk. Time Series Insights poskytuje granulární textové dotazování pro pokročilé uživatele, kteří znají SQL, i jednoduché postupy typu vybrat a kliknout pro naprosté nováčky.

Vidíme zákazníkům využívat rychlost rychle diagnostikovat problémy související s asset při provádění DevOps, abyste se dostali k původní příčině chyby v řešení IoT a identifikovat oblasti hlediska datové vědy iniciativy.  

Existují tři hlavní způsoby, jak pracovat s daty uloženými ve službě TSI:

1. První a nejjednodušší vám umožní začít se naší vizualizaci, Průzkumník, která umožňuje rychle vizualizovat všechna vaše data IoT na jednom místě. Poskytuje nástroje, jako je heatmapu, které zjednodušení vizuální zjistíte anomálie v datech. Poskytuje také perspektivním zobrazení, která umožňuje porovnávání až čtyři zobrazení z jednoho nebo více prostředí TSI v jednom řídicím panelu, získáte přehled o datech časových řad napříč všemi umístěními. Další informace o [Průzkumníku TSI](./time-series-insights-update-explorer.md). Plán aktualizace prostředí TSI, přečtěte si [plánování aktualizace TSI](./time-series-insights-update-plan.md).

1. Druhý způsob je použít naše JavaScript SDK rychle vkládat výkonné tabulky a grafy ve webové aplikaci. Pomocí několika řádků kódu můžete vytvářet výkonné dotazy k naplnění spojnicové grafy, výsečové grafy, pruhové grafy, Heat mapy, datových mřížek a další. Všechny tyto prvky existovat out-of-the-box pomocí sady SDK. SDK také abstrahuje TSI dotaz rozhraní API umožňuje vytvářet predikáty podobném SQL k dotazování na data, které chcete zobrazit na řídicím panelu. Prezentační vrstva hybridní řešení nabízí TSI parametrizovaných adres URL, které poskytují body bezproblémové připojení s Průzkumníkem pro podrobné komentáře na data. Další informace o sadě SDK JavaScript, [TSI JS Klientská knihovna](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) a [TSI klienta](https://github.com/Microsoft/tsiclient) dokumentaci. Další informace o parametrizovaných adres URL, přečtěte si náš článek o [parametrizované adresy URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Nakonec TSI poskytuje výkonné rozhraní API pro dotazování na data uložená ve službě TSI. TSI má dočasné operátory, třeba z, první a poslední, agregace a transformace, jako je průměr, min, max, rozdělit podle, order a DateHistogram a filtrování operátory, jako má v a, nebo větší než, REGEX, atd. Tyto operátory povolit příjem dat aplikace a rychle najít zajímavé trendů a vzorů ve vašich datech a slouží k naplnění uživatelské vizualizace detekovat anomálie.  

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýzy a zvýšení efektivity procesů

Povolte monitorování stavu, využití a výkonu zařízení ve velkém měřítku, a poskytuje snadný způsob, jak měřit provozní efektivitu. Time Series Insights pomáhá spravovat různorodé a nepředvídatelné úlohy IoT bez omezení ingestování nebo výkonu dotazů.

![overview][2]

Streamování a průběžné zpracování dat přicházejících z provozních procesů lze úspěšně transformovat jakoukoli firmu Pokud správné technologie a řešení s velkou provázaností. Tato řešení jsou často kombinace více systémů, které umožňují zkoumání a analýzy dat, která neustále mění speciálně ve sféře IoT a sdílet běžným vzorem.

Tyto vzory často start s IoT povolené platformy, které ingestování miliardy událostí ze zařízení a senzorů pokrývání uzlů různá národní prostředí. Tyto systémy zpracování a analýze streamovaných dat k odvození přehledy v reálném čase a akcí, Data se obvykle archivovat do horké a studené úložiště pro téměř reálném čase a dávkové analýzy. Data sbírána prochází řadu zpracování čistí a contextualize pro příjem dat scénáře, dotazování a analýzy. Microsoft Azure nabízí bohaté služby, které lze použít u těchto scénářů IoT (Asset údržby, výroby, atd.). Patří mezi ně Azure TSI, Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Microsoft Power BI.

Tato architektura řešení můžete dosáhnout následujícím způsobem – ingestování dat prostřednictvím služby Azure IoT Hub nebo Azure Event Hubs ve své třídě nejlepší zabezpečení, latence a propustnosti. Proveďte podle směrováním přijatých dat prostřednictvím služeb, jako je Azure Stream Analytics, Azure Logic Apps, Azure Functions v závislosti na potřebách určité zpracování dat zpracovávaných dat a výpočty. Vypočítané signály z kanálu zpracování se nasdílejí do Azure TSI pro ukládání a analýzu. Azure Time Series Insights nabízí téměř zkoumání dat v reálném čase a přehledy založené na asset historická data. V závislosti na obchodní potřeby můžete spustit úlohy MapReduce a Hivu s daty uloženými v Time Series Insights pomocí připojení k HDInsight Time Series Insights. Data uložená v Time Series Insights můžete k dispozici pro Power BI a dalších zákaznických aplikací přes Time Series Insights veřejné surface dotaz rozhraní API pro podrobné obchodní a provozní informace scénáře.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrace se službami pokročilou analýzu, jako je Azure Machine Learning a Azure Databricks. Time Series Insights přijímá nezpracovaná data z milionů zařízení a přidává kontextová data, která může bez problémů využívat sada analytických služeb Azure.

![analytics][3]

Pokročilé analýzy a machine learningu spotřebovat a zpracovat velké objemy dat pro datově řízená rozhodnutí a provádět prediktivní analýzy. V případech IoT pomocí pokročilých analytických algoritmů Učte se od data shromážděná z milionů zařízení, které můžou přenášet data více než jednou za sekundu. Data shromážděná ze zařízení IoT je však raw a nemá kontextové informace, jako je například umístění zařízení, jednotka čtení snímačů atd, díky čemuž obtížné data využívat přímo pro pokročilou analýzu.

Azure Time Series Insights překlenuje propast mezi IoT dat a pokročilých analýz dvěma způsoby jednoduché a nákladově efektivní. Nejprve aktualizace Time Series Insights shromažďuje nezpracovaná telemetrická data z miliónů zařízení pomocí služby IoT hub, rozšíří dat pomocí kontextové informace a transformuje data do parquet formátu, na který umožní snadnou integraci s celou řadou pokročilé analytické služby Azure Machine Learning, Azure Databricks a jiné aplikace třetí strany.  Time Series Insights může sloužit jako zdroj sady pravdy pro všechna data celé organizaci, tedy vytvoření centrální úložiště pro příjem dat analýzy úloh, které budete používat.  Od služby Time Series Insights je téměř v reálném čase úložiště služby, pokročilé analýzy, které modely můžete průběžně Učte se od příchozí telemetrická data IoT k vytváření přesnějších predikcí.

Za druhé Time Series Insights může dodáni výstup modelů strojového učení a prediktivní vkládání vizualizovat a ukládání výsledků, proto pomáhá organizacím k optimalizaci a upravit jejich modelů.  A co víc Time Series Insights usnadňuje vizualizace, streamování telemetrická data v téže rovině jako trénovaného modelu výstupy umožňující datové vědy týmy detekovat anomálie a identifikovat vzory.  

## <a name="next-steps"></a>Další postup

Další informace o [Průzkumníku TSI](./time-series-insights-update-explorer.md).

Naplánujte si prostředí, přečtěte si téma [TSI (Preview) plánování](./time-series-insights-update-plan.md).

Čtení [TSI klienta](https://github.com/Microsoft/tsiclient) dokumentaci.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
