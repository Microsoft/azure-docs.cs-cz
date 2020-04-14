---
title: 'Přehled: Co je Přehledy Azure Time Series? - Přehledy azure time series | Dokumenty společnosti Microsoft'
description: Seznámení s Azure Time Series Insights, novou službou pro analýzu dat časových řad a řešení IoT
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 59149b2ca598104d8aca9b4e5e60194a8f6398bf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269954"
---
# <a name="what-is-azure-time-series-insights"></a>Co je Azure Time Series Insights?

Azure Time Series Insights je vytvořený pro ukládání, vizualizaci a dotazování velké množství dat časové řady, jako je například generované zařízeníi IoT. Pokud chcete ukládat, spravovat, dotazovat nebo vizualizovat data časových řad v cloudu, služba Time Series Insights je pro vás pravděpodobně to pravé.

[![Vývojový diagram služby Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Služba Time Series Insights má čtyři klíčové úlohy:

- Je plně integrovaná s cloudovými bránami, jako je Azure IoT Hub a Azure Event Hubs. Snadno se připojuje k těmto zdrojům událostí a parsuje JSON ze zpráv a struktur obsahujících data v jasně uspořádaných řádcích a sloupcích. Spojuje metadata s telemetrií a indexuje vaše data ve sloupcovém úložišti.
- Time Series Insights spravuje ukládání vašich dat. Chcete-li zajistit, aby data byla vždy snadno přístupná, ukládá data do paměti a disky SSD po dobu až 400 dnů. Můžete interaktivně dotaz miliardy událostí v sekundách-na vyžádání.
- Time Series Insights poskytuje inas uvázli vizualizaci prostřednictvím průzkumníka Time Series Insights.
- Time Series Insights poskytuje dotazovou službu, a to jak v průzkumníku Time Series Insights, tak pomocí rozhraní API, která lze snadno integrovat a vložit data časových řad do vlastních aplikací.

Pokud vytváříte aplikaci pro interní spotřebu nebo pro externí zákazníky, můžete použít Time Series Insights jako back-end. Můžete ji použít k indexování, ukládání a agregaci dat časových řad. Chcete-li vytvořit vlastní vizualizaci a uživatelské prostředí na vrcholu, použijte [client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights je také vybaven několika [query API](how-to-shape-query-json.md) povolit tyto přizpůsobené scénáře.

Data časových řad představují změnu prostředku nebo procesu v průběhu času. Data časových řad jsou indexována podle časových razítek a čas je nejsmysluplnější osou, podél které jsou tato data uspořádána. Data časových řad obvykle přicházejí v sekvenčním pořadí, takže se s nimi zachází jako s vložením, nikoli jako s aktualizací databáze.

Může být výzvou k ukládání, indexování, dotazování, analýze a vizualizaci dat časových řad ve velkých objemech.
Azure Time Series Insights zachycuje a ukládá každou novou událost jako řádek a změna se efektivně měří v průběhu času. V důsledku toho se můžete podívat zpět a získat přehledy z minulosti, které vám pomohou předvídat budoucí změny.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Přečtěte si další informace o Azure Time Series Insights, cloudové analytické platformě IoT.</br>

[![Video](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primární scénáře

- Ukládat data časových řad škálovatelným způsobem.

   Jádrem služby Time Series Insights je databáze určená pro data časových řad. Vzhledem k tomu, že je škálovatelná a plně spravovaná, time series insights zpracovává práci ukládání a správy událostí.

- Prozkoumejte data téměř v reálném čase.

   Time Series Insights poskytuje průzkumníka, který vizualizuje všechna data, která proudí do prostředí. Krátce po připojení ke zdroji událostí můžete zobrazit, prozkoumat a dotazovat data událostí v rámci přehledů time series. Data vám pomohou ověřit, zda zařízení vydává data podle očekávání, a sledovat prostředek IoT pro zdraví, produktivitu a celkovou efektivitu.

- Proveďte analýzu hlavní příčiny a detekujte anomálie.

   Time Series Insights má nástroje, jako jsou vzory a perspektivní pohledy, které provádějí a ukládají vícestupňovou analýzu hlavních příčin. Time Series Insights funguje také se službami pro upozorňování, jako je Azure Stream Analytics, takže můžete zobrazit výstrahy a zjištěné anomálie téměř v reálném čase v průzkumníku Time Series Insights.

- Získejte globální přehled o datech časových řad, která proudí z různorodých umístění pro porovnání s více aktivy nebo lokalitami.

   K prostředí Time Series Insights můžete připojit několik zdrojů událostí. Tímto způsobem můžete zobrazit data, která proudí z více, různorodých míst dohromady v téměř reálném čase. Uživatelé mohou tuto viditelnost využít ke sdílení dat s obchodními lídry. Mohou lépe spolupracovat s odborníky na domény, kteří mohou využít své odborné znalosti k řešení problémů, použití osvědčených postupů a sdílení poznatků.

- Vytvořte zákaznickou aplikaci nad časem Přehledy řady.

   Time Series Insights zpřístupňuje rest query API, které můžete použít k vytváření aplikací, které používají data časových řad.

## <a name="capabilities"></a>Možnosti

- **Rychlé zahájení :** Azure Time Series Insights nevyžaduje přípravu předem na data, takže se můžete rychle připojit k milionům událostí ve svém centru IoT hubu nebo centru událostí. Po připojení můžete vizualizovat data ze senzorů a pracovat s nimi a rychle ověřit řešení IoT. S daty můžete pracovat bez psaní kódu a nemusíte se učit nový jazyk. Time Series Insights poskytuje podrobný, volný text dotazu povrch pro pokročilé uživatele a point-and-click průzkumu.

- **Přehledy téměř v reálném čase:** Time Series Insights dokáže singestovat miliony událostí senzorů denně s jednominutovou latencí. Time Series Insights vám pomůže získat přehled o datech vašich senzorů. Použijte ji k detekci trendů a anomálií, provádění analýz hlavních příčin a zabránění nákladným prostojům. Křížová korelace mezi daty v reálném čase a historickými daty vám pomůže najít skryté trendy v datech.

- **Vytváření vlastních řešení:** Vložte data Azure Time Series Insights do svých stávajících aplikací. Můžete také vytvořit nová vlastní řešení s časových řad Insights REST API. Můžete vytvářet přizpůsobená zobrazení a sdílet je s ostatními, aby i oni mohli zkoumat přehledy.

- **Škálovatelnost**: Time Series Insights je navržen tak, aby podporoval IoT ve velkém měřítku. Může ingestovat 1 až 100 milionů událostí za den při výchozím rozsahu uchování 31 dnů. Živé datové proudy můžete vizualizovat a analyzovat v téměř reálném čase spolu s historickými daty.

## <a name="get-started"></a>Začínáme

Chcete-li začít, postupujte takto.

1. Zřídit prostředí Time Series Insights na webu Azure Portal.
1. Připojte se ke zdroji událostí, jako je centrum IoT nebo centrum událostí.
1. Nahrajte referenční data. Tohle není další služba.
1. Zkontrolujte a zobrazte data během několika minut pomocí průzkumníka Time Series Insights.

## <a name="explorer"></a>Průzkumník

Zobrazujte, analyzujte a objevujte trendy ve svých datech pomocí průzkumníka Azure Time Series Insights.

![Průzkumník Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Přečtěte si, jak používat [průzkumník Azure Time Series Insights](time-series-insights-explorer.md) a kreslit přehledy z vašich dat.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte demonstrační prostředí Azure Time Series Insights [s obecnou dostupností zdarma](./time-series-quickstart.md).

- Přečtěte si další informace o tom, jak naplánovat prostředí [Time Series Insights.](time-series-insights-environment-planning.md)
