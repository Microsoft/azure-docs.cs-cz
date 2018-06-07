---
title: Co je Azure časové řady Insights? | Dokumenty Microsoft
description: Úvod do Azure časové řady Insights, novou službu k analýze dat řady čas a řešení IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/26/2018
ms.openlocfilehash: 647cce6ec907863474b70f2642932b11df666d1f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651858"
---
# <a name="what-is-azure-time-series-insights"></a>Co je Azure časové řady Insights?

Statistika řady čas je vytvořené pro ukládání, vizualizace a dotazování velké objemy dat řady čas, jako je například generovaná zařízeními IoT.  Pokud chcete uložit, spravovat, dotaz nebo vizualizovat data řady čas v cloudu, je pravděpodobné, práva pro přehledy časové řady.  

![Vývojový diagram časové řady statistiky](media/overview/time-series-insights-flowchart.png)

Statistika časové řady má čtyři klíčové úlohy:

- Nejprve je plně integrované s cloudové brány, jako je Azure IoT Hub a Azure Event Hubs. Snadno se připojuje k tyto zdroje událostí a analyzuje JSON ze zprávy a struktury, které mají data v čisté řádků a sloupců. Se připojí metadata s telemetrií a indexy vaše data v sloupcovém úložiště.
- Druhé časové řady Statistika spravuje úložiště dat. Kvůli zajištění bezpečnosti dat vždy snadno dostupný, ukládá vaše data v paměti a na SSD až 400 dní. Až miliardy událostí můžete interaktivně dotazu v sekundách – na vyžádání.
- Třetí časové řady statistika poskytuje vizualizace se na pole prostřednictvím TSI explorer.  
- Statistika časové řady čtvrté, poskytuje službu, dotaz, v Průzkumníku TSI i pomocí rozhraní API, která se dají snadno integrovat pro vložení dat v řadě čas do vlastních aplikací.  

Umožňuje vytvářet aplikace, pro využívání interní nebo externí zákazníky, pokud chcete použít, časové řady Statistika lze nastavit jako back-end pro indexování, ukládání a agregování dat časové řady. V horní části můžete vytvořit vlastní vizualizace a uživatelské prostředí.  Statistika časové řady zpřístupňuje rozhraní API dotazu chcete povolit tento scénář.  

Pokud si nejste jistí, pokud jsou vaše data časové řady, zde je co byste měli vědět.  Data řady čas představuje, jak prostředku nebo proces změny v čase.  Je jedinečný v, že má časovým razítkem a čas smysluplných jako osu.  Data řady čas obvykle dorazí v pořadí časů a obvykle považuje za typu vložení, nikoli aktualizaci vaší databáze.  Protože časové řady Statistika zaznamená a uloží každou novou událost jako řádek, změna se měří v čase, umožňuje hledat zpětné a předpovídat budoucí změny.  Ve velkých svazků může být náročné ukládání, indexování, dotazování, analýze a detekční data časové řady.  

## <a name="primary-scenarios"></a>Primární scénáře

- Ukládání časových řad dat škálovatelné způsobem.  
  - Jádro aplikace má Statistika časové řady databáze navržené s daty časové řady pamatujte.  Protože je škálovatelná a plně spravovaná, časové řady Statistika zpracovává práci při ukládání a správě události.

- Téměř zkoumání dat v reálném čase.  
  - Časové řady statistika poskytuje explorer, který vizualizuje všechna data streamování do prostředí.  Krátce po připojení zdroje událostí, data události lze zobrazit, prozkoumané a v rámci časové řady Statistika předmětem dotazu.  Data jsou užitečné pro ověření, jestli je zařízení generování dat dle očekávání a sledování prostředek IoT pro stav, produktivitu a účinnost.  

- Zjišťování, analýzu a anomálií příčin.
  - Čas řady Insights obsahuje nástroje, například vzory a zobrazení perspektivy a proveďte analýzu hlavní příčiny vícekrokový uložit.  Navíc časové řady Insights funguje ve spojení s výstrahy služby, jako je Azure Stream Analytics, tak výstrahy a zjištěném anomálií lze zobrazit v téměř v reálném čase v Průzkumníku Statistika časové řady.  

- Globální zobrazení data řady čas streamování z různorodých umístění pro porovnání asset více nebo webu.
  - Více zdroje událostí může připojit k prostředí Statistika časové řady.  To znamená, že data vysílání datového proudu z několika umístěních různorodých lze zobrazit společně v téměř v reálném čase.  Uživatelé mohou využít výhod tento přehled sdílení dat s žebříčky obchodní a umožňující lepší spolupráci s odborníky domény, kteří můžete použít jejich odbornými znalostmi k řešení problémů, použít osvědčené postupy a sdílet learnings.

- Vytvoření aplikace zákazníka nad Statistika časové řady. 
  - Statistika časové řady zpřístupňuje rozhraní API REST dotazu, umožňuje vytvářet aplikace, které používají data časové řady.

## <a name="capabilities"></a>Možnosti

- **Rychlé zahájení:** Statistika řady čas Azure vyžaduje bez přípravy počáteční data. Připojte k miliony událostí ve službě Azure IoT Hub nebo Centrum událostí v minutách. Po připojení, vizualizovat a interagovat s daty senzor rychle ověření vašich vlastních IoT řešení. Můžete pracovat s daty bez nutnosti psaní kódu.
Neexistuje žádný nový jazyk další; Časové řady statistika poskytuje podrobné, volné dotazu prostor pro pokročilé uživatele a bod a klikněte na zkoumání.
- **V blízkosti přehledy v reálném čase:** Insights časové řady může přijímat miliony událostí senzor za den, s latencí jednu minutu. Statistika časové řady pomáhá získat přehled o svých datech senzor díky čemuž trendy a anomálií, provedení analýzy příčin a vyhnout nákladným odstávkám. Díky povolení křížové korelace mezi historickými daty a daty v reálném čase pomáhá Time Series Insights odhalit skryté trendy v datech.
- **Vytvoření vlastních řešení:** vložení Statistika řady Azure čas data do existující aplikace, nebo vytvořte nová vlastní řešení s časové řady Statistika REST API. Vytvoření přizpůsobené zobrazení, že můžete sdílet pro ostatní prozkoumat přehledy.
- **Škálovatelnost:** časové řady Insights je navržen pro podporu IoT ve velkém měřítku. Můžete ho příjem příchozích dat z 1 milionu na 100 miliónů událostí za den, s dobou uchování výchozí span 31 dní. Můžete vizualizovat a analyzovat data za provozu datových proudů téměř v reálném čase, spolu s historická data. Klouzavý dopředu, příjem příchozích dat a uchování sazby zvýší zohlednit celopodnikového rozsahu.

## <a name="getting-started"></a>Začínáme
Začínáme trvá méně než 5 minut. 

1.  Chcete-li získat spustit, zřízení prostředí časové řady Insights na portálu Azure. 
2.  Připojení zdroje událostí jako Azure IoT Hub nebo Centrum událostí.  
3.  Nahrajte referenční data (Toto není další služby).
4.  Zobrazit vaše data v minutách pomocí Průzkumníka Statistika časové řady.

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights
Tento diagram znázorňuje příklad časové řady datům přehledů zobrazit pomocí Průzkumník: ![explorer Statistika časové řady](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Další postup
 - [Prozkoumat pomocí Průzkumníka časové řady statistiky v ukázce prostředí](./time-series-quickstart.md)
 - [Plánování prostředí Statistika časové řady](time-series-insights-environment-planning.md)

