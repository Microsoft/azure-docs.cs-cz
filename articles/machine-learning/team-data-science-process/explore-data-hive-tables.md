---
title: Prozkoumejte data v tabulkách Hive pomocí dotazů Hive | Microsoft Docs
description: Prozkoumejte data v tabulkách Hive pomocí dotazů Hive.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: e85530e5297618b9e87f46a5a274621b060fe1fc
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Zkoumání dat v tabulkách Hivu pomocí dotazů Hivu
Tento dokument obsahuje ukázkové skripty Hive, které se používají k zkoumat data v tabulkách Hive v clusteru HDInsight Hadoop.

Následující **nabídky** odkazy na témata, které popisují, jak používat nástroje a prozkoumejte data z různých prostředích úložiště.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořit účet úložiště Azure. Pokud budete potřebovat pokyny, najdete v části [vytvoření účtu úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Zřizuje přizpůsobené clusteru Hadoop se službou HDInsight. Pokud budete potřebovat pokyny, najdete v části [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilé analýzy](customize-hadoop-cluster.md).
* Data byla uložena do tabulek Hive v Azure HDInsight Hadoop clusterů. Pokud ne, postupujte podle pokynů v [vytvoření a načtení dat do tabulek Hive](move-hive-tables.md) nejprve nahrát data do tabulek Hive.
* Povolit pro vzdálený přístup ke clusteru. Pokud budete potřebovat pokyny, najdete v části [přístup hlavního uzlu Hadoop clusteru](customize-hadoop-cluster.md).
* Pokud budete potřebovat pokyny k odesílání dotazů Hive, najdete v části [postup odesílání dotazů Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Příklad Hive dotaz skripty pro zkoumání dat
1. Získat počet připomínky na oddíly  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Získat počet připomínky za den  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Získat úrovně ve sloupci kategorií  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Získat počet úrovní v kombinaci dvou kategorií sloupců  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Získat distribuce pro číselné sloupce  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrahování záznamů z spojení dvou tabulek
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Další dotaz skripty pro scénáře datových taxíkem cesty
Příklady dotazů, které jsou specifické pro [NYC taxíkem cestě Data](http://chriswhong.com/open-data/foil_nyc_taxi/) scénáře jsou také uvedeny v [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy už máte zadané schéma data a jsou připravené k odeslání do spustit.

