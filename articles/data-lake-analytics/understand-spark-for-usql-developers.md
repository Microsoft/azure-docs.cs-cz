---
title: Seznamte se s Apache Spark pro vývojáře Azure Data Lake Analytics U-SQL.
description: Tento článek popisuje koncepty Apache Spark, které vám pomohou rozdíly mezi vývojáři U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648425"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Vysvětlení Apache Sparku pro vývojáře v U-SQL

Microsoft podporuje několik služeb Analytics, jako jsou [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) a [Azure HDInsight,](../hdinsight/hdinsight-overview.md) stejně jako Azure Data Lake Analytics. Od vývojářů slyšíme, že při vytváření analytických kanálů mají jasnou preferenci open source řešení. Abychom vývojářům U-SQL pomohli pochopit Apache Spark a jak byste mohli transformovat své u-SQL skripty na Apache Spark, vytvořili jsme tyto pokyny.

Obsahuje řadu kroků, které můžete podniknout, a několik alternativ.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Kroky k transformaci U-SQL na Apache Spark

1. Transformujte kanály orchestrace úloh.

   Pokud používáte [Azure Data Factory](../data-factory/introduction.md) k orchestraci skriptů Azure Data Lake Analytics, budete je muset upravit, abyste mohli organizovat nové programy Spark.
2. Pochopení rozdílů mezi správou dat U-SQL a Spark

   Pokud chcete přesunout data z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md), budete muset zkopírovat data souboru i katalog udržovaná data. Všimněte si, že Azure Data Lake Analytics podporuje jenom Azure Data Lake Storage Gen1. Viz [Principy datových formátů Spark](understand-spark-data-formats.md)
3. Transformace skriptů U-SQL na Spark

   Před transformací skriptů U-SQL budete muset zvolit analytickou službu. Některé dostupné výpočetní služby, které jsou k dispozici, jsou:
      - [Tok dat Azure Data Factory](../data-factory/concepts-data-flow-overview.md) Mapování toků dat jsou vizuálně navržené transformace dat, které umožňují datovým inženýrům vyvinout logiku grafické transformace dat bez psaní kódu. I když nejsou vhodné pro spuštění komplexního uživatelského kódu, mohou snadno reprezentovat tradiční transformace toku dat podobných SQL.
      - [Podregistr Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive na HDInsight je vhodný pro operace extrakce, transformace a zatížení (ETL). To znamená, že budete překládat u-SQL skripty apache hive.
      - Apache Spark Engines, jako je [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) nebo [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) To znamená, že budete překládat u-SQL skripty na Spark. Další informace najdete [v tématu Principy datových formátů Spark](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) i [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) jsou clusterové služby a ne úlohy bez serveru, jako je Azure Data Lake Analytics. Budete muset zvážit, jak zřídit clustery získat odpovídající poměr nákladů a výkonu a jak spravovat jejich životnost minimalizovat náklady. Tyto služby mají různé charakteristiky výkonu s uživatelským kódem napsaným v rozhraní .NET, takže budete muset buď napsat obálky nebo přepsat kód v podporovaném jazyce. Další informace naleznete [v tématu Understand Spark data formats](understand-spark-data-formats.md), [Understand Apache Spark code concepts for U-SQL developers](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Další kroky

- [Principy datových formátů Spark pro vývojáře U-SQL](understand-spark-data-formats.md)
- [Principy konceptů kódu Spark pro vývojáře U-SQL](understand-spark-code-concepts.md)
- [Upgradujte svá řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformace dat pomocí aktivity Hadoop Hive v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformace dat pomocí aktivity Spark v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
