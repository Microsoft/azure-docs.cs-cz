---
title: Vizualizace dat z Azure Průzkumník dat pomocí importovaného dotazu Power BI
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností pro vizualizaci dat v Power BI: import dotazu z Azure Průzkumník dat.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562475"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Vizualizace dat pomocí dotazu importovaného do Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Azure Průzkumník dat poskytuje tři možnosti pro připojení k datům v Power BI: použijte integrovaný konektor, importujte dotaz z Azure Průzkumník dat nebo použijte dotaz SQL. V tomto článku se dozvíte, jak importovat dotaz, abyste mohli získat data a vizualizovat ho v sestavě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete následující:

* E-mailový účet organizace, který je členem Azure Active Directory, abyste se mohli připojit ke [clusteru azure Průzkumník dat Help](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **Stáhnout zdarma**)

* [Desktopová aplikace pro Azure Průzkumník dat](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Získat data z Azure Průzkumník dat

Nejdřív vytvoříte dotaz v aplikaci Azure Průzkumník dat Desktop a vyexportujete ho pro použití v Power BI. Pak se připojíte ke clusteru Azure Průzkumník dat Help a přenesete podmnožinu dat z tabulky *StormEvents* . [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V prohlížeči spusťte aplikaci Azure Průzkumník dat desktopu tak, že přejdete na [https://help.kusto.windows.net/](https://help.kusto.windows.net/) .

1. V aplikaci klasické pracovní plochy zkopírujte do pravého horního okna dotazu následující dotaz a pak ho spusťte.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Prvních několik řádků sady výsledků dotazu by měl vypadat podobně jako na následujícím obrázku.

    ![Výsledky dotazu](media/power-bi-imported-query/query-results.png)

1. Na kartě **nástroje** vyberte **dotaz pro Power BI** pak na **OK**.

    ![Exportovat dotaz](media/power-bi-imported-query/export-query.png)

1. V Power BI Desktop na kartě **Domů** vyberte **získat data** a pak **prázdný dotaz**.

    ![Získání dat](media/power-bi-imported-query/get-data.png)

1. V editoru Power Query na kartě **Domů** vyberte **Upřesnit Editor**.

1. V okně **Rozšířený editor** vložte exportovaný dotaz a potom vyberte **Hotovo**.

    ![Vložit dotaz](media/power-bi-imported-query/paste-query.png)

1. V hlavním okně editoru Power Query vyberte **Upravit přihlašovací údaje**. Vyberte **účet organizace**, přihlaste se a pak vyberte **připojit**.

    ![Upravit přihlašovací údaje](media/power-bi-imported-query/edit-credentials.png)

1. Na kartě **Domů** vyberte **Zavřít & použít**.

    ![Zavřít a použít](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste vytvořili pro tento článek, odstraňte soubor Power BI Desktop (. pbix).

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru služby Azure Průzkumník dat pro Power BI](power-bi-connector.md)