---
title: Používat ke čtení a zápisu dat HBase - Azure HDInsight Spark | Microsoft Docs
description: Pomocí konektoru HBase Spark ke čtení a zápisu dat z clusteru Spark pro HBase cluster.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 7cfc7f586e8a92c29736a7c4cff0b12796be430a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Použití Sparku ke čtení a zápisu dat HBase

Apache HBase je dotazován obvykle s jeho nízké úrovně rozhraní API (kontroly, získá a PUT) nebo s použitím Phoenix syntaxí SQL. Apache také poskytuje Spark HBase konektor, který je pohodlnou a alternativní původce pro dotazování a upravit data ukládaná HBase.

## <a name="prerequisites"></a>Požadavky

* Dva samostatné clustery HDInsight, HBase jeden a jeden Spark s 2.1 Spark (HDInsight 3.6) nainstalována.
* Spark cluster musí komunikovat přímo s clusterem HBase s minimální latence, tak doporučené konfigurace je nasazení obou clusterů ve stejné virtuální síti. Další informace najdete v tématu [vytvořit systémem Linux clusterů v HDInsight pomocí portálu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* SSH přístup k každý cluster.
* Přístup k úložišti výchozí každý cluster.

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně pro povolení váš cluster Spark dotazovat clusteru HDInsight je následující:

1. Příprava ukázkových dat v HBase.
2. Získejte hbase-site.xml soubor ze složky Konfigurace clusteru HBase (/ etc/hbase/conf).
3. Umístěte kopii hbase-site.xml ve složce Konfigurace Spark 2 (/ etc/spark2/conf).
4. Spustit `spark-shell` odkazující na konektor Spark HBase pomocí jeho Maven koordinuje v `packages` možnost.
5. Definujte katalog, který mapuje schéma z Spark tak, aby HBase.
6. Pracovat s daty HBase pomocí RDD nebo DataFrame rozhraní API.

## <a name="prepare-sample-data-in-hbase"></a>Příprava ukázkových dat v HBase

V tomto kroku vytvoříte a naplnit jednoduché tabulky v HBase, který pak můžete dotazovat pomocí Spark.

1. Připojení k hlavnímu uzlu clusteru HBase pomocí protokolu SSH. Další informace najdete v tématu [připojení k HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Spusťte prostředí HBase:

        hbase shell

3. Vytvoření `Contacts` tabulku s rodinami sloupců `Personal` a `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Načte několik ukázkových řádků dat:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Získání hbase-site.xml z clusteru HBase

1. Připojení k hlavnímu uzlu clusteru HBase pomocí protokolu SSH.
2. Zkopírujte hbase-site.xml z místního úložiště do kořenového úložiště výchozí HBase cluster:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Přejděte do vašeho clusteru HBase pomocí [portál Azure](https://portal.azure.com).
4. Vyberte účty úložiště. 

    ![Účty úložiště](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Vyberte v seznamu, který má zaškrtnutí ve sloupci výchozí účet úložiště.

    ![Výchozí účet úložiště](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. V podokně účet úložiště vyberte dlaždici objekty BLOB.

    ![Objekty BLOB dlaždice](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. V seznamu kontejnerů vyberte kontejner, který je používán HBase cluster.
8. V seznamu souborů vyberte `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. Na panelu Vlastnosti objektů Blob, vyberte možnost stáhněte a uložte `hbase-site.xml` do umístění na místním počítači.

    ![Ke stažení](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umístí hbase-site.xml váš cluster Spark

1. Přejděte do vašeho clusteru Spark pomocí [portál Azure](https://portal.azure.com).
2. Vyberte účty úložiště.

    ![Účty úložiště](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Vyberte v seznamu, který má zaškrtnutí ve sloupci výchozí účet úložiště.

    ![Výchozí účet úložiště](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. V podokně účet úložiště vyberte dlaždici objekty BLOB.

    ![Objekty BLOB dlaždice](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. V seznamu kontejnerů vyberte kontejner, který je používán váš cluster Spark.
6. Vyberte možnost odeslat.

    ![Odeslat](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Vyberte `hbase-site.xml` souborů, které jste předtím stáhli do místního počítače.

    ![Nahrát hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Vyberte možnost odeslat.
9. Připojení k hlavnímu uzlu clusteru Spark pomocí protokolu SSH.
10. Kopírování `hbase-site.xml` z clusteru Spark výchozí úložiště do složky Konfigurace Spark 2 v místním úložišti clusteru:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spusťte prostředí Spark odkazující na konektor Spark HBase

1. Připojení k hlavnímu uzlu clusteru Spark pomocí protokolu SSH.
2. Spusťte prostředí spark, určení balíčků HBase konektor Spark:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/coroups/public/

3. Tuto instanci Spark prostředí nechat otevřený a pokračovat k dalšímu kroku.

## <a name="define-a-catalog-and-query"></a>Zadejte katalogu a dotaz

V tomto kroku definujete katalogu objekt, který mapuje schéma z Spark tak, aby HBase. 

1. V otevřené Spark prostředí, spusťte následující `import` příkazy:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Definujte katalog pro tabulku kontaktů, kterou jste vytvořili v HBase:
    1. Definovat schéma katalogu pro tabulku HBase s názvem `Contacts`.
    2. Identifikovat rowkey jako `key`a mapuje názvy sloupců pomocí v Spark rodin sloupců, název sloupce a typ sloupce v rámci HBase.
    3. Také je nutné definovat podrobně jako sloupec s názvem rowkey (`rowkey`), na kterém je rodina konkrétní sloupec `cf` z `rowkey`.

            def catalog = s"""{
                |"table":{"namespace":"default", "name":"Contacts"},
                |"rowkey":"key",
                |"columns":{
                |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
                |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
                |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
                |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
                |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
                |}
            |}""".stripMargin

3. Zadejte metodu, která poskytuje DataFrame okolo vaší `Contacts` tabulky v HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Vytvoření instance DataFrame:

        val df = withCatalog(catalog)

5. Dotaz DataFrame:

        df.show()

6. Měli byste vidět dva řádky dat:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zaregistrujte dočasné tabulky, můžete dát dotaz na tabulku HBase pomocí Spark SQL:

        df.registerTempTable("contacts")

8. Vydat dotaz SQL na `contacts` tabulky:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Měli byste vidět výsledky, jako jsou tyto:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Vkládat nová data

1. Chcete-li vložit nový záznam kontaktu, definovat `ContactRecord` třídy:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Vytvoření instance `ContactRecord` a umístí jej do pole:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Uložte pole nová data do HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Podívejte se na výsledky:
    
        df.show()

5. Měl by se zobrazit výstup podobný tomuto:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Další postup

* [Spark HBase konektoru](https://github.com/hortonworks-spark/shc)
