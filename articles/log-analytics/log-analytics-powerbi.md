---
title: Importovat Azure Log Analytics data do Power BI | Microsoft Docs
description: Power BI je služba cloudové obchodní analýzy od společnosti Microsoft, která poskytuje bohatých vizualizací a sestav pro analýzu dat různé sady.  Tento článek popisuje postup konfigurace a importovat data analýzy protokolů do Power BI a nakonfigurovat, aby automaticky aktualizovat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importovat Azure Log Analytics data do Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je cloudové obchodní analýzy služba společnosti Microsoft, která poskytuje bohatých vizualizací a sestav pro analýzu dat různé sady.  Výsledky hledání protokolu analýzy protokolů můžete importovat do datové sady Power BI umožňuje využívat jeho funkce, jako je například kombinováním dat z různých zdrojů a sdílení sestavy na web a mobilní zařízení.

## <a name="overview"></a>Přehled
K importu dat z pracovního prostoru analýzy protokolů do Power BI, vytvoříte datové sady ve službě Power BI založené na protokolu vyhledávací dotaz v analýzy protokolů.  Pokaždé, když se aktualizují datovou sadu spuštění dotazu.  Potom můžete vytvořit sestavy Power BI, které používají data z datové sady.  Vytvořit datová sada v Power BI, které exportujete dotazu z analýzy protokolů pro [Power Query (M) jazyk](https://msdn.microsoft.com/library/mt807488.aspx).  To poté použít k vytvoření dotazu v Power BI Desktop a potom jej publikujte do Power BI jako datové sady.  Podrobnosti tohoto procesu jsou popsané níže.

![Analýzy protokolů k Power BI.](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Export dotazu
Začněte vytvořením [hledání protokolů](log-analytics-log-search-new.md) , vrací data z analýzy protokolů chcete naplnění datové sady Power BI.  Poté exportujte tento dotaz, který [Power Query (M) jazyk](https://msdn.microsoft.com/library/mt807488.aspx) který může být použit Power BI Desktop.

1. Vytvořte hledání protokolů v analýzy protokolů pro extrahování dat pro datovou sadu.
2. Pokud používáte portál vyhledávání protokolu, klikněte na tlačítko **Power BI**.  Pokud používáte portál analýzy, vyberte **exportovat** > **Power BI dotazu (M)**.  Obě tyto možnosti dotazu exportovat do textového souboru s názvem **PowerBIQuery.txt**. 

    ![Export protokolu vyhledávání](media/log-analytics-powerbi/export-logsearch.png) ![Export protokolu vyhledávání](media/log-analytics-powerbi/export-analytics.png)

3. Otevřete textový soubor a zkopírujte její obsah.

## <a name="import-query-into-power-bi-desktop"></a>Importovat dotaz do Power BI Desktop
Power BI Desktop je plochy aplikace, která vám umožní vytvořit datové sady a sestavy, které může být publikována do služby Power BI.  Můžete ji použít i k vytvoření dotazu pomocí doplňku Power Query jazyka exportovaný z analýzy protokolů. 

1. Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Pokud nepoužíváte ji už máte a pak otevřete aplikaci.
2. Vyberte **načíst Data** > **prázdné dotazu** otevřete nový dotaz.  Potom vyberte **Upřesnit** a vložte obsah exportovaný soubor do dotazu. Klikněte na **Done** (Hotovo).

    ![Power BI Desktop dotazu](media/log-analytics-powerbi/desktop-new-query.png)

5. Spuštění dotazu a její výsledky se zobrazí.  Můžete být vyzváni k zadání pověření pro připojení k Azure.  
6. Zadejte popisný název pro dotaz.  Výchozí hodnota je **dotaz 1**. Klikněte na tlačítko **zavřete a použít** do sestavy přidat datovou sadu.

    ![Název Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikování do služby Power BI
Když publikujete do Power BI, vytvoří se datové sady a sestavy.  Pokud vytvoříte sestavu v Power BI Desktop, pak to bude publikována s daty.  Pokud ne, pak se vytvoří prázdné sestavy.  Můžete upravit sestavu v Power BI nebo vytvořte novou založené na datovou sadu.

8. Vytvoření sestavy založené na vaše data.  Použití [Power BI Desktop dokumentaci](https://docs.microsoft.com/power-bi/desktop-report-view) Pokud nejste obeznámeni s ním.  Až budete připraveni k odeslání do Power BI, klikněte na tlačítko **publikovat**.  Po zobrazení výzvy vyberte cílové umístění ve vašem účtu Power BI.  Pokud máte na určité cílové místo v paměti, používat **pracovního prostoru**.

    ![Publikování Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Po dokončení publikování, klikněte na tlačítko **otevřít v Power BI** otevřít Power BI se vaše nová datová sada.


### <a name="configure-scheduled-refresh"></a>Nakonfigurujte plánované aktualizace
Datové sady vytvořené v Power BI bude mít stejná data, která jste předtím viděli v Power BI Desktop.  Budete muset aktualizovat datová sada pravidelně k spusťte dotaz znovu a jeho naplnění nejnovější data z analýzy protokolů.  

1. V pracovním prostoru, kde jste nahráli sestavy a vyberte příkaz **datové sady** nabídky. Vyberte příslušnou kontextovou nabídku vedle vaše nová datová sada a vyberte **nastavení**. V části **přihlašovací údaje ke zdroji dat** byste měli mít zprávu, že přihlašovací údaje jsou neplatné.  Je to proto, že nebyly zadali přihlašovací údaje ještě pro datovou sadu pro použití při ho aktualizuje jeho data.  Klikněte na tlačítko **upravit přihlašovací údaje** a zadejte přihlašovací údaje s přístupem k analýze protokolů.

    ![Plán Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. V části **naplánovaná aktualizace** zapnout možnost **průběžně aktualizovat vaše data**.  Volitelně můžete změnit **obnovovací frekvence** a určitých časech spustit aktualizaci.

    ![Aktualizace Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Další postup
* Další informace o [protokolu hledání](log-analytics-log-searches.md) vytvářet dotazy, které je možné exportovat do Power BI.
* Další informace o [Power BI](http://powerbi.microsoft.com) vytvářet vizualizace podle exportuje analýzy protokolů.
