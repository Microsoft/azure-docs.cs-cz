---
title: Prozkoumejte data v tabulkách Hive s dotazy Hive – Team Data Science Process
description: Použijte ukázkové hive skripty, které se používají k prozkoumání dat v tabulkách Hive v clusteru HDInsight Hadoop.
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
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722165"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Zkoumání dat v tabulkách Hivu pomocí dotazů Hivu

Tento článek obsahuje ukázkové hive skripty, které se používají k prozkoumání dat v tabulkách Hive v clusteru HDInsight Hadoop.

Tento úkol je krokem v [procesu vědecké vědy o týmových datech](overview.md).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořil účet úložiště Azure. Pokud potřebujete pokyny, přečtěte [si tématy Vytvoření účtu Azure Storage.](../../storage/common/storage-account-create.md)
* Zřídit vlastní cluster Hadoop se službou HDInsight. Pokud potřebujete pokyny, [přečtěte si informace o přizpůsobení clusterů Azure HDInsight Hadoop pro pokročilou analýzu](customize-hadoop-cluster.md).
* Data byla nahrána do tabulek Hive v clusterech Azure HDInsight Hadoop. Pokud tomu tak není, postupujte podle pokynů v [vytvořit a načíst data do tabulek Hive](move-hive-tables.md) a nejprve nahrát data do tabulek Hive.
* Povolený vzdálený přístup ke clusteru. Pokud potřebujete pokyny, přečtěte si [část Přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).
* Pokud potřebujete pokyny k odesílání dotazů hive, přečtěte si informace [o tom, jak odeslat dotazy hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Příklad skriptů dotazu Hive pro zkoumání dat
1. Získání počtu pozorování na oddíl`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Získejte počet pozorování za den`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Získání úrovní v kategorickém sloupci  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Získání počtu úrovní v kombinaci dvou kategorických sloupců`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Získání distribuce pro číselné sloupce  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrahování záznamů ze spojení dvou tabulek
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Další skripty dotazů pro scénáře dat o cestě taxíkem
Příklady dotazů, které jsou specifické pro scénáře [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) jsou také k dispozici v [úložišti GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy již mají schéma dat zadané a jsou připraveny ke spuštění.

