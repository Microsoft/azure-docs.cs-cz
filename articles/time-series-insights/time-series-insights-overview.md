---
title: 'Přehled: Co je Azure Time Series Insights? | Dokumenty Microsoft'
description: Seznámení s Azure Time Series Insights, novou službou pro analýzu dat časových řad a řešení IoT
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6ef965b9c22524df5893d5826548a0b07f077062
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237635"
---
# <a name="what-is-azure-time-series-insights"></a>Co je Azure Time Series Insights?

Azure Time Series Insights je určený pro ukládání, vizualizaci a dotazování velkých objemů dat časových řad, jako je například generované zařízeními IoT. Pokud chcete ukládat, spravovat, dotazovat nebo vizualizovat data časových řad v cloudu, služba Time Series Insights je pro vás pravděpodobně to pravé. 

![Vývojový diagram služby Time Series Insights](media/overview/time-series-insights-flowchart.png)

Služba Time Series Insights má čtyři klíčové úlohy:

- Je plně integrována s cloudové brány, jako je Azure IoT Hub a Azure Event Hubs. Snadno se připojuje k těmto zdrojům událostí a parsuje JSON ze zpráv a struktur obsahujících data v jasně uspořádaných řádcích a sloupcích. Spojuje metadata s telemetrií a indexuje vaše data ve sloupcovém úložišti.
- Time Series Insights spravuje úložiště dat. Pokud chcete mít jistotu, že data jsou vždy snadno k dispozici, ukládá data v paměti a vlastnosti jednotek SSD pro až 400 dnů. Můžete zadávat interaktivní dotazy miliardy událostí v sekundách – na vyžádání.
- Time Series Insights poskytuje vizualizace out-of-the-box pomocí Průzkumníka služby Time Series Insights. 
- Time Series Insights poskytuje dotazovací služby, v Průzkumníku Time Series Insights a s použitím rozhraní API, která se snadno integrovat do vlastních aplikací vkládat data časových řad.

Pokud vytváříte aplikaci ve výši interních spotřebních nebo externím zákazníkům používat, můžete jako back-end Time Series Insights. Můžete ji na index, úložiště a data agregovaná časových řad. Chcete-li sestavit vlastní vizualizace a uživatelského prostředí v horní části, použijte [klientské sady SDK](tutorial-explore-js-client-lib.md). Time Series Insights je také vybavený několik [API pro dotazy](how-to-shape-query-json.md) povolit tyto přizpůsobené scénáře.

Data časových řad představují změnu prostředku nebo procesu v průběhu času. Data časových řad je indexované podle časová razítka a čas je smysluplných osa podél který taková data jsou uspořádaná. Data časových řad se obvykle dorazí v postupném pořadí, je obvykle považován za vložení spíše než aktualizaci vaší databáze.

To může být náročné na ukládání, indexování, dotazování, analýza a vizualizace dat časových řad v velké objemy.
Azure Time Series Insights shromažďuje a ukládá každé nové události jako řádek, a změnit efektivně měří se v čase. V důsledku toho můžete se podívat zpět k vykreslení informací z minulosti pomáhají předpovědět budoucí změny.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Další informace o Azure Time Series Insights, analytické platformy IoT založené na cloudu.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primární scénáře

- Store data časových řad v škálovatelným způsobem. 

   Jádrem služby Time Series Insights je databáze určená pro data časových řad. Protože je škálovatelná a plně spravovaná, zpracovává Time Series Insights práci ukládání a správu událostí.

- Zkoumání dat v reálném čase. 

   Time Series Insights poskytuje Průzkumníka, které vizualizuje všechna data tohoto datových proudů do prostředí. Krátce po připojení ke zdroji událostí, můžete zobrazit, prozkoumejte a dotazování dat události v rámci služby Time Series Insights. Tato data vám ověření, jestli zařízení posílá data podle očekávání a monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu. 

- Provést analýzu hlavní příčiny a detekci anomálií.

   Time Series Insights má nástroje, jako je perspektivy zobrazení chování a uložte vícekrokových původních příčin a vzory. Time Series Insights také pracuje s tak, aby výstrahy můžete zobrazit výstrahy služby, jako je Azure Stream Analytics a detekované anomálie v reálném čase v Průzkumníku Time Series Insights. 

- Získejte globální přehled o datech časových řad, která jsou streamována z různorodých umístění pro porovnání více prostředků nebo webu.

   K prostředí Time Series Insights můžete připojit několik zdrojů událostí. Tímto způsobem můžete zobrazit data, která jsou streamována z několika různých umístěních společně v téměř reálném čase v. Uživatelé mohou využít výhod této viditelnost sdílet data s obchodní vedení. Se můžete lépe spolupracovat s odborníky na domény, kteří můžete použít k řešení problémů, použít osvědčené postupy a sdílet poznatky o své znalosti.

- Vytvoření aplikace zákazníků nad Time Series Insights. 

   Time Series Insights poskytuje REST API pro dotazy, můžete použít k sestavení aplikací, které používají data časových řad.

## <a name="capabilities"></a>Možnosti

- **Rychlé zprovoznění**: Azure Time Series Insights nevyžaduje přípravu dat předem, abyste se mohli rychle připojit až po miliony událostí ve službě IoT hub nebo event hub. Po připojení, můžete vizualizovat a interakci s daty senzorů rychle tak prověřit řešení IoT. Můžete pracovat s daty bez psaní kódu a není nutné učit nový jazyk. Time Series Insights poskytuje plochu podrobné volnotextové dotazování pro pokročilé uživatele a bodem a kliknout.

- **Téměř v reálném čase**: Time Series Insights může ingestovat milionů událostí senzorů za den s latencí jedné minuty. Time Series Insights umožňuje získat přehled o datech senzorů. Umožňuje odhalit trendy a anomálie, provádět analýzy původních příčin a vyhnout se nákladným prostojům. Křížové korelace mezi daty v reálném čase i historickými daty vám pomůže najít skryté trendy v datech.

- **Vytvoření vlastních řešení**: Vložte data Azure Time Series Insights do svých stávajících aplikací. Nová vlastní řešení můžete vytvořit také pomocí rozhraní čas Series Insights REST API. Můžete vytvářet přizpůsobená zobrazení a sdílet je s ostatními, aby i oni mohli zkoumat přehledy.

- **Škálovatelnost**: Time Series Insights je navržena pro podporu IoT ve velkém měřítku. Může ingestovat 1 až 100 milionů událostí za den při výchozím rozsahu uchování 31 dnů. Můžete vizualizovat a analyzovat živé datové proudy téměř v reálném čase, společně s historickými daty.

## <a name="get-started"></a>Začínáme

Abyste mohli začít, postupujte podle těchto kroků.

1. Zřízení prostředí Time Series Insights na webu Azure Portal.
1. Připojení ke zdroji událostí služby IoT hub nebo centra událostí. 
1. Nahrajte referenční data. Tato akce není další služby.
1. Během několika minut můžete data zobrazit v průzkumníku Time Series Insights.

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

Tento diagram znázorňuje příklad časové řady přehledy dat zobrazit pomocí Průzkumníka služby Time Series Insights.

![Průzkumník Time Series Insights](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Další postup

- Prozkoumejte všeobecnou dostupnost Azure Time Series Insights [bezplatném ukázkovém prostředí](./time-series-quickstart.md).
- Další informace o tom, jak [plánu služby Time Series Insights](time-series-insights-environment-planning.md) prostředí.
