---
title: Pochopení Apache Spark pro Azure Data Lake Analytics vývojářům U-SQL
description: Tento článek popisuje Apache Spark konceptů, které vám pomůžou při rozdílech mezi vývojáři U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221090"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Vysvětlení Apache Sparku pro vývojáře v U-SQL

Microsoft podporuje několik analytických služeb, jako jsou [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) a [Azure HDInsight](../hdinsight/hdinsight-overview.md) , a také Azure Data Lake Analytics. Od vývojářů uslyšíme, že mají při vytváření kanálů analýz k dispozici jasné předvolby pro open source řešení. Aby mohli vývojáři U-SQL pochopit Apache Spark a jak byste mohli transformovat skripty U-SQL na Apache Spark, vytvořili jsme tento návod.

Zahrnuje řadu kroků, které můžete provést, a několik alternativ.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Postup transformace U-SQL na Apache Spark

1. Transformujte kanály orchestrace úloh.

   Pokud k orchestraci Azure Data Lake Analytics skriptů použijete [Azure Data Factory](../data-factory/introduction.md) , budete je muset upravit a orchestrovat nové programy Spark.
2. Vysvětlení rozdílů mezi tím, jak se spravují data U-SQL a Sparku

   Chcete-li přesunout data z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md), budete muset zkopírovat data souborů i katalog uchovávající data. Upozorňujeme, že Azure Data Lake Analytics podporuje jenom Azure Data Lake Storage Gen1. Viz [vysvětlení formátů dat Spark](understand-spark-data-formats.md)
3. Transformace skriptů U-SQL na Spark

   Než transformují vaše skripty U-SQL, budete muset vybrat analytickou službu. K dispozici jsou tyto dostupné výpočetní služby:
      - [Azure Data Factory tok](../data-factory/concepts-data-flow-overview.md) dat Mapování toků dat je vizuálně navržené transformace dat, které umožňují datovým technikům vyvíjet grafickou logiku transformace dat bez psaní kódu. I když nejsou vhodné ke spouštění komplexního uživatelského kódu, můžou snadno představovat tradiční transformace toku dat typu SQL.
      - [Podregistr Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive ve službě HDInsight je vhodné k extrakci, transformaci a načítání (ETL) operací. To znamená, že budete překládat skripty U-SQL na Apache Hive.
      - Apache Spark stroje jako [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) nebo [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) to znamená, že budete překládat své skripty U-SQL do Sparku. Další informace najdete v tématu [Principy formátů dat Spark](understand-spark-data-formats.md) .

> [!CAUTION]
> [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) i [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) jsou Clusterové služby a nejedná se o úlohy bez serveru, jako je Azure Data Lake Analytics. Budete muset zvážit, jak zřídit clustery, abyste získali příslušný poměr nákladů a výkonu a jak spravovat jejich životnost, abyste minimalizovali náklady. Tyto služby mají různé charakteristiky výkonu s uživatelským kódem napsaným v .NET, takže budete muset napsat obálky nebo přepsat kód v podporovaném jazyce. Další informace najdete v tématu [Principy formátů dat Spark](understand-spark-data-formats.md), [Principy Apache Spark koncepcí kódu pro vývojáře u-SQL](understand-spark-code-concepts.md), [.NET pro Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Další kroky

- [Principy formátů dat Spark pro vývojáře U-SQL](understand-spark-data-formats.md)
- [Principy konceptů kódu Spark pro vývojáře U-SQL](understand-spark-code-concepts.md)
- [Upgradujte řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET pro Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformuje data pomocí aktivity podregistru Hadoop v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformuje data pomocí aktivity Sparku v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)