---
title: Hromadné kopírování z databáze s tabulkou ovládacího prvku s Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak plně hromadné kopírování dat z databáze pomocí externího ovládacího prvku tabulky k uložení seznamu oddílů zdrojové tabulky s Azure Data Factory pomocí šablony řešení.
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
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967189"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Hromadné kopírování databází pomocí ovládacího prvku tabulka

Pokud chcete zkopírovat data z vašeho datového skladu, jako je Oracle server, Netezza server, Teradata serveru nebo systému SQL Server do Azure, budete muset načíst obrovské množství dat z více tabulek v zdroje dat. Ve většině případů se data mají k rozdělení na další oddíly v každé tabulce tak, aby řádků s více vlákny současně můžete načíst z jedné tabulky. K dispozici šablona je určena pro tento případ. 

Pokud chcete ke zkopírování dat z malém počtu tabulek s malou velikost dat, je efektivnější pro přechod na "nástroj pro kopírování dat" pro jednu jedinou kopírováním aktivity nebo aktivita foreach a aktivita kopírování v kanálu. Tato šablona je vyšší, než budete potřebovat pro tento jednoduchý případ použití.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona načte seznam oddílů zdrojové databáze z externího ovládacího prvku tabulky, kterou je potřeba zkopírovat do cílového úložiště a pak Iteruje přes každý oddíl v databázi správy zdrojových a provádí operaci kopírování data.

Šablona obsahuje tři činnosti:
-   A **vyhledávání** aktivita pro načtení z externího ovládacího prvku tabulky v seznamu oddílů zdrojové databáze.
-   A **ForEach** aktivitu k získání seznamu oddílů z aktivita vyhledávání a potom iterovat každý z nich aktivitě kopírování.
-   A **kopírování** aktivity kopírování jednotlivých oddílů ze zdrojové databáze úložiště do cílového úložiště.

Šablona definuje pěti parametrů:
-   Parametr *Control_Table_Name* je název tabulky pro tabulku externího ovládacího prvku. Ovládací prvek tabulka slouží k uložení seznamu oddílů pro zdrojovou databázi.
-   Parametr *Control_Table_Schema_PartitionID* název sloupce v tabulce externího ovládacího prvku pro uložení ID každého oddílu. Zajistěte, aby že ID oddílu je jedinečný pro každý oddíl ve zdrojové databázi.
-   Parametr *Control_Table_Schema_SourceTableName* název sloupce v tabulce externího ovládacího prvku k uložení každý název tabulky ze zdrojové databáze.
-   Parametr *Control_Table_Schema_FilterQuery* název sloupce v tabulce externího ovládacího prvku k uložení dotaz filter na získat data z každého oddílu ve zdrojové databázi. Například pokud rozdělená na oddíly data podle každý rok dotazů uložená v jednotlivých řádcích může být podobná jako "vybrat * ze zdroje dat kde LastModifytime > =" 2015-01-01 00:00:00 "a LastModifytime < ="2015-12-31 23:59:59.999'' '
-   Parametr *Data_Destination_Folder_Path* je cesta ke složce, ve kterém se data kopírují do cílového úložiště.  Tento parametr je viditelná pouze při zvoleném cíli je úložištěm úložiště založeného na souboru.  Pokud se rozhodnete SQL Data Warehouse jako cílové úložiště, zde není žádný parametr potřeba zde zadané hodnoty. Ale názvy tabulek a schématu ve službě SQL data warehouse, musí být stejné jako ty, které ve zdrojové databázi.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Vytvoření ovládacího prvku tabulky v SQL server nebo SQL Azure k uložení seznamu oddílů zdrojové databáze pro hromadné kopírování.  V následujícím příkladu můžete zobrazit existují pěti oddílů ve zdrojové databázi, kde jsou tři oddíly pro jednu tabulku:*datasource_table* a dva oddíly, které jsou pro jiné tabulky:*tabulky project_table*. Sloupec *LastModifytime* se používá k rozdělení dat v tabulce *datasource_table* ze zdrojové databáze. Dotaz používá ke čtení první oddíl je "vybrat * z datasource_table kde LastModifytime > =" 2015-01-01 00:00:00 "a LastModifytime < ="2015-12-31 23:59:59.999"'.  Zobrazí se také podobné dotazu přečíst data z ostatních oddílů. 

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

2. Přejděte do šablony **hromadné kopírování z databáze**a vytvořit **nové připojení** do externího ovládacího prvku tabulky.  Toto připojení se připojuje k databázi, ve kterém jste vytvořili tabulku ovládacího prvku v kroku #1.

    ![Vytvořit nové připojení k tabulce ovládacího prvku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Vytvoření **nové připojení** k kde kopírování dat ze zdrojové databáze.

     ![Vytvoření nového připojení ke zdrojové databázi](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Vytvoření **nové připojení** na vaše cílové úložiště dat, ve kterém kopírování dat do.

    ![Vytvořit nové připojení do cílového úložiště](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Klikněte na tlačítko **pomocí této šablony**.

    ![Použít tuto šablonu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. K dispozici v panelu kanálu uvidíte, jak je znázorněno v následujícím příkladu:

    ![Zkontrolujte kanálu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Klikněte na tlačítko **ladění**, vstupní parametry a potom klikněte na tlačítko **dokončit**

    ![Klikněte na tlačítko ladění](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. K dispozici na panelu výsledků uvidíte, jak je znázorněno v následujícím příkladu:

    ![Zkontrolujte výsledky](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Volitelné) Pokud vyberete jako cíl dat SQL Data Warehouse, musíte také vstupní připojení služby Azure blob storage jako pracovní, která vyžaduje SQL Data Warehouse Polybase.  Zajistěte, aby že kontejneru ve službě blob storage už vytvořil.  
    
    ![Nastavení funkce Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)