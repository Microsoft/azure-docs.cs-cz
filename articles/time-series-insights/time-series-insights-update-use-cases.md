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
ms.date: 11/27/2018
ms.openlocfilehash: 9d9fab9f0a515cacdf2a1425c4da06c9e3d4c364
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855877"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Případy použití služby Azure Time Series Insights (preview)

Tento článek poskytuje přehled o několik běžných případů použití pro Azure Time Series Insights (TSI). Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikací a řešení s využitím služby TSI.

Po přečtení tohoto článku, budete moci odpovědět na následující otázky:

* Jaké jsou běžné případy použití pro Azure TSI?
* Jaké jsou výhody používání Azure TSI pro zkoumání dat a detekci anomálií visual?
* Jaké jsou výhody používání Azure TSI provozní analýzy a efektivitu procesů?
* Jaké jsou výhody používání Azure TSI pro pokročilou analýzu?

Tento dokument obsahuje přehled, které Azure čas Series Insights privátní verze Preview je navržená pro případy použití.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Zkoumání dat a vizuální anomálií

Okamžité zkoumání a analýzy miliard událostí k detekovat anomálie a odhalovat skryté trendy ve vašich datech. Azure TSI poskytuje téměř v reálném čase výkonu pro úlohy analýzy IoT a DevOps.

![Průzkumník dat][1]

Všech síly Azure TSI většina zákazníků souhlasí s tím, že čas insight je mezi silnějšího. TSI nevyžaduje žádnou přípravu dat předem a funguje rychle a připojí vás k miliardám událostí ve službě Azure IoT Hub nebo Event Hub během několika minut.  Po připojení můžete vizualizovat a analyzovat miliardy událostí k detekovat anomálie a odhalovat skryté trendy ve vašich datech.  Protože TSI je intuitivní a snadno se používá, můžete začít zpracovávat s daty, aniž byste museli napsat jediný řádek kódu. Nemusíte se učit žádný nový jazyk. Time Series Insights poskytuje granulární textové dotazování pro pokročilé uživatele, kteří znají SQL, i jednoduché postupy typu vybrat a kliknout pro naprosté nováčky.

Vidíme zákazníkům využívat rychlost rychle diagnostikovat problémy související s asset při provádění DevOps, abyste se dostali k původní příčině chyby v řešení IoT a identifikovat oblasti hlediska datové vědy iniciativy.  

Existují tři hlavní způsoby, jak pracovat s daty uloženými ve službě TSI:

1. První a nejjednodušší vám umožní začít se naší vizualizaci, Průzkumník, která umožňuje rychle vizualizovat všechna vaše data IoT na jednom místě. Nabízí nástroje, jako je heatmapu, který zjednodušení vizuální zjistíte anomálie v datech, jakož i perspektivním zobrazení, které vám umožní porovnat až čtyři zobrazení z jednoho nebo více prostředí TSI v jednom řídicím panelu, získáte přehled o datech časových řad napříč všechna umístění. Další informace o [Průzkumníku TSI](./time-series-insights-update-explorer.md). Plán aktualizace prostředí TSI, přečtěte si [plánování aktualizace TSI](./time-series-insights-update-plan.md).

