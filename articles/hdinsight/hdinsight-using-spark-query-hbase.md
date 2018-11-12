---
title: Použití Sparku ke čtení a zápisu dat HBase – Azure HDInsight
description: Konektor Spark HBase můžete číst a zapisovat data z clusteru Spark pro HBase cluster.
services: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 1136d97ad1c1d02320331e6d2013b68526d5abb4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277887"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Použití Sparku ke čtení a zápisu dat HBase

Apache HBase je obvykle dotazovat pomocí jejího nízké úrovně rozhraní API (kontrol, získá a vloží) nebo se syntaxí SQL pomocí Phoenix. Apache také poskytuje konektor Spark HBase, což je pohodlnou a výkonnější alternativou k dotazování a modifikaci dat uložených v HBase.

## <a name="prerequisites"></a>Požadavky

* Dva samostatné clustery HDInsight, jeden HBase a Spark jeden s Sparku 2.1 (HDInsight 3.6) nainstalovaný.
* Spark cluster potřebuje ke komunikaci přímo s clusterem HBase s minimální latencí, takže doporučenou konfiguraci nasazení obou clusterech ve stejné virtuální síti. Další informace najdete v tématu [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* Přístup přes SSH k každý cluster.
* Přístup k výchozím úložištěm každé clusteru.

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně pro povolení váš cluster Spark na HDInsight cluster dotazovat vypadá takto:

1. Příprava nějaká ukázková data v HBase.
2. Získejte hbase-site.xml soubor ze složky Konfigurace clusteru HBase (/ etc/hbase/potvrzení).
3. Ve složce Konfigurace Spark 2 (/ etc/spark2/conf), umístěte kopii hbase-site.xml.
4. Spustit `spark-shell` odkazující na konektoru Spark HBase pomocí jeho Maven v koordinuje `packages` možnost.
5. Definujte katalog, který mapuje schématu ze Spark HBase.
6. Práce s daty HBase pomocí RDD nebo rozhraní API datového rámce.

## <a name="prepare-sample-data-in-hbase"></a>Příprava ukázkových dat v HBase

V tomto kroku vytvoření a vyplnění jednoduché tabulky v HBase, který potom můžete dotazovat pomocí Sparku.

1. Připojení k hlavnímu uzlu clusteru HBase pomocí protokolu SSH. Další informace najdete v tématu [připojení k HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Spusťte prostředí HBase:

        hbase shell

3. Vytvoření `Contacts` tabulku s rodinami sloupců `Personal` a `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Načte několik řádků vzorku dat:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Získat hbase-site.xml z clusteru HBase

1. Připojení k hlavnímu uzlu clusteru HBase pomocí protokolu SSH.
2. Zkopírujte hbase-site.xml z místního úložiště do kořenového adresáře výchozí úložiště clusteru HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Přejděte do vašeho clusteru HBase pomocí [webu Azure portal](https://portal.azure.com).
4. Vyberte účty úložiště. 

    ![Účty úložiště](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Vyberte v seznamu uvedena značka zaškrtnutí ve sloupci výchozí účet úložiště.

    ![Výchozí účet úložiště](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. V podokně se účet úložiště vyberte dlaždici objekty BLOB.

    ![Objekty BLOB dlaždice](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. V seznamu kontejnerů vyberte kontejner, který je použit clusterem HBase.
8. Vyberte v seznamu souboru `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. Na panelu Vlastnosti objektu Blob, vyberte a stáhněte a uložte `hbase-site.xml` do umístění na místním počítači.

    ![Ke stažení](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Vložit hbase-site.xml na svém clusteru Spark

1. Přejděte k pomocí clusteru Spark [webu Azure portal](https://portal.azure.com).
2. Vyberte účty úložiště.

    ![Účty úložiště](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Vyberte v seznamu uvedena značka zaškrtnutí ve sloupci výchozí účet úložiště.

    ![Výchozí účet úložiště](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. V podokně se účet úložiště vyberte dlaždici objekty BLOB.

    ![Objekty BLOB dlaždice](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. V seznamu kontejnerů vyberte kontejner, který se používá ve svém clusteru Spark.
6. Výběr nahrání.

    ![Odeslat](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Vyberte `hbase-site.xml` jste dříve stáhli do místního počítače.

    ![Nahrát hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Výběr nahrání.
9. Připojení k hlavnímu uzlu clusteru Sparku pomocí protokolu SSH.
10. Kopírování `hbase-site.xml` z výchozí úložiště clusteru Spark ke složce Spark 2 konfigurace v místním úložišti clusteru:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spusťte prostředí Sparku odkazující na konektor Spark HBase

1. Připojení k hlavnímu uzlu clusteru Sparku pomocí protokolu SSH.
2. Spusťte prostředí sparku, určení balíčků konektor Spark HBase:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/content/groups/public/

3. Ponechat otevřené tuto instanci prostředí Spark a pokračovat k dalšímu kroku.

## <a name="define-a-catalog-and-query"></a>Zadejte katalogu a dotaz

V tomto kroku definujete objekt katalogu, který mapuje schématu ze Spark HBase. 

1. V otevřené prostředí Spark, spusťte následující příkaz `import` příkazy:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Definujte katalogu pro tabulky kontaktů, který jste vytvořili v HBase:
    1. Definovat schéma katalogu s názvem tabulky HBase `Contacts`.
    2. Identifikujte rowkey jako `key`a mapování názvů sloupce používané v rodině sloupců, název sloupce a typ sloupce v HBase Spark.
    3. Také musí být definováno v podrobnosti jako sloupec s názvem rowkey (`rowkey`), který má konkrétní sloupce řady `cf` z `rowkey`.

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

3. Definujte metodu, která poskytuje datový rámec kolem vašeho `Contacts` tabulky v HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Vytvoření instance datového rámce:

        val df = withCatalog(catalog)

5. Dotaz datového rámce:

        df.show()

6. Měli byste vidět dva řádky dat:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zaregistrujte dočasnou tabulku můžete zadat dotaz na tabulku HBase pomocí Spark SQL:

        df.registerTempTable("contacts")

8. Vydávání dotazů SQL proti `contacts` tabulky:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Měli byste vidět výsledky, jako jsou tyto:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Vložte nová data

1. Chcete-li vložit nový záznam kontaktu, definujte `ContactRecord` třídy:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Vytvoření instance `ContactRecord` a vložit ho do pole:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Pole nová data uložte do HBase:

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

* [Konektor Spark HBase](https://github.com/hortonworks-spark/shc)
