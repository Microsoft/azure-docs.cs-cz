---
title: Zkoumání dat pomocí Průzkumníka služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí Průzkumníka služby Azure Time Series Insights ve webovém prohlížeči se krátce zobrazit globální přehled o velké objemy dat a ověření IoT prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: a7905213d67fd1cb97a8e50c938427b7ad775924
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165779"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník služby Azure Time Series Insights

Tento článek popisuje funkce a možnosti Obecné dostupnosti Azure Time Series Insights [explorer webové aplikace](https://insights.timeseries.azure.com/). V Průzkumníku Time Series Insights předvádí možnosti služeb výkonné datové vizualizace poskytovaných službou a je přístupná v rámci svého vlastního prostředí.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o vašich dat, která umožňuje rychle ověřit vaše řešení IoT a vyhnout se nákladným prostojům důležitých zařízení. Můžete odhalovat skryté trendy, detekovat anomálie a provádět analýzy hlavních příčin téměř v reálném čase. V Průzkumníku Time Series Insights je momentálně ve verzi public preview.

> [!TIP]
> Prohlídku s průvodcem prostřednictvím ukázkovém prostředí najdete v článku [rychlý start Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Další informace o dotazování dat pomocí Průzkumníka služby Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Podívejte se na předchozí video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Začínáme s Time Series Insights pomocí Azure akcelerátorů řešení IoT."</a>

## <a name="prerequisites"></a>Požadavky

Než použijete Průzkumníka služby Time Series Insights, musíte mít:

- Vytvoření prostředí Time Series Insights. Další informace najdete v tématu [jak začít pracovat s Time Series Insights](./time-series-insights-get-started.md).
- [Poskytnutí přístupu](time-series-insights-data-access.md) ke svému účtu v prostředí.
- Přidat [služby IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) nebo [centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) zdroje událostí do něj.

## <a name="explore-and-query-data"></a>Prozkoumejte a dotazování dat

Během několika minut připojit váš zdroj událostí do prostředí Time Series Insights vám umožní zkoumat a dotazovat data časových řad.

1. Pokud chcete spustit, otevřete [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com/) ve webovém prohlížeči. Na levé straně okna vyberte některé prostředí. Všechna prostředí, ke kterým máte přístup k jsou uvedeny v abecedním pořadí.

1. Po výběru prostředí použít **z** a **k** konfigurace v horní části stránky, nebo klikněte na tlačítko a přetáhněte časový interval, který chcete. V pravém horním rohu vyberte ikonu lupy nebo klikněte pravým tlačítkem na vybraný časový rozsah a vyberte **hledání**.

1. Můžete také aktualizovat dostupnosti automaticky každou minutu tak, že vyberete **automaticky na** tlačítko. **Automaticky na** tlačítka se vztahuje pouze na graf dostupnosti není obsahu hlavní vizualizace.

1. Ikona cloudu Azure přejdete na vaše prostředí na webu Azure Portal.

   [![Prostředí Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. V dalším kroku se zobrazí graf, který zobrazuje počet všech událostí během vybraný časový rozsah. Zde máte několik ovládacích prvků:

    - **Panel podmínek Editor**: Místo termín je, kde dotaz vašem prostředí. Se nachází na levé straně obrazovky:
      - **Míra**: Tento rozevírací seznam obsahuje všechny číselné sloupce (**zdvojnásobí**).
      - **Rozděleno podle**: Tento rozevírací seznam zobrazuje zařazené do kategorií sloupce (**řetězce**).
      - Můžete povolit schodovou interpolaci, zobrazí minimální a maximální a upravit vedle osy y z ovládacích panelů **míru**. Také můžete nastavit, jestli se data zobrazená počet, průměr nebo součet data.
      - Můžete přidat až pět podmínky zobrazíte na stejnou osu x. Použití **Rozbalovat podrobnosti kopírování** tlačítko Přidat další období nebo vyberte **přidat** přidat novou podmínku.

        [![Panel podmínek editoru](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikátu**: Použití predikátu rychle vyfiltrovat události s využitím sadu operandů s uvedené v následující tabulce. Pokud spustíte hledání výběrem nebo kliknutím na, predikát automaticky aktualizace založené na hledání. Operand podporované typy patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, časový interval       |         |
         |`=`, `!=`, `<>`     | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL        |         |
         |IN     | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL        |  Všechny operandy musí být stejného typu nebo konstanta NULL.        |
         |HAS     | String        |  Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a NULL nejsou povoleny.       |

      - **Příklady dotazů**

         [![Příklady dotazů](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Můžete použít **velikost intervalu** nástroj posuvník pro zvětšení do intervalů za stejný časový interval. Jezdec zajišťuje přesnější kontrolu pohyb mezi velké časové úseky, které ukazují smooth trendy dolů řezy malá jako milisekund, které umožňují zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod nastaven jako optimální zobrazení dat z výběru pro rozlišení, rychlost dotazů a členitosti.

1. **Čas štětce** nástroj umožňuje snadno přejít z jednoho timespan do druhého.

1. Použití **Uložit** příkazu Uložit aktuální dotaz a sdílet s ostatními uživateli prostředí. Při použití **otevřít**, můžete zobrazit všechny uložené dotazy a jiných uživatelů v prostředí máte přístup k žádné sdílené dotazy.

   [![Dotazy](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Vizualizace dat

1. Použití **Perspektiva** nástroj pro současně zobrazit až čtyři jedinečný dotazů. **Perspektiva** je tlačítko v pravém horním rohu grafu.

   [![Perspektiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Zobrazení grafu můžete vizuálně zkoumat data a použít **grafu** nástroje:

    - **Vyberte** nebo **klikněte na tlačítko** konkrétní časový rozsah nebo jedno datové řady.
    - V rámci určitého výběru časový interval můžete přiblížit nebo zkoumat události.
    - V rámci datové řady můžete řadu rozdělit podle jiného sloupce, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, odešlete zprávu ping onu nebo prozkoumat události z vybraného řady.
    - V oblasti filtrů nalevo od grafu můžete zobrazit všechny řady zobrazených dat a změnit pořadí podle hodnoty nebo název. Také můžete zobrazit všechny datové řady nebo libovolné připnuté nebo nepřipnuté řady. Můžete vybrat jednu řadu dat a řadu rozdělit podle jiného sloupce, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, připnout onu nebo prozkoumat události z vybraného řady.
    - Při zobrazení několika podmínek současně zásobníku, zrušit seskupení, zobrazit další data o datové řady a použít stejné osy y napříč všemi podmínkami. Pomocí tlačítek v pravém horním rohu grafu.

    [![Nástroje grafu](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Použití **heatmapu** rychle rozpoznat jedinečný nebo neobvyklé datové řady v daný dotaz. Pouze jeden hledaný termín lze vizualizovat jako heatmapu.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Když zkoumat události vybráním nebo kliknete pravým tlačítkem, **události** panely je k dispozici. Tady můžete zobrazit všechny nezpracovaných událostí a export událostí jako soubory JSON nebo CSV. Time Series Insights ukládá všechny nezpracovaná data.

    [![Události](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Vyberte **statistiky** kartě po zkoumat události k vystavení vzory a statistiky sloupce.

    - **Vzory**: Tato funkce proaktivně zobrazí nejvíce statisticky významná vzory ve vybrané datové oblasti. Podívejte se na tisíce událostí, abyste pochopili, jaké vzorce vyžadují nejvíce času i energie nemusíte. S Time Series Insights můžete přejít přímo do těchto statisticky významná vzorků pokračovat v provádění analýzy. Tato funkce je také užitečné pro následné vyšetřování historická data.
    - **Statistiky sloupce**: Statistiky sloupce poskytují grafů a tabulek, které rozdělení dat každého sloupce vybrané datové řady nad vybraný časový rozsah.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Nyní jste viděli různé funkce a možnosti, které jsou k dispozici v rámci webové aplikace Průzkumníka Time Series Insights.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [diagnostikovat a řešit problémy](time-series-insights-diagnose-and-solve-problems.md) ve vašem prostředí Time Series Insights.
- Využijte s průvodcem [rychlý start Azure Time Series Insights](time-series-quickstart.md) tour.
