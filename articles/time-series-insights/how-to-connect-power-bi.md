---
title: Připojení prostředí k Power BI – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit Azure Time Series Insights s Power BI a sdílet, grafovat a zobrazovat data v celé organizaci.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863838"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Vizualizace dat z přehledů časových řad v Power BI

Azure Time Series Insights je platforma pro ukládání, správu, dotazování a vizualizaci dat časových řad v cloudu. [Power BI](https://powerbi.microsoft.com) je nástroj pro obchodní analýzu s bohatými vizualizačními funkcemi, který vám umožní sdílet přehledy a výsledky v celé organizaci. Obě služby lze nyní integrovat, aby bylo možné získat to nejlepší z vlastních vizualizačních schopností Time Series Insights i z power BI.

Dozvíte se, jak provést tyto akce:

* Připojení přehledů časové řady k Power BI pomocí cloudového konektoru
* Vytváření vizuálů s daty v Power BI
* Publikování sestavy do Power BI a sdílení se zbytkem vaší organizace

Na konci se dozvíte, jak vizualizovat data časových řad pomocí Azure Time Series Insights a vylepšit je pomocí silné vizualizace dat a snadných možností sdílení Power BI.

Nezapomeňte si zaregistrovat [bezplatné předplatné Azure,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Stažení a instalace nejnovější verze [Power BI Desktopu](https://powerbi.microsoft.com/downloads/)
* Mít nebo vytvořit [instanci Náhled Přehledy Azure Time Series](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Konektor Power BI je v současné době podporovaný v prostředích s *průběžným platbou* s průběžným způsobem Time Series Insights, které je konfigurované pro **teplý obchod**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Připojení dat z přehledů časové řady k Power BI

Pokud chcete prostředí Time Series Insights připojit k Power BI, postupujte takto:

1. Otevřít Průzkumník přehledů časových řad
1. Export dat jako dotazu nebo jako nezpracovaná data
1. Otevření power BI plochy
1. Načíst z vlastního dotazu

### <a name="export-data-into-power-bi-desktop"></a>Export dat do desktopu Power BI

Jak začít:

1. Otevřete Průzkumník náhledu přehledů časové řady a kaplanujte svá data.
1. Po vytvoření zobrazení, se kterým jste spokojeni, přejděte do rozevírací nabídky **Další akce** a vyberte **Připojit k Power BI**.

    [![Export Průzkumníku náhledů přehledů časové řady](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Nastavte parametry na této kartě:

   1. Určete relativní časový rámec, který chcete zobrazit. Pokud jste spokojeni s existujícízobrazení, ponechte to jako **existující časový rámec**.
   
   1. Vyberte si mezi **agregovanými** a **nezpracovanými událostmi**. 
   
       > [!NOTE]
       > Data můžete v Power BI agregovat vždy později, ale po agregaci se k nezpracovaným datům vrátit. 
       
       > [!NOTE]
       > Pro data na úrovni raw událostí je k dispozici limit počtu událostí 100 K.

       [![Připojení](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Pokud jste nenakonfigurovali instanci Time Series Insights pro **teplý obchod**, zobrazí se upozornění.

       [![Upozornění na zateplý obchod](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Můžete nakonfigurovat existující instance pro **Warm Store** na webu Azure Portal.

1. Vyberte **Kopírovat dotaz do schránky**.
1. Teď spusťte Power BI Desktop.
1. V Power BI Desktop na kartě **Domů** vyberte V levém horním rohu **nasávat data** a pak **Další**.

    [![Domovská rozevírací v rozebraka](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Vyhledejte **přehledy časových řad**, vyberte **Azure Time Series Insights (Beta)** a **pak Connect**.

    [![Připojení Power BI k přehledům časové řady](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Případně přejděte na kartu **Azure,** vyberte **Azure Time Series Insights (Beta)** a pak **Connect**.
    
1. Zobrazí se dialogové okno se zprávou s žádostí o oprávnění k připojení k prostředkům jiných výrobců. Vyberte **pokračovat**.

    [![Zvolte Vytvořit vlastní dotaz.](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. V rozevírací nabídce v části **Zdroj dat**zvolte Vytvořit **vlastní dotaz**. Vložte ze schránky do volitelného pole **Vlastní dotaz (volitelné)** níže a stiskněte **tlačítko OK**.

    [![Předajte vlastní dotaz a vyberte OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Tabulka dat se nyní načte. Stisknutím **tlačítka Load** se načtete do Power BI.

    [![Kontrola načtených dat v tabulce a výběr možnosti Načíst](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Pokud jste tyto kroky dokončili, přejděte na další část.

## <a name="create-a-report-with-visuals"></a>Vytvoření sestavy s vizuály

Teď, když jste importovali data do Power BI, je čas vytvořit sestavu s vizuály.

1. Na levé straně okna zkontrolujte, zda jste vybrali zobrazení **Sestavy.**

    [![Výběr zobrazení sestavy](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Ve sloupci **Vizualizace** vyberte vizuál. Vyberte například **spojnicový graf**. Tím přidáte na plátno prázdný spojnicový graf.
 
1.  V seznamu **Pole** vyberte **Časové razítko** a přetažením do pole **Osa** zobrazte položky podél osy X.

1.  Znovu vyberte v seznamu **Pole** **Id časové řady** a přetažením do pole **Hodnoty** zobrazte položky podél osy Y.

    [![Vytvoření spojnicového grafu](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Chcete-li na plátno přidat další graf, vyberte kdekoli na plátně mimo spojnicový graf a tento postup opakujte.

    [![Vytvoření dalších grafů ke sdílení](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Po vytvoření sestavy ji můžete publikovat ve službě Power BI Reporting Services.

## <a name="advanced-editing"></a>Pokročilé úpravy

Pokud jste už v Power BI načetli datovou sadu, ale chcete upravit dotaz (třeba parametry data a času nebo ID prostředí), můžete to udělat prostřednictvím funkce Rozšířeného editoru Power BI. Další informace najdete v dokumentaci k [Power BI.](https://docs.microsoft.com/power-bi/desktop-query-overview)

Jako přehled:

1. V Power BI Desktopu vyberte **Upravit dotazy**.
1. Stiskněte **klávesu Advanced Editor**.

    [![Úpravy dotazů v rozšířeném editoru](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Upravte datovou část JSON podle potřeby.
1. V yberte **Hotovo** a potom **Zavřít & Použít** v okně Power Query Editor **.**

Rozhraní bude nyní odrážet požadované změny, které jste použili.  

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [konceptech konektorů Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) pro Azure Time Series Insights.

* Přečtěte si další informace o [desktopu Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Přečtěte si [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) a Time Series Insights Náhled [Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
