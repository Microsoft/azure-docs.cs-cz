---
title: Hromadné kopírování ze souborů do databáze
description: Naučte se, jak pomocí šablony řešení hromadně kopírovat data z Azure Data Lake Storage Gen2 do Azure synapse Analytics/Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: c7f4cba10117efef4099b3524b49cae313593a9a
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442707"
---
# <a name="bulk-copy-from-files-to-database"></a>Hromadné kopírování ze souborů do databáze

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít ke hromadnému kopírování dat z Azure Data Lake Storage Gen2 do služby Azure synapse Analytics/Azure SQL Database.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona načte soubory ze zdroje Azure Data Lake Storage Gen2. Pak projde všechny soubory ve zdroji a zkopíruje soubor do cílového úložiště dat. 

V současné době tato šablona podporuje pouze kopírování dat ve formátu **DelimitedText** . Soubory v jiných datových formátech lze také načíst ze zdrojového úložiště dat, ale nelze je zkopírovat do cílového úložiště dat.  

Šablona obsahuje tři aktivity:
- Aktivita **získat metadata** načte soubory z Azure Data Lake Storage Gen2 a předá je do následné aktivity *foreach* .
- Aktivita **foreach** získá soubory z aktivity *získat metadata* a každý soubor projde do aktivity *kopírování* .
- Aktivita **kopírování** se nachází v aktivitě *foreach* a kopíruje jednotlivé soubory ze zdrojového úložiště dat do cílového úložiště dat.

Šablona definuje následující dva parametry:
- *SourceContainer* je cesta ke kořenovému kontejneru, ze které se zkopírují data v Azure Data Lake Storage Gen2. 
- *SourceDirectory* je cesta k adresáři v kořenovém kontejneru, ve kterém se data zkopírují z ve vašem Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Přejít na **soubor hromadného kopírování ze souborů do šablony databáze** . Vytvoří **nové** připojení ke zdrojovému úložišti Gen2. Upozorňujeme, že "GetMetadataDataset" a "SourceDataset" odkazují na stejné připojení ke zdrojovému úložišti souborů.

    ![Vytvoří nové připojení ke zdrojovému úložišti dat.](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Vytvoří **nové** připojení k úložišti dat jímky, do kterého kopírujete data.

    ![Vytvoří nové připojení k úložišti dat jímky.](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Zobrazí se vytvořený kanál, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Pokud jste ve výše uvedeném **kroku 2** zvolili **Azure synapse Analytics (dřív SQL DW)** jako cíl dat, musíte pro přípravu zadat připojení k úložišti objektů BLOB v Azure, jak to vyžaduje základna Azure Synapse Analytics (dříve SQL Data Warehouse). Jak ukazuje následující snímek obrazovky, šablona automaticky vygeneruje cestu k *úložišti* pro úložiště objektů BLOB. Ověřte, zda byl kontejner vytvořen po spuštění kanálu.
        
    ![Základní nastavení](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Vyberte **ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na * * ladit * *.](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Po úspěšném dokončení kanálu se zobrazí podobné výsledky jako v následujícím příkladu:

    ![Kontrola výsledku](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Data Factory](introduction.md)