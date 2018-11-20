---
title: 'Rychlý start: Vizualizujte data pomocí Průzkumníku dat Azure konektoru pro Power BI'
description: 'V tomto rychlém startu se dozvíte, jak chcete použít jeden z těchto tří možností pro vizualizace dat v Power BI: konektor Power BI pro Průzkumník dat Azure.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 3cb8f52677991997a0176a9f8d408e2fd6d2d8d9
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51974267"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Rychlý start: Vizualizujte data pomocí Průzkumníku dat Azure konektoru pro Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Průzkumník služby Azure Data poskytuje tři možnosti připojení k datům v Power BI: použití integrovaného konektoru importovat dotaz v Průzkumníku dat Azure a použít dotaz SQL. V tomto rychlém startu se dozvíte, jak získat data a vizualizace v sestavě Power BI pomocí integrovaného konektoru.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k dokončení tohoto rychlého startu:

* Účet organizace e-mailu, který je členem skupiny Azure Active directory, abyste se mohli připojit k [clusteru help Průzkumník dat Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **DOWNLOAD FREE**)

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníku dat Azure

Nejprve připojte ke clusteru help Průzkumník dat Azure a pak přenést v podmnožinu dat z *StormEvents* tabulky. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V Power BI Desktopu na **Domů** kartu, vyberte možnost **získat Data** pak **Další**.

    ![Získání dat](media/power-bi-connector/get-data-more.png)

1. Vyhledejte *Průzkumník dat Azure*vyberte **Průzkumník dat Azure (beta verze)** pak **připojit**.

    ![Vyhledání a získání dat](media/power-bi-connector/search-get-data.png)

1. Na obrazovce **Předběžná verze konektoru** vyberte **Pokračovat**.

1. Na další obrazovce vyplňte formulář s následujícími informacemi.

    ![Možnosti clusteru, databáze a tabulky](media/power-bi-connector/cluster-database-table.png)

    **Nastavení** | **Hodnota** | **Popis pole**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | Adresa URL clusteru help. Pro další clustery, adresa URL je ve formě *https://\<ClusterName\>.\< Oblast\>. kusto.windows.net*. |
    | Databáze | Ponechte prázdné | Databáze, která je hostovaná v clusteru, ke kterému se připojujete. Vybereme to v pozdějším kroku. |
    | Název tabulky | Ponechte prázdné | Jednu z tabulek v databázi nebo dotaz, jako jsou "StormEvents | Využijte 1000'. Vybereme to v pozdějším kroku. |
    | Upřesnit možnosti | Ponechte prázdné | Nastavit možnosti pro vaše dotazy, jako je například výsledek velikost. |
    | Režim připojení dat | *DirectQuery* | Určuje, jestli Power BI dokončí import dat nebo připojuje přímo ke zdroji dat. Pomocí tohoto konektoru můžete použít jednu z možností. |
    | | | |

1. Pokud ještě nemáte připojení ke clusteru pomoc, přihlaste se. Přihlaste se pomocí účtu organizace a pak vyberte **připojit**.

    ![Přihlásit se](media/power-bi-connector/sign-in.png)

1. Na **Navigátor** obrazovky, rozbalte **ukázky** databáze, vyberte **StormEvents** pak **upravit**.

    ![Vyberte tabulku](media/power-bi-connector/select-table.png)

    Tabulka se otevře v editoru Power Query, kde můžete před importem dat upravit řádky a sloupce.

1. V editoru Power Query vyberte šipku vedle položky **DamageCrops** sloupce pak **seřadit sestupně**.

    ![Seřadit sestupně DamageCrops](media/power-bi-connector/sort-descending.png)

1. Na **Domů** kartu, vyberte možnost **zachovat řádky** pak **zachovat horní řádky**. Zadejte hodnotu *1000* pro prvních 1000 řádků tabulky seřazené.

    ![Zachovat horní řádky](media/power-bi-connector/keep-top-rows.png)

1. Na **Domů** kartu, vyberte možnost **zavřít a použít**.

    ![Zavřít a použít](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavy, kterou jste vytvořili pro účely tohoto rychlého startu, odstraňte soubor Power BI Desktopu (.pbix).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlý start: Vizualizujte data pomocí importovaných dotazů v Power BI](power-bi-imported-query.md)