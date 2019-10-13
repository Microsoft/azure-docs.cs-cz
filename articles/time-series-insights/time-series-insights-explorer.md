---
title: Prozkoumat data pomocí Průzkumníka Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak pomocí Průzkumníka Azure Time Series Insights ve webovém prohlížeči rychle zobrazit globální zobrazení velkých objemů dat a ověřit prostředí IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: aa04b2508f89b9658d904624e884bf9133caf3b7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299541"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník Azure Time Series Insights

Tento článek popisuje funkce a možnosti všeobecně dostupné pro [webovou aplikaci Azure Time Series Insights Explorer](https://insights.timeseries.azure.com/). Průzkumník Time Series Insights ukazuje výkonné možnosti vizualizace dat poskytované službou a lze k němu přistupovat v rámci vašeho vlastního prostředí.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o datech, který vám umožní rychle ověřit vaše řešení IoT a vyhnout se nákladným výpadkům důležitých zařízení. Můžete zjišťovat skryté trendy, odhalit anomálie a provádět analýzy hlavních příčin téměř v reálném čase. Průzkumník Time Series Insights je aktuálně ve verzi Public Preview.

> [!TIP]
> Prohlídku provedenou pomocí demonstračního prostředí si můžete přečíst v tématu [rychlý start Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Přečtěte si informace o dotazování na data pomocí Time Series Insights Exploreru. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Projděte si předchozí video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Začínáme s Time Series Insights pomocí akcelerátoru řešení Azure IoT".</a>

## <a name="prerequisites"></a>Předpoklady

Než budete moct použít Time Series Insights Explorer, musíte:

- Vytvořte Time Series Insights prostředí. Další informace najdete v tématu [jak začít s Time Series Insights](./time-series-insights-get-started.md).
- [Poskytněte](time-series-insights-data-access.md) v prostředí přístup k vašemu účtu.
- Přidejte do něj zdroj události služby [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) nebo [centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) .

## <a name="explore-and-query-data"></a>Prozkoumat data a dotazovat se na ně

Během několika minut od připojení zdroje událostí k vašemu Time Series Insights prostředí můžete zkoumat a dotazovat se na data časových řad.

1. Začněte tím, že ve webovém prohlížeči otevřete [průzkumníka Time Series Insights](https://insights.timeseries.azure.com/) . Na levé straně okna vyberte prostředí. Všechna prostředí, ke kterým máte přístup, jsou uvedena v abecedním pořadí.

1. Po výběru prostředí buď použijte konfigurace **od** a **do** v horní části, nebo klikněte a přetáhněte ukazatel na časový rozsah, který chcete. Vyberte lupu v pravém horním rohu nebo klikněte pravým tlačítkem na vybraný časový interval a vyberte **Hledat**.

1. Dostupnost můžete také automaticky aktualizovat, a to tak, že vyberete tlačítko **automaticky zapnuto** . Tlačítko **automaticky zapnuto** se vztahuje pouze na graf dostupnosti, nikoli na obsah hlavní vizualizace.

1. Ikona cloudu Azure vás přesměruje do vašeho prostředí v Azure Portal.

   [prostředí @no__t – 1Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. V dalším kroku se zobrazí graf, který zobrazuje počet všech událostí během vybraného časového rozmezí. Tady máte několik ovládacích prvků:

    - **Panel editoru podmínek**: termín místo, kde se dotazuje na vaše prostředí. Nachází se na levé straně obrazovky:
      - **Measure**: Tento rozevírací seznam zobrazuje všechny číselné sloupce (**Double**).
      - **Rozdělit podle**: Tento rozevírací seznam zobrazuje kategorií sloupce (**řetězce**).
      - Můžete povolit interpolaci kroku, zobrazit minimum a maximum a upravit osu y z ovládacích panelů vedle **míry**. Můžete také upravit, zda jsou zobrazená data počet, průměr nebo součet dat.
      - Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Pomocí tlačítka pro **kopírování** přidejte další termín nebo vyberte **Přidat** a přidejte nový termín.

        [@no__t – panel editoru 1Terms](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikát**: pomocí predikátu můžete rychle filtrovat události pomocí sady operandů uvedených v následující tabulce. Pokud hledání provádíte tak, že vyberete nebo kliknete, predikát se na základě tohoto hledání automaticky aktualizuje. Mezi podporované typy operandů patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |`<`, `>` `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, bool, Double, DateTime, TimeSpan, NULL        |  Všechny operandy by měly být stejného typu nebo mít nulovou konstantu.        |
         |MÁ     | Řetězec        |  Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a hodnota NULL nejsou povoleny.       |

      - **Příklady dotazů**

         [@no__t – dotazy 1Example](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Pomocí nástroje posuvník **velikosti intervalu** můžete přiblížit nebo oddálit intervaly ve stejném časovém intervalu. Posuvník poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času, které znázorňují hladké trendy v průřezech až do milisekund, což vám umožní zobrazit podrobné a vysoce rozlišení vašich dat. Výchozí počáteční bod posuvníku je nastaven jako optimální zobrazení dat z výběru pro vyvážení rozlišení, rychlosti dotazů a členitosti.

1. Nástroj **Time štětec** usnadňuje přechod z jednoho časového rozpětí do druhého.

1. Pomocí příkazu **Uložit** uložte aktuální dotaz a sdílejte ho s dalšími uživateli prostředí. Když použijete možnost **otevřít**, můžete zobrazit všechny uložené dotazy a všechny sdílené dotazy dalších uživatelů v prostředích, ke kterým máte přístup.

   [@no__t – 1Queries](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Vizualizace dat

1. Použijte nástroj **perspektivní zobrazení** pro simultánní zobrazení až čtyř jedinečných dotazů. Tlačítko **zobrazení Perspektiva** se nachází v pravém horním rohu grafu.

   [@no__t – zobrazení 1Perspective](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Zobrazením grafu můžete vizuálně prozkoumat data a použít nástroje **grafu** :

    - **Vyberte** nebo **klikněte** na konkrétní časový rozsah nebo jednu datovou řadu.
    - V rámci výběru TimeSpan můžete události přiblížit nebo prozkoumat.
    - V rámci datové řady můžete řadu rozdělit na jiný sloupec, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyřadit z nich vybrané řady, vyloučíte je z této řady a prozkoumáte události z vybrané řady.
    - V oblasti filtru nalevo od grafu můžete zobrazit všechny zobrazené datové řady a změnit jejich pořadí podle hodnoty nebo názvu. Můžete také zobrazit všechny datové řady nebo připnuté nebo nepřipnutých řad. Můžete vybrat jednu datovou řadu a rozdělit ji na jiný sloupec, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, připnout tyto řady nebo prozkoumat události z vybrané řady.
    - Pokud současně zobrazujete více podmínek, můžete vytvořit zásobník, odskládat, zobrazit další data o datové řadě a použít stejnou osu y napříč všemi podmínkami. Použijte tlačítka v pravém horním rohu grafu.

    [Nástroj @no__t – 1Chart](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Pomocí **heatmapu** můžete v daném dotazu rychle vymezit jedinečné nebo neobvyklé datové řady. Pouze jeden hledaný termín lze vizuálně rozčlenit jako heatmapu.

    [@no__t – 1Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Když prozkoumáte události tak, že vyberete nebo kliknete pravým tlačítkem myši, zpřístupní se panel **události** . Tady uvidíte všechny nezpracované události a exportujte události jako soubory JSON nebo CSV. Time Series Insights ukládá všechna nezpracovaná data.

    [@no__t – 1Events](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Po prozkoumání událostí a vystavení vzorců a statistiky sloupců vyberte kartu **statistiky** .

    - **Vzory**: Tato funkce proaktivně rozsvítí nejdůležitější vzory ve vybrané datové oblasti. Nemusíte se pohlížet na tisíce událostí, abyste zjistili, jaké vzory vyžadují nejvíce času a energii. Pomocí Time Series Insights můžete přejít přímo na tyto statisticky významné vzory, abyste mohli pokračovat v analýze. Tato funkce je užitečná také pro následné šetření do historických dat.
    - **Statistiky sloupce**: statistiky sloupců poskytují grafy a tabulky, které rozdělují data z každého sloupce vybrané řady dat přes vybrané časové rozpětí.

      [@no__t – 1STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Nyní jste viděli různé funkce a možnosti, které jsou k dispozici v rámci webové aplikace Time Series Insights Explorer.

## <a name="next-steps"></a>Další kroky

- Naučte se [diagnostikovat a řešit problémy](time-series-insights-diagnose-and-solve-problems.md) v prostředí Time Series Insights.

- Převezměte [Azure Time Series Insights](time-series-quickstart.md) úvodní prohlídka s asistencí.
