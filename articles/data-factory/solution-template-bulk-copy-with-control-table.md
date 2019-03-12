---
title: Hromadné kopírování z databáze pomocí ovládacího prvku tabulky s Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak zkopírovat hromadných dat z databáze pomocí tabulku externího ovládacího prvku k uložení seznamu oddílů zdrojové tabulky pomocí Azure Data Factory pomocí šablony řešení.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530565"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Hromadné kopírování z databáze s tabulkou ovládacího prvku

Ke zkopírování dat z datového skladu na serveru Oracle, Netezza, Teradata nebo SQL Server do Azure SQL Data Warehouse, je nutné načíst obrovské objemy dat z více tabulek. Data se obvykle mají k rozdělení na oddíly v každé tabulce, aby mohl načíst řádky s více vlákny paralelně z jedné tabulky. Tento článek popisuje šablonu pro použití v těchto scénářích.

 >! Poznámka: Pokud chcete ke zkopírování dat z malém počtu tabulek s poměrně málo početnému datový svazek do služby SQL Data Warehouse, je výhodnější používat [nástroj pro kopírování dat Azure Data Factory](copy-data-tool.md). Šablony, který je popsaný v tomto článku je vyšší, než budete potřebovat pro tento scénář.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona načte seznam zdrojových oddílů databáze zkopírovat z externího ovládacího prvku tabulky. Pak Iteruje přes každého oddílu ve zdrojové databázi a zkopíruje data do cíle.

Šablona obsahuje tři činnosti:
- **Vyhledávání** načte seznam objektů že oddíly databáze z externího ovládacího prvku tabulky.
- **ForEach** získá seznam oddílů z aktivity vyhledávání a iteruje jednotlivé oddíly do aktivity kopírování.
- **Kopírování** zkopíruje každý oddíl ze zdrojového úložiště databáze do cílového úložiště.

Šablona definuje pěti parametrů:
- *Control_Table_Name* externího ovládacího prvku tabulka, která ukládá seznam oddílů pro zdrojovou databázi.
- *Control_Table_Schema_PartitionID* je název název sloupce v tabulce externího ovládacího prvku, který ukládá každé ID oddílu. Ujistěte se, že ID oddílu je jedinečný pro každý oddíl ve zdrojové databázi.
- *Control_Table_Schema_SourceTableName* je externího ovládacího prvku tabulky, která ukládá názvy jednotlivých tabulek ze zdrojové databáze.
- *Control_Table_Schema_FilterQuery* je název sloupce v tabulce externího ovládacího prvku, která ukládá dotaz filter na získat data z každého oddílu ve zdrojové databázi. Například, pokud je rozdělená na oddíly data podle roku, dotaz, který je uložen v jednotlivých řádcích může být podobně jako "vybrat * ze zdroje dat kde LastModifytime > =" 2015-01-01 00:00:00 "a LastModifytime < ="2015-12-31 23:59:59.999'' '.
- *Data_Destination_Folder_Path* je cesta kde se data kopírují do cílového úložiště. Tento parametr je viditelné pouze pokud je cíl, který zvolíte souborové úložiště. Pokud se rozhodnete SQL Data Warehouse jako cílové úložiště, není tento parametr povinný. Ale názvy tabulek a schématu ve službě SQL Data Warehouse, musí být stejné jako ty, které ve zdrojové databázi.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Vytvoření ovládacího prvku tabulky v SQL Server nebo Azure SQL Database k uložení seznamu zdrojové databáze oddílů pro hromadné kopírování. V následujícím příkladu existují pěti oddílů ve zdrojové databázi. Tři oddíly jsou pro *datasource_table*, a dva jsou určené pro *tabulky project_table*. Sloupec *LastModifytime* se používá k rozdělení dat v tabulce *datasource_table* ze zdrojové databáze. Dotaz, který slouží k načtení první oddíl je "vybrat * z datasource_table kde LastModifytime > =" 2015-01-01 00:00:00 "a LastModifytime < ="2015-12-31 23:59:59.999'' '. Podobně jako dotaz můžete číst data z jiných oddílů.

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

2. Přejděte **hromadné kopírování z databáze** šablony. Vytvoření **nový** připojení k tabulce externího ovládacího prvku, který jste vytvořili v kroku 1.

    ![Vytvořit nové připojení k tabulce ovládacího prvku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Vytvoření **nový** připojení, která se kopírování dat ze zdrojové databáze.

     ![Vytvoření nového připojení ke zdrojové databázi](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Vytvoření **nový** připojení k datům cílového úložiště, že kopírujete data, která mají.

    ![Vytvořit nové připojení do cílového úložiště](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Vyberte **pomocí této šablony**.

    ![Použít tuto šablonu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Zobrazí se kanál, jak je znázorněno v následujícím příkladu:

    ![Zkontrolujte kanálu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Vyberte **ladění**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na ** ladění **](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zobrazí se výsledky, které jsou podobné jako v následujícím příkladu:

    ![Zkontrolujte výsledky](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Volitelné) Pokud jste zvolili SQL Data Warehouse jako cíle dat, musíte zadat připojení k úložišti objektů Blob v Azure pro fázování, podle potřeby pomocí Polybase služby SQL Data Warehouse. Ujistěte se, že se vytvořil už kontejneru v úložišti objektů Blob.
    
    ![Nastavení funkce Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)
