---
title: Připojení prostředí k Power BI-Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak připojit Azure Time Series Insights, abyste Power BI mohli sdílet, sestavovat a zobrazovat data napříč vaší organizací.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740711"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Vizualizace dat z Azure Time Series Insights v Power BI

Azure Time Series Insights je platforma pro ukládání, správu, dotazování a vizualizaci dat časových řad v cloudu. [Power BI](https://powerbi.microsoft.com) je nástroj pro obchodní analýzu s bohatou schopností vizualizace, který umožňuje sdílet přehledy a výsledky napříč vaší organizací. Obě služby se teď dají integrovat, což vám umožní rozšířit výkonné analýzy Azure Time Series Insights díky funkcím silného sdílení dat a snadnému sdílení Power BI.

Dozvíte se, jak provést tyto akce:

* Připojení Azure Time Series Insights k Power BI pomocí nativního konektoru Azure Time Series Insights
* Vytváření vizuálů s daty časových řad v Power BI
* Publikování sestavy pro Power BI a sdílení se zbytkem vaší organizace


## <a name="prerequisites"></a>Požadavky

* Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.
* Stáhněte a nainstalujte si nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Vytvoření nebo vytvoření [prostředí Azure Time Series Insights Gen2](./how-to-provision-manage.md)

Zkontrolujte prosím [zásady přístupu k prostředí](./concepts-access-policies.md) a ujistěte se, že máte přímý přístup k Azure Time Series Insights Gen2 prostředí pro přístup hosta. 

> [!IMPORTANT]
> * Stáhněte a nainstalujte nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/downloads/). Pokud chcete postupovat podle kroků v tomto článku, ujistěte se, že máte nainstalovanou minimálně 2020 Power BI Desktop verzi z prosince. 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Připojení dat z Azure Time Series Insights k Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. Export dat do Power BI plochy

Jak začít:

1. Otevřete Azure Time Series Insights Průzkumníku Gen2 a dootevírejte data. Jedná se o data, která budou exportována do Power BI.
1. Po vytvoření zobrazení, které jste splnili, přejděte do rozevírací nabídky **Další akce** a vyberte **připojit k Power BI**.

    [![Export Průzkumníka Azure Time Series Insights Gen2](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Nastavte parametry pro export:

   * **Formát dat**: Určete, zda chcete exportovat **agregovaná data** nebo **nezpracované události** do Power BI. 

       > [!NOTE]
       > * Pokud exportujete nezpracované události, můžete tato data později v Power BI agregovat. Pokud ale exportujete agregovaná data, nebudete se moct vrátit na nezpracovaná data v Power BI. 
       > * Pro data na úrovni nezpracované události je povolený limit počtu událostí 250 000.

   * **Časový rozsah**: vyberte, jestli se chcete podívat na **pevný** časový rozsah nebo **nejnovější** data v Power BI. Volba pevného časového rozsahu znamená, že se data v rozsahu hledání, která jste nastavili v grafu, exportují do Power BI. Když zvolíte poslední časový rozsah, znamená to, že Power BI načte nejnovější data pro rozsah hledání, který jste si zvolili (například Pokud seřadíte 1 hodinu dat a vyberete nastavení "poslední", Power BI konektor vždycky provede dotazy na nejnovější 1 hodinu dat.)
  
   * **Typ úložiště**: vyberte, jestli byste chtěli spustit vybraný dotaz pro úložiště s **teplem** nebo z **chladírenského** skladu. 

    > [!TIP]
    > * Azure Time Series Insights Explorer automaticky vybere Doporučené parametry v závislosti na datech, která jste se rozhodli exportovat. 

1. Po nakonfigurování nastavení vyberte **Kopírovat dotaz do schránky**.

    [![Exportovat modální okno Průzkumníka Azure Time Series Insights](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. Spusťte Power BI Desktop.
   
3. V Power BI Desktop na kartě **Domů** vyberte v levém horním rohu **získat data** a pak **Další**.

    [![Získání dat v Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. Vyhledejte **Azure Time Series Insights**, vyberte **Azure Time Series Insights (beta)** a pak se **Připojte**.

    [![Připojit Power BI k Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Případně přejděte na kartu **Azure** , vyberte **Azure Time Series Insights (beta)** a pak se **Připojte**.

5. Vložte dotaz, který jste zkopírovali z aplikace Azure Time Series Insights Explorer do pole **vlastní dotaz** , a pak stiskněte tlačítko **OK**.

    [![Vložte vlastní dotaz a vyberte OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  Tabulka dat se teď načte. Stisknutím klávesy **Load** se načtěte do Power BI. Pokud chcete data převést na data, můžete to udělat nyní kliknutím na **transformovat data**. Data můžete po načtení také transformovat.

    [![Zkontrolujte data v tabulce a vyberte načíst.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Vytvoření sestavy s vizuály

Teď, když jste data importovali do Power BI, je čas sestavovat sestavu s vizuály.

1. Na levé straně okna se ujistěte, že jste vybrali zobrazení **sestavy** .

    [![Snímek obrazovky zobrazuje ikonu zobrazení sestavy.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Ve sloupci **vizualizace** vyberte svůj vizuál výběru. Vyberte například **Spojnicový graf**. Tím se na plátno přidá prázdný spojnicový graf.

1.  V seznamu **pole** vyberte **_Timestamp** a přetáhněte ji do pole **osa** , abyste zobrazili čas podél osy X. Nezapomeňte přepnout na **_Timestamp** jako hodnotu pro **osu** (výchozí je **hierarchie data**).

    [![Vyberte _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Znovu v seznamu **pole** vyberte proměnnou, kterou chcete vykreslit, a přetáhněte ji do pole **hodnoty** , abyste zobrazili hodnoty podél osy Y. Vyberte svou hodnotu ID časové řady a přetáhněte ji do pole **Legenda** , abyste v grafu mohli vytvořit více řádků, jedno za ID časové řady. Tato akce vykreslí zobrazení podobné tomu, co je k dispozici v Průzkumníkovi Azure Time Series Insights. 

    [![Vytvoření základního spojnicového grafu](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Pokud chcete na plátno přidat další graf, vyberte kdekoliv na plátně mimo spojnicový graf a tento postup opakujte.

    [![Vytvoření dalších grafů ke sdílení](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Jakmile sestavu vytvoříte, můžete ji publikovat ve službě Power BI Reporting Services a sdílet s ostatními uživateli ve vaší organizaci.

## <a name="advanced-editing"></a>Rozšířené úpravy
Pokud jste již datovou sadu načetli v Power BI, ale chcete upravit dotaz (například parametry data a času nebo ID prostředí), můžete to provést prostřednictvím funkce Rozšířený editor Power BI. Další informace o tom, jak provádět změny pomocí **editoru Power Query**, najdete v [dokumentaci k Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) . 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Power BI desktopu](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Přečtěte si informace o [dotazování na data](concepts-query-overview.md) v Azure Time Series Insights Gen2.
