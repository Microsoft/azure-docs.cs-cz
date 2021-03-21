---
title: Výpočet počtu a velikosti objektů BLOB pomocí Azure Storageho inventáře
description: Zjistěte, jak vypočítat počet a celkovou velikost objektů blob na kontejner.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637901"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Vypočítá počet objektů BLOB a celkovou velikost na kontejner pomocí Azure Storageho inventáře.

V tomto článku se používá funkce inventáře Azure Blob Storage a Azure Synapse k výpočtu počtu objektů BLOB a celkové velikosti objektů blob na kontejner. Tyto hodnoty jsou užitečné při optimalizaci využití objektů blob na kontejner.

Metadata objektu BLOB nejsou v této metodě obsažena. Funkce inventáře Azure Blob Storage používá [seznam objektů blob](/rest/api/storageservices/list-blobs) REST API s výchozími parametry. Proto tento příklad nepodporuje snímky, kontejnery $ a tak dále.

> [!IMPORTANT]
> Inventář objektů BLOB je momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

## <a name="enable-inventory-reports"></a>Povolit sestavy inventáře

Prvním krokem v této metodě je [Povolení sestav inventáře](blob-inventory.md#enable-inventory-reports) v účtu úložiště. Možná budete muset počkat až 24 hodin, než povolíte generování sestav inventáře první sestavy.

Když máte sestavu inventáře k analýze, udělte sobě přístup pro čtení objektu blob do kontejneru, ve kterém se nachází soubor CSV sestavy.

1. Přejděte do kontejneru se souborem sestavy CSV pro inventář.
1. Vyberte **Access Control (IAM)** a pak **přidejte přiřazení rolí** .

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Vyberte Přidat přiřazení rolí.":::

1. V rozevíracím seznamu **role** vyberte **čtečka dat objektů BLOB úložiště** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Přidání role čtečky dat objektů BLOB úložiště z rozevíracího seznamu":::

1. Zadejte e-mailovou adresu účtu, který používáte ke spuštění sestavy v poli **Vybrat** .

## <a name="create-an-azure-synapse-workspace"></a>Vytvoření pracovního prostoru Azure Synapse

V dalším kroku [Vytvořte pracovní prostor Azure synapse](/azure/synapse-analytics/get-started-create-workspace) , kde spustíte dotaz SQL, který bude hlásit výsledky inventáře.

## <a name="create-the-sql-query"></a>Vytvoření dotazu SQL

Po vytvoření pracovního prostoru Azure synapse proveďte následující kroky.

1. Přejděte na [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Na levém okraji vyberte kartu **vývoj** .
1. Vyberte velký znaménko plus (+) a přidejte položku.
1. Vyberte **skript SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Vyberte skript SQL pro vytvoření nového dotazu.":::

## <a name="run-the-sql-query"></a>Spustit dotaz SQL

1. Přidejte následující dotaz SQL do svého pracovního prostoru Azure synapse a [Přečtěte si soubor CSV s inventářem](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file).

    Pro `bulk` parametr použijte adresu URL souboru CSV sestavy inventáře, který chcete analyzovat.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Pojmenujte svůj dotaz SQL v podokně vlastnosti napravo.

1. Publikujte svůj dotaz SQL stisknutím kombinace kláves CTRL + S nebo kliknutím na tlačítko **publikovat vše** .

1. Kliknutím na tlačítko **Spustit** spusťte dotaz SQL. Počet objektů BLOB a celková velikost na kontejner jsou hlášeny v podokně **výsledků** .

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Výstup z běhu skriptu pro výpočet počtu objektů BLOB a celkovou velikost":::

## <a name="next-steps"></a>Další kroky

- [Použití Azure Storage inventáře objektů BLOB ke správě dat objektů BLOB](blob-inventory.md)
- [Vypočítat celkovou fakturační velikost kontejneru objektů BLOB](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
