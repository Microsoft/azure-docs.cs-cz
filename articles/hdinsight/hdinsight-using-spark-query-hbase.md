---
title: Použití Sparku ke čtení a zápisu dat HBase – Azure HDInsight
description: Konektor Spark HBase můžete číst a zapisovat data z clusteru Spark pro HBase cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: e747f39ca84bb859b37550efef51e01cffd96876
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056750"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Použití Apache Sparku ke čtení a zápisu dat Apache HBase

Apache HBase je dotazovaný obvykle s jeho nízké úrovně rozhraní API (kontrol, získá a vloží) nebo se syntaxí SQL pomocí Apache Phoenix. Apache také poskytuje Apache Spark HBase konektor, který je pohodlnou a výkonnější alternativou k dotazování a modifikaci dat uložených v HBase.

## <a name="prerequisites"></a>Požadavky

* Dva samostatné clustery HDInsight, které jsou nasazené ve stejné virtuální síti. Jeden jeden HBase a Spark s nejméně Sparku 2.1 (HDInsight 3.6) nainstalovaný. Další informace najdete v tématu [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma identifikátoru URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) jako primární úložiště vašich clusterů. To může být wasb: / / Azure Blob Storage, abfs: / / pro Azure Data Lake Storage Gen2 nebo adl: / / pro Azure Data Lake Storage Gen1. Pokud pro úložiště objektů Blob nebo Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by wasbs: / / nebo abfss: / /, respektive naleznete také [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně pro povolení váš cluster Spark na HDInsight cluster dotazovat vypadá takto:

1. Příprava nějaká ukázková data v HBase.
2. Získejte hbase-site.xml soubor ze složky Konfigurace clusteru HBase (/ etc/hbase/potvrzení).
3. Ve složce Konfigurace Spark 2 (/ etc/spark2/conf), umístěte kopii hbase-site.xml.
4. Spustit `spark-shell` odkazující na konektoru Spark HBase pomocí jeho Maven v koordinuje `packages` možnost.
5. Definujte katalog, který mapuje schématu ze Spark HBase.
6. Práce s daty HBase pomocí RDD nebo rozhraní API datového rámce.

## <a name="prepare-sample-data-in-apache-hbase"></a>Příprava ukázkových dat v Apache HBase

V tomto kroku vytvoření a vyplnění tabulky v Apache HBase, který potom můžete dotazovat pomocí Sparku.

1. Použití `ssh` příkazu se připojte ke svému clusteru HBase. Upravte následující příkaz tak, že nahradíte `HBASECLUSTER` s názvem vaší HBase clusteru a potom zadejte příkaz:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Použití `hbase shell` příkaz ke spuštění interaktivního prostředí HBase. Zadejte následující příkaz v připojení SSH:

    ```bash
    hbase shell
    ```

3. Použití `create` příkaz pro vytvoření tabulky HBase se dvěma skupinami sloupců. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Použití `put` příkaz pro vložení hodnot v zadaném sloupci v zadaný řádek v určité tabulce. Zadejte následující příkaz:

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

5. Použití `exit` příkaz k zastavení interaktivní prostředí HBase. Zadejte následující příkaz:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Zkopírujte hbase-site.xml ke clusteru Spark

Zkopírujte hbase-site.xml z místního úložiště do kořenového adresáře výchozí úložiště clusteru Spark.  Upravte následující příkaz tak, aby odrážely konfiguraci.  V otevřít relaci SSH pro HBase cluster, zadejte příkaz:

| Hodnota syntaxe | Nová hodnota|
|---|---|
|[Schéma identifikátoru URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Upravte tak, aby odrážely úložiště.  Níže uvedené syntaxe je pro úložiště objektů blob s povoleným zabezpečeným přenosem.|
|`SPARK_STORAGE_CONTAINER`|Nahraďte výchozí název kontejneru úložiště používají pro clustery Spark.|
|`SPARK_STORAGE_ACCOUNT`|Nahraďte výchozí název účtu úložiště pro Spark cluster.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Ukončete vaše ssh připojení k vašemu clusteru HBase.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Vložit hbase-site.xml na svém clusteru Spark

1. Připojení k hlavnímu uzlu clusteru Sparku pomocí protokolu SSH.

2. Zadejte následující příkaz pro kopírování `hbase-site.xml` z výchozí úložiště clusteru Spark ke složce Spark 2 konfigurace v místním úložišti clusteru:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spusťte prostředí Sparku odkazující na konektor Spark HBase

1. V otevřít relaci SSH ke clusteru Spark zadejte následující příkaz ke spuštění prostředí sparku:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Ponechat otevřené tuto instanci prostředí Spark a pokračovat k dalšímu kroku.

## <a name="define-a-catalog-and-query"></a>Zadejte katalogu a dotaz

V tomto kroku definujete objekt katalogu, který se mapuje schéma z Apache Spark pro Apache HBase.  

1. Otevřete prostředí Sparku, zadejte následující `import` příkazy:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Zadejte následující příkaz k definování katalogu pro tabulky kontaktů, že kterou jste vytvořili v HBase:

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

    Kód provede následující akce:  

     a. Definovat schéma katalogu s názvem tabulky HBase `Contacts`.  
     b. Identifikujte rowkey jako `key`a mapování názvů sloupce používané v rodině sloupců, název sloupce a typ sloupce v HBase Spark.  
     c. Také musí být definováno v podrobnosti jako sloupec s názvem rowkey (`rowkey`), který má konkrétní sloupce řady `cf` z `rowkey`.  

3. Zadejte následující příkaz k definování metodu, která poskytuje datový rámec kolem vašeho `Contacts` tabulky v HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Vytvoření instance datového rámce:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Dotaz datového rámce:

    ```scala
    df.show()
    ```

6. Měli byste vidět dva řádky dat:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zaregistrujte dočasnou tabulku můžete zadat dotaz na tabulku HBase pomocí Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Vydávání dotazů SQL proti `contacts` tabulky:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Měli byste vidět výsledky, jako jsou tyto:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Vložte nová data

1. Chcete-li vložit nový záznam kontaktu, definujte `ContactRecord` třídy:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Vytvoření instance `ContactRecord` a vložit ho do pole:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Pole nová data uložte do HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Podívejte se na výsledky:

    ```scala  
    df.show()
    ```

5. Měl by se zobrazit výstup podobný tomuto:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

6. Ukončete prostředí sparku tak, že zadáte následující příkaz:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Další postup

* [Apache Spark HBase Connector](https://github.com/hortonworks-spark/shc)
