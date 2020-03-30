---
title: Hromadná kopie z databáze pomocí ovládací tabulky
description: Zjistěte, jak pomocí šablony řešení kopírovat hromadná data z databáze pomocí tabulky externího ovládacího prvku k uložení seznamu oddílů zdrojových tabulek pomocí Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439923"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Hromadná kopie z databáze s řídicí tabulkou

Chcete-li zkopírovat data z datového skladu v Oracle Server, Netezza, Teradata nebo SQL Server do Azure SQL Data Warehouse, musíte načíst obrovské množství dat z více tabulek. Obvykle musí být data rozdělena do oddílů v každé tabulce, takže můžete načíst řádky s více vlákny paralelně z jedné tabulky. Tento článek popisuje šablonu, která se má použít v těchto scénářích.

 >! Poznámka: Pokud chcete kopírovat data z malého počtu tabulek s relativně malým datovým svazkem do sql data warehouse, je efektivnější použít [nástroj Azure Data Factory Copy Data .](copy-data-tool.md) Šablona, která je popsána v tomto článku je více, než potřebujete pro tento scénář.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona načte seznam oddílů zdrojové databáze ke kopírování z tabulky externího ovládacího prvku. Pak iterates přes každý oddíl ve zdrojové databázi a zkopíruje data do cíle.

Šablona obsahuje tři aktivity:
- **Vyhledávání načte** seznam sure databázových oddílů z tabulky externího ovládacího prvku.
- **ForEach** získá seznam oddílů z aktivity vyhledávání a itetuje každý oddíl na aktivitu Copy.
- **Zkopírujte** kopie každého oddílu ze zdrojového úložiště databáze do cílového úložiště.

Šablona definuje následující parametry:
- *Control_Table_Name* je externí řídicí tabulka, která ukládá seznam oddílů pro zdrojovou databázi.
- *Control_Table_Schema_PartitionID* je název sloupce v tabulce externího ovládacího prvku, ve které jsou uloženy id každého oddílu. Ujistěte se, že ID oddílu je jedinečný pro každý oddíl ve zdrojové databázi.
- *Control_Table_Schema_SourceTableName* je externí řídicí tabulka, ve které jsou uloženy názvy jednotlivých tabulek ze zdrojové databáze.
- *Control_Table_Schema_FilterQuery* je název sloupce v tabulce externího ovládacího prvku, který ukládá dotaz filtru pro získání dat z každého oddílu ve zdrojové databázi. Například pokud jste rozdělili data podle roku, dotaz, který je uložen v každém řádku může být podobný 'select * z datasource where LastModifytime >= ''2015-01-01 00:00:00'' a LastModifytime <= ''2015-12-31 23:59:59.999''.
- *Data_Destination_Folder_Path* je cesta, kde jsou data zkopírována do cílového úložiště (platí v případě, že cíl, který zvolíte, je "Systém souborů" nebo "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* je cesta kořenové složky, do které se data zkopírují v cílovém úložišti. 
- *Data_Destination_Directory* je cesta k adresáři pod kořenem, kde jsou data zkopírována do cílového úložiště. 

Poslední tři parametry, které definují cestu v cílovém úložišti, jsou viditelné pouze v případě, že zvolený cíl je úložiště založené na souborech. Pokud zvolíte "Azure Synapse Analytics (dříve SQL DW)" jako cílové úložiště, tyto parametry nejsou vyžadovány. Ale názvy tabulek a schéma v databázi SQL Data Warehouse musí být stejné jako ty ve zdrojové databázi.

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Vytvořte řídicí tabulku v SQL Serveru nebo Azure SQL Database pro uložení seznamu oddílů zdrojové databáze pro hromadnou kopii. V následujícím příkladu je pět oddílů ve zdrojové databázi. Tři oddíly jsou pro *datasource_table*a dva jsou pro *project_table*. Sloupec *LastModifytime* se používá k rozdělení dat v tabulce *datasource_table* ze zdrojové databáze. Dotaz, který se používá ke čtení prvního oddílu je 'select * z datasource_table kde LastModifytime >= ''2015-01-01 00:00:00'' a LastModifytime <= ''2015-12-31 23:59:59.999''. Podobný dotaz můžete použít ke čtení dat z jiných oddílů.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Přejděte do šablony **Hromadná kopie z databáze.** Vytvořte **nové** připojení k tabulce externího ovládacího prvku, kterou jste vytvořili v kroku 1.

    ![Vytvoření nového připojení k řídicí tabulce](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Vytvořte **nové** připojení ke zdrojové databázi, ze které kopírujete data.

    ![Vytvoření nového připojení ke zdrojové databázi](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Vytvořte **nové** připojení k cílovému úložišti dat, do kterého data kopírujete.

    ![Vytvoření nového připojení k cílovému úložišti](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Vyberte **Použít tuto šablonu**.

6. Zobrazí se kanál, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na **Ladění**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zobrazí se výsledky, které jsou podobné v následujícím příkladu:

    ![Zkontrolujte výsledek](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Nepovinné) Pokud jste jako cíl dat zvolili "Azure Synapse Analytics (dříve SQL DW)", musíte zadat připojení k úložišti objektů blob Azure pro pracovní, jak to vyžaduje SQL Data Warehouse Polybase. Šablona automaticky vygeneruje cestu kontejneru pro úložiště objektů Blob. Zkontrolujte, zda byl kontejner vytvořen po spuštění kanálu.
    
    ![Polybase, nastavení](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
