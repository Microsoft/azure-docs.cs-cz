---
title: Vizualizace dat z Azure Data Exploreru pomocí importovaného dotazu Power BI
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností vizualizace dat v Power BI: import dotazu z Azure Data Exploreru.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562475"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Vizualizace dat pomocí dotazu importovaného do Power BI

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Azure Data Explorer nabízí tři možnosti pro připojení k datům v Power BI: použít integrovaný konektor, importovat dotaz z Azure Data Exploreru nebo použít dotaz SQL. V tomto článku se zobrazí postup importu dotazu, abyste mohli získat data a vizualizovat ho v sestavě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* E-mailový účet organizace, který je členem adresáře Azure Active Directory, takže se můžete připojit k [clusteru nápovědy Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (zvolte **STÁHNOUT ZDARMA)**

* [Desktopová aplikace Azure Data Explorer](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníka dat Azure

Nejprve vytvoříte dotaz v desktopové aplikaci Azure Data Explorer a exportujete ho pro použití v Power BI. Potom se připojíte ke clusteru nápovědy Azure Data Explorer a přineste podmnožinu dat z tabulky *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V prohlížeči přejděte na [https://help.kusto.windows.net/](https://help.kusto.windows.net/) spuštění desktopové aplikace Azure Data Explorer.

1. V aplikaci klasické pracovní plochy zkopírujte následující dotaz do pravého horního okna dotazu a spusťte jej.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Prvních několik řádků sady výsledků by mělo vypadat podobně jako na následujícím obrázku.

    ![Výsledky dotazu](media/power-bi-imported-query/query-results.png)

1. Na kartě **Nástroje** vyberte **Dotaz na Power BI** a pak **OK**.

    ![Exportovat dotaz](media/power-bi-imported-query/export-query.png)

1. V Power BI Desktopu na kartě **Domů** vyberte **Získat data** a pak **prázdný dotaz**.

    ![Získání dat](media/power-bi-imported-query/get-data.png)

1. V Editoru Power Query vyberte na kartě **Domů** **rozšířený editor**.

1. V okně **Rozšířený editor** vložte exportovaný dotaz a vyberte **Hotovo**.

    ![Vložit dotaz](media/power-bi-imported-query/paste-query.png)

1. V hlavním okně Power Query Editoru vyberte **Upravit přihlašovací údaje**. Vyberte **Účet organizace**, přihlaste se a pak vyberte **Připojit**.

    ![Upravit přihlašovací údaje](media/power-bi-imported-query/edit-credentials.png)

1. Na kartě **Domů** vyberte **Zavřít & Použít**.

    ![Zavřít a použít](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste pro tento článek vytvořili, odstraňte soubor Power BI Desktop (.pbix).

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru Azure Data Explorer pro Power BI](power-bi-connector.md)