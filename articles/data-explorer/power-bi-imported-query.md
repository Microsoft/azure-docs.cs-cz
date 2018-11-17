---
title: 'Rychlý start: Vizualizujte data pomocí dotazu importované do Power BI'
description: 'V tomto rychlém startu se dozvíte, jak chcete použít jeden z těchto tří možností pro vizualizace dat v Power BI: import dotaz v Průzkumníku dat Azure.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 139b20a2390c7301bd83113c3e98be40846fab22
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854794"
---
# <a name="quickstart-visualize-data-using-a-query-imported-into-power-bi"></a>Rychlý start: Vizualizujte data pomocí dotazu importované do Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Průzkumník služby Azure Data poskytuje tři možnosti připojení k datům v Power BI: použití integrovaného konektoru importovat dotaz v Průzkumníku dat Azure a použít dotaz SQL. V tomto rychlém startu se dozvíte, jak importovat dotaz, aby mohli získat data a vizualizace v sestavě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k dokončení tohoto rychlého startu:

* Účet organizace e-mailu, který je členem skupiny Azure Active directory, abyste se mohli připojit k [clusteru help Průzkumník dat Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **DOWNLOAD FREE**)

* [Desktopové aplikace Průzkumník dat Azure](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníku dat Azure

Nejprve vytvořte dotaz v desktopové aplikaci Průzkumník dat Azure a exportujte ho pro použití v Power BI. Potom připojení ke clusteru help Průzkumník dat Azure a umožňuje přinést si podmnožinu dat z *StormEvents* tabulky. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V prohlížeči přejděte na [ https://help.kusto.windows.net/ ](https://help.kusto.windows.net/) spusťte desktopovou aplikaci Průzkumník dat Azure.

1. V desktopové aplikaci zkopírujte do okna dotazu pravém pak spusťte následující dotaz.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Několik prvních řádků sady výsledků dotazu by měla vypadat podobně jako na následujícím obrázku.

    ![Výsledky dotazu](media/power-bi-imported-query/query-results.png)

1. Na **nástroje** kartu, vyberte možnost **dotazu do Power BI** pak **OK**.

    ![Exportovat dotaz](media/power-bi-imported-query/export-query.png)

1. V Power BI Desktopu na **Domů** kartu, vyberte možnost **získat Data** pak **prázdný dotaz**.

    ![Získání dat](media/power-bi-imported-query/get-data.png)

1. V editoru Power Query na **Domů** kartu, vyberte možnost **Rozšířený editor**.

1. V **Rozšířený editor** okna a pak vyberte dotaz, který jste exportovali vložení **provádí**.

    ![Vložit dotaz](media/power-bi-imported-query/paste-query.png)

1. V hlavním okně Power Query Editor vyberte **upravit přihlašovací údaje**. Vyberte **účet organizace**, přihlaste se a pak vyberte **připojit**.

    ![Upravit přihlašovací údaje](media/power-bi-imported-query/edit-credentials.png)

1. Na **Domů** kartu, vyberte možnost **zavřít a použít**.

    ![Zavřít a použít](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavy, kterou jste vytvořili pro účely tohoto rychlého startu, odstraňte soubor Power BI Desktopu (.pbix).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlý start: Vizualizujte data pomocí importovaných dotazů v Power BI](power-bi-sql-query.md)