1. Druhý způsob je použít naše JavaScript SDK rychle vkládat výkonné tabulky a grafy ve webové aplikaci. Pomocí několika řádků kódu můžete vytvářet výkonné dotazy k naplnění spojnicové grafy, výsečové grafy, pruhové grafy, Heat mapy, datových mřížek a další. Všechny tyto prvky existovat out-of-the-box pomocí sady SDK. SDK také abstrahuje TSI dotaz rozhraní API umožňuje vytvářet predikáty podobném SQL k dotazování na data, které chcete zobrazit na řídicím panelu. Prezentační vrstva hybridní řešení nabízí TSI parametrizovaných adres URL, které poskytují body bezproblémové připojení s Průzkumníkem pro podrobné komentáře na data. Další informace o sadě SDK JavaScript, [TSI JS Klientská knihovna](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) a [TSI klienta](https://github.com/Microsoft/tsiclient) dokumentaci. Další informace o parametrizovaných adres URL, přečtěte si náš článek o [parametrizované adresy URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Nakonec TSI poskytuje výkonné rozhraní API pro dotazování na data uložená ve službě TSI. TSI má dočasné operátory, třeba z, první a poslední, agregace a transformace, jako je průměr, min, max, rozdělit podle, order a DateHistogram a filtrování operátory, jako má v a, nebo větší než, REGEX, atd. Tyto operátory povolit příjem dat aplikace a rychle najít zajímavé trendů a vzorů ve vašich datech a slouží k naplnění uživatelské vizualizace detekovat anomálie.  

Další informace o nabídky Azure for IoT, najdete v části [vytvářejte Internet vlastních věcí](https://www.microsoft.com/internet-of-things).

## <a name="operational-analysis-and-driving-process-efficiency"></a>Provozní analýzy a efektivity řízení procesu

Povolte monitorování stavu, využití a výkonu zařízení ve velkém měřítku, a poskytuje snadný způsob, jak měřit provozní efektivitu. Time Series Insights pomáhá se správou různorodým a nepředvídatelným úlohám IoT bez omezení ingestování nebo výkonu dotazů.

![overview][2]

Streamování a průběžné zpracování dat přicházejících z provozních procesů lze úspěšně transformovat jakoukoli firmu Pokud správné technologie a řešení s velkou provázaností. Tato řešení jsou často kombinace více systémů, které umožňují zkoumání a analýzy dat, která se neustále mění speciálně ve sféře IoT. Tyto systémy společně světla si scénáře, které sdílet běžným postupem při rozhodování o zpracování příjmu, zpracování, ukládání, analýza a vizualizace dat IoT.

Součást řešení, systémů potřeba ingestovat miliardy událostí ze zařízení a senzorů pokrývání uzlů různá národní prostředí. Tyto systémy dále zpracovávat a analyzovat streamovaná data k závěry v reálném čase. Data se potom archivovat do horké a studené úložiště pro téměř reálném čase a dávkové analýzy.

Tyto systémy dále třeba proces, který údajů umožňuje čistit a contextualization při ukládání dat pro povolení podřízené scénáře dotazování a analýzy. Microsoft Azure nabízí bohaté služby, které mohou být použity pro tyto scénáře IoT, včetně Azure TSI, Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning a Microsoft Power BI.

Pomocí výše uvedených nastavení řešení je možné ingestovat data prostřednictvím Azure IoT nebo Služba Event Hubs protože nabízí vysokou propustnost příjem dat s nízkou latencí. Data ingestována, kterou je potřeba zpracovat za vytváření přehledů v reálném čase můžete funneled Azure Stream Analytics, Azure Logic Apps a Azure Functions. Výsledkem pak lze vkládat do Power BI pro v reálném čase mnoha, jakož i lze načíst do služby Azure Time Series Insights pro upozorňování a monitorování, porovnejte ji historických synchronizace replik indexů. Data ingestována, které potřebuje zkoumání dat v téměř reálném čase nebo ad hoc dotazování na historické změny trendů, je možné načíst přímo do Azure Time Series Insights. Data načítají je připraven k dotazování spolu s neomezenou historická data pro provozní analýzy a analýzy k optimalizaci procesů pro maximální efektivitu. Všechna data nebo jenom změny dat v, který načten jako poslední může sloužit jako referenční data jako součást analýzy v reálném čase. Data navíc další může být kontrast a zpracovává data Azure Time Series Insights připojení k HDInsight pro mapování/zmenšování, Hive, úlohy atd. Nakonec zpřístupnění těchto dat v Power BI a v jakékoli aplikace pro zákazníky prostřednictvím našich veřejných surface dotaz rozhraní API.

## <a name="advanced-analytics"></a>Pokročilé analýzy

Integrace se službami pokročilou analýzu, jako je Azure Machine Learning a Azure Databricks. TSI ingresses nezpracovaných dat z milionů zařízení a přidá kontextové údaje, které mohou bez problémů využívat sadu služeb analýzy Azure.

![analytics][3]

Pokročilé analýzy a machine learningu spotřebovat a zpracovat velké objemy dat pro datově řízená rozhodnutí a provádět prediktivní analýzy. V případech IoT pomocí pokročilých analytických algoritmů Učte se od data shromážděná z milionů zařízení, které můžou přenášet data více než jednou za sekundu. Data shromážděná ze zařízení IoT je však raw a nemá kontextové informace, jako je například umístění zařízení, jednotka čtení snímačů atd. Tato data nejde použít přímo pro pokročilou analýzu.

Azure TSI překlenuje propast mezi IoT dat a pokročilých analýz v jednoduché a nákladově efektivní způsob. TSI shromažďuje nezpracovaná telemetrická data z miliónů zařízení, rozšíří dat pomocí kontextové informace a transformuje data do parquet formátu, který umožňuje snadnou integraci s celou řadou Azure pokročilé analytické služby jako Azure Machine Learning, Azure DataBricks a aplikace třetích stran. Pokročilých analytických modelů můžete průběžně Učte se od příchozí telemetrická data IoT k vytváření přesnějších predikcí.

## <a name="next-steps"></a>Další postup

* Další informace o [Průzkumníku TSI](./time-series-insights-update-explorer.md).

* Naplánujte si prostředí, přečtěte si téma [TSI (preview) plánování](./time-series-insights-update-plan.md).

* Čtení [TSI klienta](https://github.com/Microsoft/tsiclient) dokumentaci.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.png
[2]: media/v2-update-use-cases/overview.png
[3]: media/v2-update-use-cases/advanced-analytics.png
