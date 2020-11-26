---
title: Import dat služby Azure Log Analytics do Power BI | Microsoft Docs
description: Power BI je cloudová služba pro obchodní analýzy od Microsoftu, která poskytuje bohatou vizualizaci a sestavy pro analýzu různých sad dat.  Tento článek popisuje, jak nakonfigurovat a importovat Log Analytics data do Power BI a nakonfigurovat je tak, aby se automaticky aktualizovala.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 53277f64c3d1b03572732157756da1fececbcd43
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184565"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importovat data protokolu Azure Monitor do Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je cloudová služba pro obchodní analýzy od Microsoftu, která poskytuje bohatou vizualizaci a sestavy pro analýzu různých sad dat.  Výsledky dotazu protokolu Azure Monitor můžete importovat do datové sady Power BI, abyste mohli využít jeho funkce, jako je například kombinování dat z různých zdrojů a sdílení sestav na webových a mobilních zařízeních.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Přehled
Chcete-li importovat data z [pracovního prostoru Log Analytics](manage-access.md) v Azure Monitor do Power BI, vytvořte datovou sadu v Power BI na základě [dotazu protokolu](../log-query/log-query-overview.md) v Azure monitor.  Dotaz se spustí pokaždé, když se datová sada aktualizuje.  Potom můžete vytvořit sestavy Power BI, které používají data z datové sady.  Chcete-li vytvořit datovou sadu v Power BI, exportujte dotaz z Log Analytics do [jazyka Power Query (M)](/powerquery-m/power-query-m-language-specification).  Pak použijete tuto akci k vytvoření dotazu v Power BI Desktop a pak ho publikujete do Power BI jako datovou sadu.  Podrobnosti o tomto procesu jsou popsány níže.

![Log Analytics Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportovat dotaz
Začněte vytvořením [dotazu protokolu](../log-query/log-query-overview.md) , který vrátí data, která chcete vyplnit Power BI datovou sadu.  Pak tento dotaz exportujte do [jazyka Power Query (M)](/powerquery-m/power-query-m-language-specification) , který lze použít v Power BI Desktop.

1. [Vytvořte dotaz protokolu v Log Analytics](../log-query/log-analytics-tutorial.md) pro extrakci dat pro datovou sadu.
2. Vyberte **exportovat**  >  **Power BI dotaz (M)**.  Tím se dotaz exportuje do textového souboru s názvem **PowerBIQuery.txt**. 

    ![Exportovat hledání protokolu](media/powerbi/export-analytics.png)

3. Otevřete textový soubor a zkopírujte jeho obsah.

## <a name="import-query-into-power-bi-desktop"></a>Importovat dotaz do Power BI Desktop
Power BI Desktop je desktopová aplikace, která umožňuje vytvořit datové sady a sestavy, které se dají publikovat do Power BI.  Můžete ji také použít k vytvoření dotazu pomocí Power Queryho jazyka exportovaného z Azure Monitor. 

1. Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/desktop/) , pokud ho ještě nemáte, a pak aplikaci otevřete.
2. Vyberte **načíst data**  >  **prázdný dotaz** pro otevření nového dotazu.  Pak vyberte **Rozšířený editor** a vložte obsah exportovaného souboru do dotazu. Klikněte na **Hotovo**.

    ![Power BI Desktop dotaz](media/powerbi/desktop-new-query.png)

5. Dotaz se spustí a zobrazí se jeho výsledky.  Může se zobrazit výzva k zadání přihlašovacích údajů pro připojení k Azure.  
6. Zadejte popisný název dotazu.  Výchozí hodnota je **Dotaz1**. Kliknutím na **Zavřít a použít** přidejte datovou sadu do sestavy.

    ![Název Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikovat do Power BI
Když publikujete do Power BI, vytvoří se datová sada a sestava.  Pokud vytvoříte sestavu v Power BI Desktop, bude tato zpráva publikována s Vašimi daty.  V takovém případě se vytvoří prázdná sestava.  Sestavu můžete upravit v Power BI nebo vytvořit novou v závislosti na datové sadě.

1. Vytvořte sestavu na základě vašich dat.  Pokud s ním nejste obeznámeni, použijte [dokumentaci Power BI Desktop](/power-bi/desktop-report-view) .  
1. Až budete připraveni ho odeslat Power BI, klikněte na **publikovat**.  
1. Po zobrazení výzvy vyberte cíl v účtu Power BI.  Pokud nemáte na mysli konkrétní cíl, použijte **pracovní prostor**.

    ![Publikování Power BI Desktop](media/powerbi/desktop-publish.png)

1. Po dokončení publikování kliknutím na **otevřít v Power BI** otevřete Power BI s novou datovou sadou.


### <a name="configure-scheduled-refresh"></a>Konfigurace plánované aktualizace
Datová sada vytvořená v Power BI bude mít stejná data, která jste předtím viděli v Power BI Desktop.  Musíte pravidelně aktualizovat datovou sadu a znovu spustit dotaz a naplnit ho nejnovějšími daty z Azure Monitor.  

1. Klikněte na pracovní prostor, kam jste nahráli sestavu, a vyberte nabídku **datové sady** . 
1. Vyberte kontextovou nabídku vedle vaší nové datové sady a vyberte **Nastavení**. 
1. V části **přihlašovací údaje ke zdroji dat** byste měli mít zprávu, že přihlašovací údaje jsou neplatné.  Důvodem je to, že jste ještě nezadali přihlašovací údaje pro datovou sadu, která se používá při aktualizaci dat.  
1. Klikněte na **Upravit přihlašovací údaje** a zadejte přihlašovací údaje s přístupem k pracovnímu prostoru Log Analytics v Azure monitor. Pokud požadujete dvojúrovňové ověřování, vyberte **OAuth2** , aby se pro **metodu ověřování** zobrazila výzva k přihlášení pomocí vašich přihlašovacích údajů.

    ![Plán Power BI](media/powerbi/powerbi-schedule.png)

5. V části **plánovaná aktualizace** zapněte možnost a **Udržujte si data v aktuálním stavu**.  Volitelně můžete změnit **Četnost aktualizace** a jednu nebo více konkrétních časů pro spuštění aktualizace.

    ![Power BI aktualizace](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Další kroky
* Přečtěte si informace o [prohledávání protokolu](../log-query/log-query-overview.md) k vytváření dotazů, které je možné exportovat do Power BI.
* Přečtěte si další informace o [Power BI](https://powerbi.microsoft.com) k vytváření vizualizací na základě Azure Monitorch exportů protokolů.