---
title: Seznamte se s datovými formáty Apache Spark pro vývojáře U-SQL Azure Data Lake Analytics.
description: Tento článek popisuje koncepty Apache Spark, které pomáhají U_SQL vývojářům pochopit rozdíly mezi datovými formáty U-SQL a Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648438"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Vysvětlení rozdílů mezi datovými formáty U-SQL a Spark

Pokud chcete použít [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) nebo [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), doporučujeme migrovat data z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Kromě přesouvání souborů budete chtít, aby vaše data uložená v tabulkách U-SQL byla přístupná i Sparku.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Přesunutí dat uložených v souborech Azure Data Lake Storage Gen1

Data uložená v souborech lze přesouvat různými způsoby:

- Napište kanál [Azure Data Factory](../data-factory/introduction.md) pro kopírování dat z účtu Azure Data Lake Storage [Gen1](../data-lake-store/data-lake-store-overview.md) do účtu [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Napište úlohu Spark, která čte data z účtu [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) a zapíše ji do účtu Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md) Na základě případu použití můžete chtít napsat v jiném formátu, jako je parketa, pokud nepotřebujete zachovat původní formát souboru.

Doporučujeme, abyste si prostudovali článek [Upgradujte řešení analýzy velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Přesunutí dat uložených v tabulkách U-SQL

U-SQL tabulky nejsou chápány Spark. Pokud máte data uložená v tabulkách U-SQL, spustíte úlohu U-SQL, která extrahuje data tabulky a uloží je ve formátu, kterému Spark rozumí. Nejvhodnějším formátem je vytvoření sady parketových souborů po rozložení složek metastore Hive.

Výstup lze dosáhnout v U-SQL s vestavěným výstupem parket a pomocí dynamického výstupního dělení se sadami souborů k vytvoření složek oddílů. [Zpracovat více souborů než kdy předtím a použití parket](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) je příkladem toho, jak vytvořit taková data spotřebního materiálu Spark.

Po této transformaci zkopírujete data, jak je uvedeno v kapitole [Přesunout data uložená v souborech Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Upozornění

- Sémantiku dat Při kopírování souborů dojde ke kopírování na úrovni bajtů. Takže stejná data by se měla objevit v účtu [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Všimněte si však, Spark může interpretovat některé znaky odlišně. Může například použít jiné výchozí nastavení pro oddělovač řádků v souboru CSV.
    Navíc pokud kopírujete zadaný data (z tabulek), pak parkety a Spark může mít různé přesnosti a škálování pro některé zadané hodnoty (například float) a může zacházet s hodnotami null odlišně. Například U-SQL má c# sémantiku pro nulové hodnoty, zatímco Spark má logiku se třemi hodnotami pro hodnoty null.

- Datové organizace (dělení) U-SQL tabulky poskytují dvě úrovně dělení. Vnější úroveň`PARTITIONED BY`( ) je podle hodnoty a mapuje většinou do hive/spark partitioning schéma pomocí hierarchie složek. Budete muset zajistit, že hodnoty null jsou mapovány na správnou složku. Vnitřní úroveň`DISTRIBUTED BY`( ) v U-SQL nabízí 4 distribuční schémata: kruhové dotazování, rozsah, hash a přímé hash.
    Tabulky Hive/Spark podporují pouze dělení hodnot nebo mlácení hash pomocí jiné funkce hash než U-SQL. Při výstupu dat tabulky U-SQL, budete pravděpodobně moci mapovat pouze do dělení hodnoty pro Spark a může být nutné provést další ladění rozložení dat v závislosti na konečné dotazy Spark.

## <a name="next-steps"></a>Další kroky

- [Principy konceptů kódu Spark pro vývojáře U-SQL](understand-spark-code-concepts.md)
- [Upgradujte svá řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformace dat pomocí aktivity Spark v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformace dat pomocí aktivity Hadoop Hive v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
