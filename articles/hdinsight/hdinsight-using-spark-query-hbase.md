---
title: Použití Sparku ke čtení a zápisu dat HBA – Azure HDInsight
description: Konektor Spark HBA slouží ke čtení a zápisu dat z clusteru Spark do clusteru HBA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: e6b3fc4f9badeedbed55f89702933b41a952977b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180804"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Použití Apache Sparku ke čtení a zápisu dat Apache HBase

Apache Hbas se obvykle dotazuje buď pomocí rozhraní API na nižší úrovni (vyhledávání, získání a vložení), nebo pomocí syntaxe SQL pomocí Apache Phoenix. Apache taky poskytuje konektor Apache Spark HBA, což je praktická alternativa k dotazování a úpravám dat uložených v rámci adaptérů HBA.

## <a name="prerequisites"></a>Požadavky

* Ve stejné virtuální síti jsou nasazené dva samostatné clustery HDInsight. Je nainstalovaná jedna z adaptérů HBA a jedna Spark s aspoň Spark 2,1 (HDInsight 3,6). Další informace najdete v tématu [Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma identifikátoru URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To je wasb://pro Azure Blob Storage, abfs://pro Azure Data Lake Storage Gen2 nebo adl://pro Azure Data Lake Storage Gen1. Pokud je pro Blob Storage povolený zabezpečený přenos, identifikátor URI `wasbs://`by byl.  Viz také [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně, který umožňuje vašemu clusteru Spark dotazovat se na cluster HDInsight, je následující:

1. Připravte si některá ukázková data v adaptérech HBA.
2. Získejte soubor HBase-site. XML z vaší složky Konfigurace clusteru HBA (/etc/HBase/conf).
3. Uložte kopii HBase-site. XML do konfigurační složky Spark 2 (/etc/spark2/conf).
4. Spusťte `spark-shell` odkazování konektoru Spark HBA podle jeho souřadnic Maven `packages` v možnosti.
5. Definujte katalog, který mapuje schéma ze Sparku na HBA.
6. Můžete pracovat s daty HBA pomocí rozhraní API RDD nebo dataframe.

## <a name="prepare-sample-data-in-apache-hbase"></a>Příprava ukázkových dat v Apache HBA

V tomto kroku vytvoříte a naplníte tabulku v Apache Hbach, které pak můžete dotazovat pomocí Sparku.

1. `ssh` Pomocí příkazu se připojte ke clusteru HBA. Níže uvedený příkaz upravte nahrazením `HBASECLUSTER` názvem vašeho clusteru HBA a zadáním příkazu:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell` Pomocí příkazu spusťte interaktivní prostředí pro adaptéry HBA. Do připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

3. `create` Pomocí příkazu vytvořte tabulku HBA se dvěma skupinami sloupců. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `put` Pomocí příkazu můžete vkládat hodnoty do zadaného sloupce v zadaném řádku v konkrétní tabulce. Zadejte následující příkaz:

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

5. `exit` Pomocí příkazu zastavte prostředí HBA interaktivní prostředí. Zadejte následující příkaz:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopírování HBase-site. XML do clusteru Spark

Zkopírujte soubor HBase-site. XML z místního úložiště do kořenového adresáře výchozího úložiště clusteru Spark.  Upravte následující příkaz tak, aby odrážel vaši konfiguraci.  Pak z otevřené relace SSH do clusteru HBA zadejte příkaz:

| Hodnota syntaxe | Nová hodnota|
|---|---|
|[Schéma identifikátoru URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Upravte, aby odrážela vaše úložiště.  Níže uvedená syntaxe je pro úložiště objektů BLOB s povoleným zabezpečeným přenosem.|
|`SPARK_STORAGE_CONTAINER`|Nahraďte výchozím názvem kontejneru úložiště použitým pro cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Nahraďte názvem výchozího účtu úložiště použitým pro cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Pak ukončete připojení SSH k vašemu clusteru HBA.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Vložte HBase-site. XML do clusteru Spark.

1. Připojte se k hlavnímu uzlu clusteru Spark pomocí SSH.

2. Zadejte následující příkaz pro zkopírování `hbase-site.xml` z výchozího úložiště clusteru Spark do složky Konfigurace Spark 2 v místním úložišti clusteru:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spustit prostředí Spark odkazující na konektor Spark HBA

1. V otevřené relaci SSH ke clusteru Spark zadejte následující příkaz, který spustí prostředí Spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Nechejte tuto instanci prostředí Spark otevřené a pokračujte dalším krokem.

## <a name="define-a-catalog-and-query"></a>Definování katalogu a dotazu

V tomto kroku definujete objekt katalogu, který mapuje schéma z Apache Spark na Apache HBA.  

1. V otevřeném prostředí Spark zadejte následující `import` příkazy:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Zadejte následující příkaz pro definování katalogu pro tabulku kontaktů, kterou jste vytvořili v části HBA:

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

    Kód provede následující:  

     a. Definujte schéma katalogu pro tabulku HBA s názvem `Contacts`.  
     b. Identifikujte rowkey jako `key`a namapujte názvy sloupců používané ve Sparku na rodinu sloupců, název sloupce a typ sloupce, jak se používá v adaptérech HBA.  
     c. Rowkey musí být také definováno podrobněji jako pojmenovaný sloupec (`rowkey`), který má konkrétní `rowkey`rodinu `cf` sloupců.  

3. Níže uvedeným příkazem Definujte metodu, která poskytuje objekt dataframe kolem `Contacts` tabulky v hbach:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Vytvořte instanci datového rámce:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Dotaz na datový rámec:

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

7. Zaregistrujte dočasnou tabulku, abyste mohli zadat dotaz na tabulku HBA pomocí Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Vydejte dotaz SQL na `contacts` tabulku:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Měli byste vidět podobné výsledky:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Vložit nová data

1. Chcete-li vložit nový záznam kontaktu, definujte `ContactRecord` třídu:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Vytvořte instanci `ContactRecord` a vložte ji do pole:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Uložte pole nových dat do HBA:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Projděte si výsledky:

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

6. Zavřete prostředí Spark zadáním následujícího příkazu:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Další kroky

* [Konektor Apache Spark HBA](https://github.com/hortonworks-spark/shc)
