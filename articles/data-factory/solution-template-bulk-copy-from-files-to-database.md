---
title: Hromadné kopírování ze souborů do databáze
description: Zjistěte, jak pomocí šablony řešení hromadně kopírovat data z Azure Data Lake Storage Gen2 do Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414870"
---
# <a name="bulk-copy-from-files-to-database"></a>Hromadné kopírování ze souborů do databáze

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování dat hromadně z Azure Data Lake Storage Gen2 do Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona načítá soubory ze zdroje Azure Data Lake Storage Gen2. Pak iterates přes každý soubor ve zdroji a zkopíruje soubor do cílového úložiště dat. 

V současné době tato šablona podporuje pouze kopírování dat ve formátu **DelimitedText.** Soubory v jiných formátech dat lze také načíst ze zdrojového úložiště dat, ale nelze je zkopírovat do cílového úložiště dat.  

Šablona obsahuje tři aktivity:
- **Získejte metadata aktivity** načte soubory z Azure Data Lake Storage Gen2 a předá je následné *ForEach* aktivity.
- **ForEach** aktivita získá soubory z *get metadata aktivity* a itety každý soubor *kopírovat* aktivity.
- **Zkopírujte** aktivitu, která se nachází v části *ForEach,* a zkopírujte každý soubor ze zdrojového úložiště dat do cílového úložiště dat.

Šablona definuje následující dva parametry:
- *SourceContainer* je kořenová cesta kontejneru, kde se data zkopírují z ve vašem Azure Data Lake Storage Gen2. 
- *SourceDirectory* je cesta k adresáři pod kořenovým kontejnerem, ze kterého se data zkopírují ve vašem Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Přejděte do šablony **Hromadné kopírování ze souborů do databáze.** Vytvořte **nové** připojení ke zdrojovému úložišti Gen2. Uvědomte si, že "GetMetadataDataset" a "SourceDataset" jsou odkazy na stejné připojení úložiště zdrojových souborů.

    ![Vytvoření nového připojení ke zdrojovému úložišti dat](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Vytvořte **nové** připojení k úložišti dat jímky, do kterého kopírujete data.

    ![Vytvoření nového připojení k úložišti dat jímky](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Zobrazí se vytvořený kanál, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Pokud jste jako cíl dat v **kroku 2** uvedeném výše zvolili **Azure Synapse Analytics (dříve SQL DW),** musíte zadat připojení k úložišti objektů Blob Azure pro pracovní, jak to vyžaduje SQL Data Warehouse Polybase. Jak ukazuje následující snímek obrazovky, šablona automaticky vygeneruje *cestu úložiště* pro úložiště objektů Blob. Zkontrolujte, zda byl kontejner vytvořen po spuštění kanálu.
        
    ![Polybase, nastavení](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na **Ladění**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Po úspěšném dokončení spuštění kanálu se zobrazí výsledky podobné následujícímu příkladu:

    ![Zkontrolujte výsledek](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)