---
title: 'Přehled: co je Azure Time Series Insights? -Azure Time Series Insights | Microsoft Docs'
description: Seznámení s Azure Time Series Insights, novou službou pro analýzu dat časových řad a řešení IoT
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 12/11/2019
ms.custom: seodec18
ms.openlocfilehash: 9b9491a4438ff07222bb5ce5bd684964bdfc0031
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452836"
---
# <a name="what-is-azure-time-series-insights"></a>Co je Azure Time Series Insights?

Azure Time Series Insights je postavená na ukládání, vizualizaci a dotazování velkých objemů dat časových řad, jako jsou generovaná zařízeními IoT. Pokud chcete ukládat, spravovat, dotazovat nebo vizualizovat data časových řad v cloudu, služba Time Series Insights je pro vás pravděpodobně to pravé.

[Vývojový diagram ![Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Služba Time Series Insights má čtyři klíčové úlohy:

- Je plně integrovaná se službou Cloud Gateway, jako je Azure IoT Hub a Azure Event Hubs. Snadno se připojuje k těmto zdrojům událostí a parsuje JSON ze zpráv a struktur obsahujících data v jasně uspořádaných řádcích a sloupcích. Spojuje metadata s telemetrií a indexuje vaše data ve sloupcovém úložišti.
- Time Series Insights spravuje úložiště vašich dat. Aby se zajistilo, že data jsou vždycky snadno dostupná, uloží vaše data do paměti a SSD po dobu až 400 dnů. Můžete interaktivně dotazovat miliardy událostí v řádu sekund – na vyžádání.
- Time Series Insights poskytuje okamžitou vizualizaci pomocí Time Series Insights Exploreru.
- Time Series Insights poskytuje dotazovací službu v Průzkumníkovi Time Series Insights a pomocí rozhraní API, která se dají snadno integrovat pro vložení dat časových řad do vlastních aplikací.

Pokud sestavíte aplikaci pro interní spotřebu nebo externím zákazníkům, abyste je mohli použít, můžete jako back-end použít Time Series Insights. Můžete ji použít k indexování, ukládání a agregaci dat časových řad. Chcete-li vytvořit vlastní vizualizaci a činnost koncového uživatele, použijte [klientskou sadu SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights je také vybaven několika [rozhraními API dotazů](how-to-shape-query-json.md) , aby bylo možné tyto vlastní scénáře povolit.

Data časových řad představují změnu prostředku nebo procesu v průběhu času. Data časové řady jsou indexována pomocí časových razítek a časová osa, podél které jsou taková data uspořádána. Data časové řady obvykle přicházejí v sekvenčním pořadí, takže se považuje za vložené místo aktualizace databáze.

Může se jednat o výzvu k ukládání, indexování, dotazování, analýze a vizualizaci dat časových řad ve velkých svazcích.
Azure Time Series Insights zachycuje a ukládá každou novou událost jako řádek a změna se efektivně měří v průběhu času. Výsledkem je, že se můžete podívat zpátky a nakreslit přehledy z minulosti, abyste mohli předpovědět budoucí změnu.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Přečtěte si další informace o Azure Time Series Insights, cloudové platformě IoT Analytics.</br>

[VIDEO ![](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primární scénáře

- Umožňuje ukládat data časových řad škálovatelným způsobem.

   Jádrem služby Time Series Insights je databáze určená pro data časových řad. Vzhledem k tomu, že je škálovatelná a plně spravovaná, Time Series Insights zpracovává práci při ukládání a správě událostí.

- Prozkoumejte data prakticky v reálném čase.

   Time Series Insights poskytuje Průzkumníka, který vizualizuje všechna data, která se streamují do prostředí. Krátce po připojení ke zdroji událostí můžete zobrazit, prozkoumat a dotazovat data událostí v rámci Time Series Insights. Data vám pomůžou ověřit, jestli zařízení emituje data podle očekávání, a monitoruje prostředky IoT pro stav, produktivitu a celkovou efektivitu.

- Proveďte analýzu hlavní příčiny a detekci anomálií.

   Time Series Insights mají nástroje, jako jsou vzory a zobrazení Perspektiva, k provádění a ukládání vysoce kroků při analýze hlavní příčiny. Time Series Insights také funguje se službami upozorňování jako Azure Stream Analytics, takže můžete zobrazit výstrahy a zjištěné anomálie téměř v reálném čase v Průzkumníkovi Time Series Insights.

- Získejte globální přehled o datech časových řad, které proudí z různorodých míst pro více prostředků nebo porovnání lokalit.

   K prostředí Time Series Insights můžete připojit několik zdrojů událostí. Tímto způsobem můžete zobrazit data, která se streamují v různých umístěních, a to téměř v reálném čase. Uživatelé můžou tuto viditelnost využít k tomu, aby mohli sdílet data s obchodními vedoucími. Můžou lépe spolupracovat s odborníky v doméně, kteří můžou využít své odbornosti při řešení problémů, použití osvědčených postupů a sdílení výukových kurzů.

- Sestavte zákaznickou aplikaci nad Time Series Insights.

   Time Series Insights zpřístupňuje rozhraní REST API, které můžete použít k vytváření aplikací, které používají data časových řad.

## <a name="capabilities"></a>Možnosti

- **Rychlé zahájení**: Azure Time Series Insights nevyžaduje přípravu dat předem, takže se můžete rychle připojit k miliónům událostí ve službě IoT Hub nebo v centru událostí. Po připojení můžete vizualizovat a interagovat s daty ze senzorů, abyste mohli rychle ověřit vaše řešení IoT. S daty můžete pracovat, aniž byste museli psát kód a nemusíte se učit nový jazyk. Time Series Insights poskytuje detailní a volné plochy pro dotazování pro pokročilé uživatele, a to tak, že vyhledáme a kliknete na průzkum.

- **Přehledy téměř v reálném**čase: Time Series Insights můžou ingestovat miliony událostí senzorů za den a latence v minutách. Time Series Insights vám pomůže získat přehled o datech ze senzorů. Využijte ji k vyřízení trendů a anomálií, provádění analýz hlavní příčiny a zamezení nákladnému výpadku. Křížové korelace mezi daty v reálném čase a historická data vám pomůžou najít skryté trendy v datech.

- **Sestavování vlastních řešení**: vkládání Azure Time Series Insights dat do stávajících aplikací. Můžete také vytvořit nová vlastní řešení s rozhraními REST API pro Time Series Insights. Můžete vytvářet přizpůsobená zobrazení a sdílet je s ostatními, aby i oni mohli zkoumat přehledy.

- **Škálovatelnost:** Služba Time Series Insights je navržena tak, aby podporovala škálování IoT. Může ingestovat 1 až 100 milionů událostí za den při výchozím rozsahu uchování 31 dnů. Společně s historickými daty můžete vizualizovat a analyzovat živé datové proudy téměř v reálném čase.

## <a name="get-started"></a>Začít

Chcete-li začít, postupujte podle těchto kroků.

1. Zřízení Time Series Insightsho prostředí v Azure Portal.
1. Připojte se ke zdroji událostí, jako je centrum IoT nebo centrum událostí.
1. Nahrajte referenční data. Nejedná se o další službu.
1. Během několika minut můžete data zobrazit v průzkumníku Time Series Insights.

## <a name="explorer"></a>Průzkumník

Umožňuje zobrazit, analyzovat a zjišťovat trendy ve vašich datech pomocí Azure Time Series Insights Exploreru.

![Průzkumník Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Naučte se používat [Azure Time Series Insights Explorer](time-series-insights-explorer.md) a nakreslovat z vašich dat přehledy.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte Azure Time Series Insights obecné dostupnosti [bezplatného ukázkového prostředí](./time-series-quickstart.md).

- Přečtěte si další informace o [plánování Time Series Insightsho](time-series-insights-environment-planning.md) prostředí.
