---
title: Čtení a zápis dat HBase pomocí Spark – Azure HDInsight
description: Pomocí konektoru Spark HBase můžete číst a zapisovat data z clusteru Spark do clusteru HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623092"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Použití Apache Sparku ke čtení a zápisu dat Apache HBase

Apache HBase je obvykle dotazován buď s jeho low-level API (skenuje, dostane a staví) nebo se syntaxí SQL pomocí Apache Phoenix. Apache také poskytuje Apache Spark HBase Connector, což je pohodlná a výkonná alternativa k dotazování a úpravám dat uložených společností HBase.

## <a name="prerequisites"></a>Požadavky

* Dva samostatné clustery HDInsight nasazené ve stejné [virtuální síti](./hdinsight-plan-virtual-network-deployment.md). Jedna HBase a jedna Spark s nainstalovanou alespoň Spark 2.1 (HDInsight 3.6). Další informace najdete [v tématu Vytváření clusterů založených na Linuxu ve službě HDInsight pomocí portálu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. Toto schéma by bylo wasb:// pro Azure Blob Storage, abfs:// pro Azure Data Lake Storage Gen2 nebo adl:// pro Azure Data Lake Storage Gen1. Pokud je pro úložiště objektů BLOB povolen `wasbs://`zabezpečený přenos, identifikátor URI bude .  Viz také [bezpečný přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně pro povolení clusteru Spark k dotazování clusteru HDInsight je následující:

1. Připravte některá ukázková data v HBase.
2. Získejte soubor hbase-site.xml z konfigurační složky clusteru HBase (/etc/hbase/conf).
3. Umístěte kopii souboru hbase-site.xml do konfigurační složky Spark 2 (/etc/spark2/conf).
4. Spusťte `spark-shell` odkazování na konektor Spark HBase podle `packages` jeho souřadnic Maven v možnosti.
5. Definujte katalog, který mapuje schéma ze Spark u HBase.
6. Pracujte s daty HBase pomocí vyvd nebo dataframe API.

## <a name="prepare-sample-data-in-apache-hbase"></a>Příprava ukázkových dat v Apache HBase

V tomto kroku vytvoříte a naplníte tabulku v Apache HBase, kterou pak můžete dotazovat pomocí Spark.

1. Pomocí `ssh` příkazu se připojte ke clusteru HBase. Upravte níže uvedený `HBASECLUSTER` příkaz nahrazením názvem clusteru HBase a zadejte příkaz:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí HBase. V připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

3. Pomocí `create` příkazu vytvořte tabulku HBase s rodinami se dvěma sloupci. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Pomocí `put` příkazu vkládejte hodnoty do zadaného sloupce v zadaném řádku v určité tabulce. Zadejte následující příkaz:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Pomocí `exit` příkazu zastavte interaktivní prostředí HBase. Zadejte následující příkaz:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopírování souboru hbase-site.xml do clusteru Spark

Zkopírujte soubor hbase-site.xml z místního úložiště do kořenového adresáře výchozího úložiště clusteru Spark.  Upravte níže uvedený příkaz tak, aby odrážel vaši konfiguraci.  Potom z otevřené relace SSH do clusteru HBase zadejte příkaz:

| Hodnota syntaxe | Nová hodnota|
|---|---|
|[Schéma URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Upravte tak, aby odráželo vaše úložiště.  Syntaxe níže je pro úložiště objektů blob s povoleným zabezpečeným přenosem.|
|`SPARK_STORAGE_CONTAINER`|Nahraďte výchozí název kontejneru úložiště používaný pro cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Nahraďte výchozí název účtu úložiště používaný pro cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Potom ukončete připojení ssh ke clusteru HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Vložte soubor hbase-site.xml do clusteru Spark

1. Připojte se k hlavnímu uzlu clusteru Spark pomocí SSH. Upravte níže uvedený `SPARKCLUSTER` příkaz nahrazením názvem clusteru Spark a zadejte příkaz:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Zadejte níže uvedený `hbase-site.xml` příkaz pro kopírování z výchozího úložiště clusteru Spark do konfigurační složky Spark 2 v místním úložišti clusteru:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spuštění prostředí Spark, které odkazuje na konektor Spark HBase

1. Z otevřené relace SSH do clusteru Spark zadejte níže uvedený příkaz a spusťte prostředí jiskry:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Ponechte tuto instanci Prostředí Spark otevřenou a pokračujte dalším krokem.

## <a name="define-a-catalog-and-query"></a>Definování katalogu a dotazu

V tomto kroku definujete objekt katalogu, který mapuje schéma z Apache Spark na Apache HBase.  

1. Do otevřeného prostředí Spark `import` Shell zadejte následující příkazy:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Zadáním následujícího příkazu definujte katalog pro tabulku Kontaktů, kterou jste vytvořili v HBase:

    ```scala
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
    ```

    Kód provádí následující akce:  

     a. Definujte schéma katalogu pro tabulku HBase s názvem `Contacts`.  
     b. Identifikujte řádek jako `key`a namapujte názvy sloupců použité v Spark na rodinu sloupců, název sloupce a typ sloupce, jak je použit v HBase.  
     c. Řádek klávesy musí být také podrobně definován`rowkey`jako pojmenovaný sloupec ( `cf` `rowkey`), který má konkrétní rodinu sloupců .  

1. Chcete-li definovat metodu, která poskytuje datový `Contacts` rámec kolem tabulky v hbase, zadejte následující příkaz:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Vytvořte instanci datového rámce:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Dotaz na datový rámec:

    ```scala
    df.show()
    ```

    Měli byste vidět dva řádky dat:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Zaregistrujte dočasnou tabulku, abyste se mohli dotazovat na tabulku HBase pomocí Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Vydejte dotaz `contacts` SQL proti tabulce:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Výsledky byste měli vidět takto:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Vložení nových dat

1. Chcete-li vložit nový záznam `ContactRecord` kontaktu, definujte třídu:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Vytvořte instanci `ContactRecord` a vložte ji do pole:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Uložte pole nových dat do HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Prohlédněte si výsledky:

    ```scala  
    df.show()
    ```

    Měl by se zobrazit výstup podobný tomuto:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Zavřete zapalovací skořepinu zadáním následujícího příkazu:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Další kroky

* [Konektor Apache Spark HBase](https://github.com/hortonworks-spark/shc)
