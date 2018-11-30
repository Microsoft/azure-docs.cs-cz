---
title: Zkoumání dat v tabulkách Hivu pomocí dotazů Hive | Dokumentace Microsoftu
description: Zkoumání dat v tabulkách Hivu pomocí dotazů Hive.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: c50c6de28c60bbe6a6c0d510e62f35cd037d6e95
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443308"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Zkoumání dat v tabulkách Hivu pomocí dotazů Hivu

Tento článek obsahuje ukázkové skripty Hive, které slouží ke zkoumání dat v tabulkách Hivu v clusteru HDInsight Hadoop.

Tato úloha je nějaký krok [vědecké zpracování týmových dat](overview.md).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete pokyny, přečtěte si [vytvoření účtu služby Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Zřídit vlastní cluster Hadoop ve službě HDInsight. Pokud potřebujete získat pokyny, přečtěte si téma [přizpůsobení clusterů Azure HDInsight pro pokročilé analýzy Hadoop](customize-hadoop-cluster.md).
* Data se odeslal do tabulek Hive v clusterech Azure HDInsight Hadoop. Pokud ne, postupujte podle pokynů v [vytvoření a načtení dat do tabulek Hive](move-hive-tables.md) nejdřív odesílat data do tabulek Hive.
* Povolit vzdálený přístup ke clusteru. Pokud potřebujete získat pokyny, přečtěte si téma [přístup k hlavní uzel z clusteru Hadoop](customize-hadoop-cluster.md).
* Pokud potřebujete získat pokyny o tom, jak odesílání dotazů Hive, přečtěte si téma [způsob odesílání dotazů Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Příklady skriptů pro dotaz Hive pro zkoumání dat
1. Získat počet připomínky na oddíl  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Získat počet vyjádření za den  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Získání úrovně ve sloupci zařazené do kategorií  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Získat počet úrovní v kombinaci dva sloupce a bude zařazené do kategorií  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Získání distribuce pro číselné sloupce  
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Další dotaz skripty pro scénáře dat o jízdách taxislužby města
Příklady dotazů, které jsou specifické pro [Data o jízdách taxislužby NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) scénáře jsou také uvedeny ve [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tyto dotazy už mají zadáno schéma dat a jsou připravené k odeslání ke spuštění.

