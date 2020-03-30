---
title: Prozkoumejte data pomocí průzkumníka – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí průzkumníka Azure Time Series Insights zobrazit data IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046171"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník Přehledů Azure Time Series

Tento článek popisuje funkce a možnosti obecné dostupnosti pro [webovou aplikaci](https://insights.timeseries.azure.com/)Průzkumník Azure Time Series Insights . Průzkumník Time Series Insights demonstruje výkonné možnosti vizualizace dat poskytované službou a lze k nim přistupovat ve vašem vlastním prostředí.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o vašich datech, což vám umožní rychle ověřit vaše řešení IoT a vyhnout se nákladným prostojům kritických zařízení. Skryté trendy, anomálie a analýzy hlavních příčin můžete objevit téměř v reálném čase. Průzkumník Time Series Insights je momentálně ve verzi Public Preview.

> [!TIP]
> Prohlídka s průvodcem v demonstračním prostředí si přečtěte [úvodní příručku Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Přečtěte si o dotazování na data pomocí průzkumníka Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Přehrajte předchozí video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Začínáme s Time Series Insights pomocí akcelerátoru řešení Azure IoT.".</a>

## <a name="prerequisites"></a>Požadavky

Než budete moci použít průzkumníka Time Series Insights, musíte:

- Vytvořte prostředí Time Series Insights. Další informace najdete v článek [Jak začít s time series insights](./time-series-insights-get-started.md).
- [Poskytněte přístup](time-series-insights-data-access.md) ke svému účtu v prostředí.
- Přidejte do něj centrum [IoT](time-series-insights-how-to-add-an-event-source-iothub.md) nebo zdroj událostí [centra událostí.](time-series-insights-how-to-add-an-event-source-eventhub.md)

## <a name="explore-and-query-data"></a>Prozkoumání a dotazování dat

Během několika minut po připojení zdroje událostí k prostředí Time Series Insights můžete prozkoumat data časových řad a zadat dotaz.

1. Chcete-li začít, otevřete [průzkumník a průzkumník a průzkumník Time Series Insights](https://insights.timeseries.azure.com/) ve webovém prohlížeči. Na levé straně okna vyberte prostředí. Všechna prostředí, ke kterým máte přístup, jsou uvedena v abecedním pořadí.

1. Po výběru prostředí použijte konfigurace **Od** a **Do** v horní části nebo vyberte a táhněte přes požadovaný časový rozsah. Vyberte lupu v pravém horním rohu nebo klikněte pravým tlačítkem myši na vybraný časový rozsah a vyberte **Hledat**.

1. Dostupnost můžete také aktualizovat automaticky každou minutu výběrem tlačítka **Automatické zapnutí.** Tlačítko **Automaticky zapnout** se vztahuje pouze na graf dostupnosti, nikoli na obsah hlavní vizualizace.

1. Ikona cloudu Azure vás přenese do vašeho prostředí na webu Azure Portal.

   [![Výběr prostředí Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Dále se zobrazí graf, který zobrazuje počet všech událostí během vybraného časovým rozpětí. Zde máte řadu ovládacích prvků:

    - **Panel Editor termínů**: Termín mezera je místo, kde dotaz na vaše prostředí. Nachází se na levé straně obrazovky:
      - **MÍRA**: V tomto rozevíracím seznamu jsou uvedeny všechny číselné sloupce (**Čtyřhra**).
      - **ROZDĚLIT:** V tomto rozevíracím seznamu jsou uvedeny kategorické sloupce (**Řetězce).**
      - Můžete povolit interpolaci kroku, zobrazit minimální a maximální a nastavit osu y z ovládacího panelu vedle **možnosti MEASURE**. Můžete také upravit, zda jsou zobrazená data počet, průměr nebo součet dat.
      - Můžete přidat až pět termínů pro zobrazení na stejné ose x. Vyberte **Přidat,** chcete-li přidat nový termín, nebo pomocí tlačítka **Klonovat tento termín** přidejte kopii existujícího termínu.

        [![Výběr, filtrování a panel dotazů](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikát**: Pomocí predikátu můžete rychle filtrovat události pomocí sady operandů uvedených v následující tabulce. Pokud provádíte vyhledávání výběrem nebo kliknutím, predikát se automaticky aktualizuje na základě tohoto vyhledávání. Mezi podporované typy operandů patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Dvojité**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **Řetězec**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**In**     | **Řetězec**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Všechny operandy by měly být stejného typu nebo **konstanta NULL.**        |
         |**Hsa**     | **Řetězec**        |  Na pravé straně jsou povoleny pouze literály konstantní řetězec. Prázdný řetězec a **NULL** nejsou povoleny.       |

      - **Ukázkové dotazy**

         [![Příklad dotazů GA](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Pomocí posuvníku **Velikost intervalu** můžete zvětšit a oddálit intervaly ve stejném časovém intervalu. Jezdec poskytuje přesnější kontrolu pohybu mezi velkými časovými úseky, které zobrazují hladké trendy až po řezy tak malé, jako je milisekunda, které umožňují zobrazit a analyzovat podrobné řezy dat s vysokým rozlišením. Výchozí počáteční bod posuvníku je nastaven jako nejoptimálnější zobrazení dat z výběru, aby bylo vyvažováno rozlišení, rychlost dotazu a rozlišovací schopnost.

1. Nástroj **stopa času** usnadňuje navigaci z jednoho časového rozpětí do druhého.

1. Výběrem ikony **Uložit** uložíte aktuální dotaz a sdílejte ho s ostatními uživateli prostředí. Když vyberete ikonu **Otevřít,** můžete zkontrolovat všechny uložené dotazy a všechny sdílené dotazy ostatních uživatelů v prostředích, ke které máte přístup.

   [![Dotazy](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Vizualizace dat

1. Nástroj **Perspektivní zobrazení** slouží k současnému zobrazení až čtyř jedinečných dotazů. Tlačítko **Perspektivní zobrazení** se nachází v pravém horním rohu grafu.

   [![Výběr dotazů, které chcete přidat do perspektivního podokna](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Zobrazení grafu pro vizuální prozkoumání dat a použití nástrojů **grafu:**

    - **Vyberte** nebo **klikněte na** určitý časový rozsah nebo jednu datovou řadu.
    - V rámci výběru časových rozpětí můžete události přiblížit nebo prozkoumat.
    - V rámci datové řady můžete řadu rozdělit o jiný sloupec, přidat řadu jako nový termín, zobrazit pouze vybranou řadu, vyloučit vybranou řadu, pingovat tuto řadu nebo prozkoumat události z vybrané řady.
    - V oblasti filtru nalevo od grafu můžete zkontrolovat všechny zobrazené datové řady a řadit podle hodnoty nebo názvu. Můžete také zobrazit všechny datové řady nebo všechny připnuté nebo nepřipnuté řady. Můžete vybrat jednu datovou řadu a rozdělit řadu o jiný sloupec, přidat řadu jako nový termín, zobrazit pouze vybranou řadu, vyloučit vybranou řadu, připnout tuto řadu nebo prozkoumat události z vybrané řady.
    - Při zobrazení více termínů současně můžete skládat, rozbalovat, kontrolovat další data o datové řadě a používat stejnou osu y ve všech termínech. Pomocí tlačítek v pravém horním rohu grafu.

    [![Nástroj graf, nastavení možností v pravém horním rohu](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Pomocí **heatmap** rychle rozpoznat jedinečné nebo anomální datové řady v daném dotazu. Pouze jeden hledaný výraz lze vizualizovat jako heatmap.

    [![Ga explorer heatmap grafy](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Když prozkoumáte události výběrem nebo kliknutím pravým tlačítkem myši, je k dispozici panel **UDÁLOSTI.** Zde můžete zkontrolovat všechny nezpracované události a exportovat události jako soubory JSON nebo CSV. Time Series Insights ukládá všechna nezpracovaná data.

    [![Události](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Po prozkoumání událostí vyberte kartu **STATS,** abyste odhalili vzorky a statistiky sloupců.

    - **Vzorky**: Tato funkce proaktivně zobrazuje statisticky nejvýznamnější vzorky ve vybrané oblasti dat. Nemusíte se dívat na tisíce událostí, abyste pochopili, jaké vzory vyžadují nejvíce času a energie. S Time Series Insights můžete skočit přímo do těchto statisticky významných vzorců a pokračovat v provádění analýzy. Tato funkce je také užitečná pro postmortální vyšetřování historických dat.
    - **Statistiky sloupců**: Statistiky sloupců poskytují grafy a tabulky, které rozdělují data z každého sloupce vybrané datové řady na vybrané časový rozsah.

      [![Grafy a možnosti sloupců STATS](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nyní jste se dozvěděli o klíčových funkcích, nastavení konfigurace a možnostech zobrazení, které jsou k dispozici ve webové aplikaci Explorer Time Series Insights.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [diagnostikovat a řešit problémy](time-series-insights-diagnose-and-solve-problems.md) v prostředí Time Series Insights.

- Vydejte se na úvodní [prohlídku rychlého startu Azure Time Series Insights.](time-series-quickstart.md)
