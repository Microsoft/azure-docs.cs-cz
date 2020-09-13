---
title: Prozkoumejte data pomocí Průzkumníka – Azure Time Series Insights | Microsoft Docs
description: Naučte se používat Azure Time Series Insights Explorer k zobrazení dat IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1bcf8ec2395dbc94154072c1c4d839bf45125a3e
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487296"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Průzkumník Gen1

Tento článek popisuje funkce a možnosti pro [webovou aplikaci](https://insights.timeseries.azure.com/)Azure Time Series Insights Gen1 Explorer. Průzkumník Azure Time Series Insights ukazuje výkonné možnosti vizualizace dat poskytované službou a lze k němu přistupovat v rámci vašeho vlastního prostředí.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o datech, který vám umožní rychle ověřit vaše řešení IoT a vyhnout se nákladným výpadkům důležitých zařízení. Můžete zjišťovat skryté trendy, odhalit anomálie a provádět analýzy hlavních příčin téměř v reálném čase.

> [!TIP]
> Prohlídku provedenou pomocí demonstračního prostředí si můžete přečíst v tématu [rychlý start Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Přečtěte si informace o dotazování na data pomocí Azure Time Series Insights Exploreru. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Přehrajte si předchozí video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Začínáme s Azure Time Series Insights pomocí akcelerátoru řešení Azure IoT".</a>

## <a name="prerequisites"></a>Požadavky

Než budete moct použít Azure Time Series Insights Explorer, musíte:

- Vytvořte Azure Time Series Insights prostředí. Další informace najdete v článku [jak začít s Azure Time Series Insights](./time-series-insights-get-started.md).
- [Poskytněte](time-series-insights-data-access.md) v prostředí přístup k vašemu účtu.
- Přidejte do něj zdroj události služby [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) nebo [centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) .

## <a name="explore-and-query-data"></a>Prozkoumat data a dotazovat se na ně

Během několika minut od připojení zdroje událostí k vašemu Azure Time Series Insights prostředí můžete zkoumat a dotazovat se na data časových řad.

1. Začněte tím, že ve webovém prohlížeči otevřete [průzkumníka Azure Time Series Insights](https://insights.timeseries.azure.com/) . Na levé straně okna vyberte prostředí. Všechna prostředí, ke kterým máte přístup, jsou uvedena v abecedním pořadí.

1. Po výběru prostředí buď použijte konfigurace **od** a **do** v horní části, nebo vyberte a přetáhněte přes časový rozsah, který chcete. Vyberte lupu v pravém horním rohu nebo klikněte pravým tlačítkem na vybraný časový interval a vyberte **Hledat**.

1. Dostupnost můžete také automaticky aktualizovat, a to tak, že vyberete tlačítko **automaticky zapnuto** . Tlačítko **automaticky zapnuto** se vztahuje pouze na graf dostupnosti, nikoli na obsah hlavní vizualizace.

1. Ikona cloudu Azure vás přesměruje do vašeho prostředí v Azure Portal.

   [![Výběr prostředí Azure Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. V dalším kroku se zobrazí graf zobrazující počet všech událostí během vybraného časového rozmezí. Tady máte několik ovládacích prvků:

    - **Panel editoru podmínek**: termín místo, kde se dotazuje na vaše prostředí. Nachází se na levé straně obrazovky:
      - **Measure**: Tento rozevírací seznam zobrazuje všechny číselné sloupce (**Double**).
      - **Rozdělit podle**: Tento rozevírací seznam zobrazuje kategorií sloupce (**řetězce**).
      - Můžete povolit interpolaci kroku, zobrazit minimum a maximum a upravit osu y z ovládacích panelů vedle **míry**. Můžete také upravit, zda jsou zobrazená data počet, průměr nebo součet dat.
      - Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Vyberte **Přidat** , chcete-li přidat nový výraz nebo použijte tlačítko **klonovat tento termín** a přidejte kopii existujícího období.

        [![Výběr podmínek, filtrování a panel dotazu](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikát**: pomocí predikátu můžete rychle filtrovat události pomocí sady operandů uvedených v následující tabulce. Pokud hledání provádíte tak, že vyberete nebo kliknete, predikát se na základě tohoto hledání automaticky aktualizuje. Mezi podporované typy operandů patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **String**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |         |
         |**PRO**     | **String**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |  Všechny operandy by měly být stejného typu nebo mít **nulovou** konstantu.        |
         |**MÁ**     | **Řetězec**        |  Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a **hodnota null** nejsou povoleny.       |

      - **Ukázky dotazů**

         [![Příklady dotazů Gen1](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Pomocí nástroje posuvník **velikosti intervalu** můžete přiblížit nebo oddálit intervaly ve stejném časovém intervalu. Posuvník poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času, které znázorňují hladké trendy až po milisekundy, což umožňuje zobrazit a analyzovat podrobné a vysoce rozlišení vašich dat. Výchozí počáteční bod posuvníku je nastaven jako optimální zobrazení dat z výběru pro vyvážení rozlišení, rychlosti dotazů a členitosti.

1. Nástroj **Time štětec** usnadňuje přechod z jednoho časového rozpětí do druhého.

1. Výběrem ikony **Uložit** uložte aktuální dotaz a sdílejte ho s dalšími uživateli prostředí. Když vyberete ikonu **otevřít** , můžete zkontrolovat všechny uložené dotazy a všechny sdílené dotazy dalších uživatelů v prostředích, ke kterým máte přístup.

   [![Dotazy](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Vizualizace dat

1. Použijte nástroj **perspektivní zobrazení** pro simultánní zobrazení až čtyř jedinečných dotazů. Tlačítko **zobrazení Perspektiva** se nachází v pravém horním rohu grafu.

   [![Vybrat dotazy, které se mají přidat do podokna perspektivy](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Zobrazením grafu můžete vizuálně prozkoumat data a použít nástroje **grafu** :

    - **Vyberte** nebo **klikněte** na konkrétní časový rozsah nebo jednu datovou řadu.
    - V rámci výběru TimeSpan můžete události přiblížit nebo prozkoumat.
    - V rámci datové řady můžete řadu rozdělit na jiný sloupec, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyřadit z nich vybrané řady, vyloučíte je z této řady a prozkoumáte události z vybrané řady.
    - V oblasti filtru nalevo od grafu můžete zkontrolovat všechny zobrazené datové řady a změnit jejich pořadí podle hodnoty nebo názvu. Můžete také zobrazit všechny datové řady nebo připnuté nebo nepřipnutých řad. Můžete vybrat jednu datovou řadu a rozdělit ji na jiný sloupec, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, připnout tyto řady nebo prozkoumat události z vybrané řady.
    - Když si zobrazíte víc podmínek najednou, můžete Stack, odskládat, kontrolovat další data o datové řadě a používat stejnou osu y napříč všemi podmínkami. Použijte tlačítka v pravém horním rohu grafu.

    [![Nastavení možností v pravém horním rohu nástroje Chart Tool](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Pomocí **heatmapu** můžete v daném dotazu rychle vymezit jedinečné nebo neobvyklé datové řady. Pouze jeden hledaný termín lze vizuálně rozčlenit jako heatmapu.

    [![Vytváření grafů v Průzkumníkovi Time Series Insights heatmapu ](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Když prozkoumáte události tak, že vyberete nebo kliknete pravým tlačítkem myši, zpřístupní se panel **události** . Tady můžete zkontrolovat všechny nezpracované události a vyexportovat události jako soubory JSON nebo CSV. Azure Time Series Insights ukládá všechna nezpracovaná data.

    [![Události](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Po prozkoumání událostí a vystavení vzorců a statistiky sloupců vyberte kartu **statistiky** .

    - **Vzory**: Tato funkce proaktivně rozsvítí nejdůležitější vzory ve vybrané datové oblasti. Nemusíte se pohlížet na tisíce událostí, abyste zjistili, jaké vzory vyžadují nejvíce času a energii. Pomocí Azure Time Series Insights můžete přejít přímo na tyto statisticky významné vzory, abyste mohli pokračovat v analýze. Tato funkce je užitečná také pro následné šetření do historických dat.
    - **Statistiky sloupce**: statistiky sloupců poskytují grafy a tabulky, které rozdělují data z každého sloupce vybrané řady dat přes vybrané časové rozpětí.

      [![Grafy a možnosti statistiky sloupců](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nyní jste se seznámili s klíčovými funkcemi, konfiguračními nastaveními a možnostmi zobrazení, které jsou k dispozici v aplikaci Azure Time Series Insights Explorer Web App.

## <a name="next-steps"></a>Další kroky

- Naučte se [diagnostikovat a řešit problémy](time-series-insights-diagnose-and-solve-problems.md) v prostředí Azure Time Series Insights.

- Převezměte [Azure Time Series Insights](time-series-quickstart.md) úvodní prohlídka s asistencí.
