---
title: Import dat Azure Log Analytics do Power BI | Dokumenty společnosti Microsoft
description: Power BI je cloudová obchodní analytická služba od Microsoftu, která poskytuje bohaté vizualizace a sestavy pro analýzu různých sad dat.  Tento článek popisuje, jak nakonfigurovat a importovat data Log Analytics do Power BI a nakonfigurovat je tak, aby se automaticky aktualizovaly.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659279"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Import dat protokolu Azure Monitoru do Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je cloudová obchodní analytická služba od Microsoftu, která poskytuje bohaté vizualizace a sestavy pro analýzu různých sad dat.  Výsledky dotazu protokolu Azure Monitoru můžete importovat do datové sady Power BI, abyste mohli využívat jeho funkce, jako je kombinování dat z různých zdrojů a sdílení sestav na webu a mobilních zařízeních.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Přehled
Pokud chcete importovat data z [pracovního prostoru Log Analytics](manage-access.md) v Azure Monitoru do Power BI, vytvořte datovou sadu v Power BI na základě [dotazu protokolu](../log-query/log-query-overview.md) ve Službě Azure Monitor.  Dotaz je spuštěn při každé aktualizaci datové sady.  Potom můžete vytvářet sestavy Power BI, které používají data z datové sady.  Pokud chcete vytvořit datovou sadu v Power BI, exportujte dotaz z Log Analytics do [jazyka Power Query (M).](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  Pak to použijete k vytvoření dotazu v Power BI Desktopu a jeho publikování do Power BI jako datové sady.  Podrobnosti pro tento proces jsou popsány níže.

![Log Analytics do Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportovat dotaz
Začněte vytvořením [dotazu protokolu,](../log-query/log-query-overview.md) který vrátí data, která chcete naplnit datovou sadou Power BI.  Potom exportujete tento dotaz do [jazyka Power Query (M),](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) který může power BI Desktop používat.

1. [Vytvořte dotaz protokolu v Log Analytics](../log-query/get-started-portal.md) extrahovat data pro vaši datovou sadu.
2. Vyberte **Exportovat** > **Power BI query (M)**.  Tím se dotaz exportuje do textového souboru s názvem **PowerBIQuery.txt**. 

    ![Exportovat hledání protokolu](media/powerbi/export-analytics.png)

3. Otevřete textový soubor a zkopírujte jeho obsah.

## <a name="import-query-into-power-bi-desktop"></a>Import dotazu na Power BI Desktop
Power BI Desktop je desktopová aplikace, která umožňuje vytvářet datové sady a sestavy, které se dají publikovat v Power BI.  Můžete ji také použít k vytvoření dotazu pomocí jazyka Power Query exportovaného z Azure Monitoru. 

1. Pokud power [BI desktop](https://powerbi.microsoft.com/desktop/) ještě nemáte, nainstalujte ho a pak aplikaci otevřete.
2. Chcete-li otevřít nový dotaz, vyberte **možnost Získat prázdný** > **dotaz** dat.  Pak vyberte **Rozšířený editor** a vložte obsah exportovaného souboru do dotazu. Klikněte na **Done** (Hotovo).

    ![Dotaz power BI desktopu](media/powerbi/desktop-new-query.png)

5. Dotaz se spustí a jeho výsledky jsou zobrazeny.  Můžete být vyzváni k zadání přihlašovacích údajů pro připojení k Azure.  
6. Zadejte popisný název dotazu.  Výchozí hodnota je **Query1**. Kliknutím na **Zavřít a Použít** přidejte datovou sadu do sestavy.

    ![Název desktopu Power BI](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikovat do Power BI
Při publikování v Power BI se vytvoří datová sada a sestava.  Pokud vytvoříte sestavu v Power BI Desktopu, bude se to publikovat spolu s vašimi daty.  Pokud ne, bude vytvořena prázdná sestava.  Sestavu můžete upravit v Power BI nebo vytvořit novou na základě datové sady.

1. Vytvořte sestavu na základě vašich dat.  [Dokumentaci k Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-report-view) použijte, pokud s ní nejste obeznámeni.  
1. Až ho budete chtít poslat do Power BI, klikněte na **Publikovat**.  
1. Po zobrazení výzvy vyberte cíl ve svém účtu Power BI.  Pokud nemáte na mysli konkrétní cíl, použijte **můj pracovní prostor**.

    ![Publikování Power BI Desktopu](media/powerbi/desktop-publish.png)

1. Po dokončení publikování klikněte **v Power BI** na Otevření Power BI s novou datovou sadou.


### <a name="configure-scheduled-refresh"></a>Konfigurace plánované aktualizace
Datová sada vytvořená v Power BI bude mít stejná data, která jste viděli v Power BI Desktopu.  Chcete-li dotaz znovu spustit a naplnit nejnovějšími daty z Azure Monitoru, je třeba pravidelně aktualizovat datovou sadu.  

1. Klikněte na pracovní prostor, do kterého jste sestavu nahráli, a vyberte nabídku **Datové sady.** 
1. Vyberte místní nabídku vedle nové datové sady a vyberte **Nastavení**. 
1. V části **Pověření zdroje dat** byste měli mít zprávu, že pověření jsou neplatná.  Důvodem je, že jste ještě nezadali pověření pro datovou sadu, která se má použít při aktualizaci dat.  
1. Klikněte na **Upravit přihlašovací údaje** a zadejte přihlašovací údaje s přístupem k pracovnímu prostoru Log Analytics v Azure Monitoru. Pokud požadujete dvoufaktorové ověřování, vyberte **OAuth2** pro **ověřování metoda,** která má být vyzváni k přihlášení s pověření.

    ![Plán Power BI](media/powerbi/powerbi-schedule.png)

5. V části **Plánovaná aktualizace** zapněte možnost **Udržovat data aktuální**.  Volitelně můžete změnit **frekvenci aktualizace** a jeden nebo více konkrétních časů pro spuštění aktualizace.

    ![Aktualizace Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Další kroky
* Přečtěte si o [hledání protokolů](../log-query/log-query-overview.md) a vytváření dotazů, které se dá exportovat do Power BI.
* Přečtěte si další informace o [Power BI](https://powerbi.microsoft.com) k vytváření vizualizací na základě exportů protokolů Azure Monitoru.
