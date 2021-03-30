---
title: Pochopení Apache Spark datových formátů pro Azure Data Lake Analytics vývojářů U-SQL.
description: Tento článek popisuje Apache Spark koncepty, které U_SQL vývojářům porozumět rozdílům mezi datovými formáty U-SQL a Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221107"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Vysvětlení rozdílů mezi datovými formáty U-SQL a Spark

Pokud chcete použít [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) nebo [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), doporučujeme migrovat data z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) na [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Kromě přesunu souborů budete také chtít vytvořit data uložená v tabulkách U-SQL, která jsou dostupná pro Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Přesun dat uložených v souborech Azure Data Lake Storage Gen1

Data uložená v souborech lze přesouvat různými způsoby:

- Napíšete kanál [Azure Data Factory](../data-factory/introduction.md) ke zkopírování dat z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) účtu do účtu [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) .
- Napište úlohu Sparku, která načte data z účtu [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) a zapíše ji do účtu [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . V závislosti na vašem případu použití ho můžete chtít zapsat v jiném formátu, jako je například Parquet, pokud nepotřebujete zachovat formát původního souboru.

Doporučujeme vám projít si článek [Upgrade řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Přesun dat uložených v tabulkách U-SQL

Pomocí Sparku nerozumíme tabulkám U-SQL. Pokud máte data uložená v tabulkách U-SQL, spustíte úlohu U-SQL, která extrahuje data tabulky a uloží je ve formátu, který Spark zná. Nejvhodnější formát je vytvořit sadu souborů Parquet po rozložení složky metastore Hive.

Výstup je možné dosáhnout v U-SQL s integrovaným Parquetm pro výstup a použití dynamického výstupu do oddílů se sadami souborů k vytvoření složek oddílu. [Zpracování více souborů než kdy dřív a použití Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) poskytuje příklad, jak vytvořit taková spotřební data Sparku.

Po této transformaci můžete zkopírovat data, jak je popsáno v kapitole [přesun dat uložených v Azure Data Lake Storage Gen1 souborech](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Upozornění

- Sémantika dat při kopírování souborů dojde k kopírování na úrovni bajtů. Takže by se stejná data měla zobrazovat v účtu [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . Poznámka: Spark ale může interpretovat některé znaky odlišně. Například může použít jinou výchozí hodnotu pro oddělovač řádků v souboru CSV.
    Kromě toho, pokud kopírujete zadaná data (z tabulek), pak Parquet a Spark mohou mít různou přesnost a měřítko pro některé ze zadaných hodnot (například float) a mohou považovat hodnoty null jinak. Například U-SQL má sémantiku jazyka C# pro hodnoty null, zatímco Spark má pro hodnoty null logiku se třemi hodnotami.

- Datové organizace (dělení) tabulek U-SQL poskytují dva oddíly na úrovni. Vnější úroveň ( `PARTITIONED BY` ) je podle hodnoty a mapuje se většinou do schématu dělení na oddíly/Spark pomocí hierarchií složek. Bude nutné zajistit, aby hodnoty null byly namapovány na správnou složku. Vnitřní úroveň ( `DISTRIBUTED BY` ) v u-SQL nabízí 4 distribuční schémata: kruhové dotazování, rozsah, hodnota hash a přímá hodnota hash.
    Tabulky podregistru/Spark podporují pouze dělení hodnot nebo dělení hodnot hash s použitím jiné funkce hash než U-SQL. Když vytváříte výstup dat tabulky U-SQL, pravděpodobně bude možné mapovat pouze na dělení hodnoty pro Spark a může být nutné provést další ladění rozložení dat v závislosti na finálních dotazech Sparku.

## <a name="next-steps"></a>Další kroky

- [Principy konceptů kódu Spark pro vývojáře U-SQL](understand-spark-code-concepts.md)
- [Upgradujte řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET pro Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformuje data pomocí aktivity Sparku v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformuje data pomocí aktivity podregistru Hadoop v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)