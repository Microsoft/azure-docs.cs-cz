---
title: Import dat Azure Log Analytics do Power BI | Dokumentace Microsoftu
description: Power BI je Cloudová služba obchodní analýzy od Microsoftu, která poskytuje bohaté vizualizace a sestavy pro analýzu z různých sad dat.  Tento článek popisuje, jak nakonfigurovat a importovat do Power BI data služby Log Analytics a nakonfigurovat, aby automaticky aktualizovat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: f35236f369cb1870c4bdfaab77582970b0684966
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720163"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Import dat Azure Log Analytics do Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je Cloudová služba obchodní analýzy od Microsoftu, která poskytuje bohaté vizualizace a sestavy pro analýzu z různých sad dat.  Výsledky prohledávání protokolu Log Analytics můžete importovat do datové sady Power BI umožňuje využívat jeho funkce, jako je kombinování dat z různých zdrojů a sdílení sestav na webu a mobilních zařízeních.

## <a name="overview"></a>Přehled
K importu dat do Power BI z pracovního prostoru Log Analytics, vytvoření datové sady v Power BI na základě dotazu vyhledávání protokolů v Log Analytics.  Pokaždé, když se aktualizuje datovou sadu spuštění dotazu.  Potom můžete vytvářet sestavy Power BI, která využívají data z datové sady.  Vytvořte datovou sadu v Power BI, exportujete z Log Analytics pro váš dotaz [Power Query (M) jazyka](https://msdn.microsoft.com/library/mt807488.aspx).  Potom použít pro vytvoření dotazu v Power BI Desktopu a publikujte ji do Power BI jako datovou sadu.  Podrobnosti o tomto procesu jsou popsané níže.

![Log Analytics pro Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Exportovat dotaz
Začněte vytvořením [prohledávání protokolů](log-analytics-queries.md) , která vrací data ze služby Log Analytics, že chcete naplnění datové sady Power BI.  Poté exportovat tento dotaz k [Power Query (M) jazyk](https://msdn.microsoft.com/library/mt807488.aspx) které může využívat Power BI Desktopu.

1. Vytvoření prohledávání protokolů v Log Analytics k extrakci dat pro datové sady.
2. Pokud používáte portál pro prohledávání protokolu, klikněte na tlačítko **Power BI**.  Pokud používáte portál Analytics, vyberte **exportovat** > **Power BI dotazu (M)**.  Obě tyto možnosti exportovat dotaz do textového souboru s názvem **PowerBIQuery.txt**. 

    ![Export prohledávání protokolů](media/log-analytics-powerbi/export-logsearch.png) ![Export prohledávání protokolů](media/log-analytics-powerbi/export-analytics.png)

3. Otevřete textový soubor a zkopírujte jeho obsah.

## <a name="import-query-into-power-bi-desktop"></a>Importovat dotaz do Power BI Desktopu
Power BI Desktop je desktopová aplikace, která umožňuje vytvoření datové sady a sestavy, které je možné publikovat do Power BI.  Také ho můžete použít k vytvoření dotazu v jazyce Power Query exportované z Log Analytics. 

1. Nainstalujte [Power BI Desktopu](https://powerbi.microsoft.com/desktop/) Pokud nemáte ji už máte a pak otevřete aplikaci.
2. Vyberte **získat Data** > **prázdný dotaz** otevřete nový dotaz.  Potom vyberte **rozšířený Editor** a vložte obsah exportovaného souboru do dotazu. Klikněte na **Done** (Hotovo).

    ![Power BI Desktopu dotazu](media/log-analytics-powerbi/desktop-new-query.png)

5. Spustí dotaz a jeho výsledky se zobrazí.  Můžete být vyzváni k zadání pověření pro připojení k Azure.  
6. Zadejte popisný název dotazu.  Výchozí hodnota je **Query1**. Klikněte na tlačítko **zavřít a použít** na datovou sadu přidat do sestavy.

    ![Název Power BI Desktopu](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikování do Power BI
Při publikování do Power BI se vytvoří datovou sadu a sestavu.  Pokud vytvoříte sestavu v Power BI Desktopu, pak toto bude publikován s vašimi daty.  Pokud ne, pak se vytvoří prázdné sestavy.  Můžete upravit sestavu v Power BI nebo vytvořte novou u datové sady.

8. Vytvoření sestavy založené na datech.  Použití [dokumentace ke službě Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-report-view) Pokud nejste obeznámeni s ním.  Až budete připravení odeslat do Power BI, klikněte na tlačítko **publikovat**.  Po zobrazení výzvy vyberte cílové umístění ve vašem účtu Power BI.  Pokud nemáte konkrétní cíl v paměti, použijte **Můj pracovní prostor**.

    ![Publikování Power BI Desktopu](media/log-analytics-powerbi/desktop-publish.png)

3. Po dokončení publikování klikněte na tlačítko **otevřít v Power BI** otevřete Power BI s novou datovou sadu.


### <a name="configure-scheduled-refresh"></a>Konfigurace plánované aktualizace
Datová sada v Power BI bude mít stejná data, která jste předtím viděli v Power BI Desktopu.  Bude nutné aktualizovat datové sady pravidelně na dotaz spustit znovu a jeho naplnění nejnovější data ze služby Log Analytics.  

1. Klikněte na pracovní prostor, kam jste odeslali sestavy a vyberte **datových sad** nabídky. Vyberte příslušnou kontextovou nabídku vedle nová datová sada a vyberte **nastavení**. V části **přihlašovací údaje ke zdroji dat** byste měli mít zprávu, že přihlašovací údaje jsou neplatné.  Je to proto, že jste nezadali pověření zatím pro datovou sadu pro použití při aktualizuje jeho data.  Klikněte na tlačítko **upravit přihlašovací údaje** a zadat přihlašovací údaje s přístupem ke službě Log Analytics.

    ![Power BI plán](media/log-analytics-powerbi/powerbi-schedule.png)

5. V části **naplánovaná aktualizace** zapnout možnost **datech udržovat aktuální**.  Volitelně můžete změnit **obnovovací frekvence** a konkrétní čas spuštění aktualizace.

    ![Aktualizace Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Další postup
* Další informace o [prohledávání protokolů](log-analytics-queries.md) sestavování dotazů, které je možné exportovat do Power BI.
* Další informace o [Power BI](https://powerbi.microsoft.com) k vytváření vizualizací založených na exporty Log Analytics.