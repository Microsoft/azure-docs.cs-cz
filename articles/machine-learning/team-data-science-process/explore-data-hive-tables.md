---
title: Prozkoumat data v tabulkách podregistru pomocí dotazů na podregistr – vědecký proces týmového zpracování dat
description: Použijte ukázkové skripty podregistru, které se používají k prozkoumání dat v tabulkách podregistru v clusteru HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c0dfa06e2ece2ba4631c0d5681b066ab0134daba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085663"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Zkoumání dat v tabulkách Hivu pomocí dotazů Hivu

Tento článek popisuje ukázkové skripty v podregistru, které se používají k prozkoumávání dat v tabulkách podregistru v clusteru HDInsight Hadoop.

Tento úkol je krok v rámci [vědeckého procesu týmového zpracování dat](overview.md).

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte následující:

* Vytvořili jste účet úložiště Azure. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Byl zřízen přizpůsobený cluster Hadoop se službou HDInsight. Pokud potřebujete pokyny, přečtěte si téma [přizpůsobení Azure HDInsight Hadoop clusterů pro pokročilou analýzu](customize-hadoop-cluster.md).
* Data byla nahrána do tabulek podregistru v clusterech Azure HDInsight Hadoop. Pokud tomu tak není, postupujte podle pokynů v části [Vytvoření a načtení dat do tabulek podregistru](move-hive-tables.md) a nahrajte data do tabulek podregistru.
* Povolen vzdálený přístup ke clusteru. Pokud potřebujete pokyny, přečtěte si téma [přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).
* Pokud potřebujete pokyny k odeslání dotazů na podregistr, přečtěte si téma [odeslání dotazů na podregistr](move-hive-tables.md#submit) .

## <a name="example-hive-query-scripts-for-data-exploration"></a>Ukázkové skripty pro dotaz na podregistr pro zkoumání dat
1. Získá počet pozorování na oddíl.  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Získá počet pozorování za den.  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Získat úrovně ve sloupci kategorií  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Získá počet úrovní v kombinaci dvou kategorií sloupců.  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Získat distribuci pro číselné sloupce  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrakce záznamů z spojení dvou tabulek
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Další skripty pro dotazy pro scénáře taxislužby data Trip
Příklady dotazů, které jsou specifické pro scénáře [NYC dat TRIPS taxislužby](https://chriswhong.com/open-data/foil_nyc_taxi/) , jsou také k dispozici v [úložišti GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy již mají zadané schéma dat a jsou připraveny k odeslání ke spuštění.

