---
title: Použití Sparku ke čtení a zápisu dat HBA – Azure HDInsight
description: Konektor Spark HBA slouží ke čtení a zápisu dat z clusteru Spark do clusteru HBA.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931227"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Použití Apache Sparku ke čtení a zápisu dat Apache HBase

Apache Hbas se obvykle dotazuje buď pomocí rozhraní API na nižší úrovni (vyhledávání, získání a vložení), nebo pomocí syntaxe SQL pomocí Apache Phoenix. Apache taky poskytuje konektor Apache Spark HBA. Konektor je praktická a efektivní alternativa k dotazování a úpravám dat uložených pomocí adaptérů HBA.

## <a name="prerequisites"></a>Požadavky

* Ve stejné [virtuální síti](./hdinsight-plan-virtual-network-deployment.md)jsou nasazené dva samostatné clustery HDInsight. Je nainstalovaná jedna z adaptérů HBA a jedna Spark s aspoň Spark 2,1 (HDInsight 3,6). Další informace najdete v tématu [Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* Schéma identifikátoru URI pro primární úložiště clusterů. Toto schéma by bylo wasb://pro Azure Blob Storage pro `abfs://` Azure Data Lake Storage Gen2 nebo ADL://pro Azure Data Lake Storage Gen1. Pokud je pro Blob Storage povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` .  Viz také [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Celkový proces

Proces vysoké úrovně, který umožňuje vašemu clusteru Spark dotazovat se na váš cluster HBA, je následující:

1. Připravte si některá ukázková data v adaptérech HBA.
2. Získejte soubor hbase-site.xml z vaší konfigurační složky clusteru HBA (/etc/HBase/conf) a umístěte kopii hbase-site.xml do konfigurační složky Spark 2 (/etc/spark2/conf). (Volitelné: pro automatizaci tohoto procesu použijte skript poskytovaný týmem HDInsight.)
4. Spusťte `spark-shell` odkazování konektoru Spark HBA podle jeho souřadnic Maven v `packages` Možnosti.
5. Definujte katalog, který mapuje schéma ze Sparku na HBA.
6. Můžete pracovat s daty HBA pomocí rozhraní API RDD nebo dataframe.

## <a name="prepare-sample-data-in-apache-hbase"></a>Příprava ukázkových dat v Apache HBA

V tomto kroku vytvoříte a naplníte tabulku v Apache Hbach, které pak můžete dotazovat pomocí Sparku.

1. Pomocí `ssh` příkazu se připojte ke clusteru HBA. Níže uvedený příkaz upravte nahrazením `HBASECLUSTER` názvem vašeho clusteru HBA a zadáním příkazu:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí pro adaptéry HBA. Do připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

3. Pomocí `create` příkazu vytvořte tabulku HBA se dvěma skupinami sloupců. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Pomocí `put` příkazu můžete vkládat hodnoty do zadaného sloupce v zadaném řádku v konkrétní tabulce. Zadejte následující příkaz:

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

5. Pomocí `exit` příkazu zastavte prostředí HBA interaktivní prostředí. Zadejte následující příkaz:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Spuštění skriptů pro nastavení připojení mezi clustery

Pokud chcete nastavit komunikaci mezi clustery, postupujte podle následujících kroků a spusťte ve svých clusterech dva skripty. Tyto skripty automatizují proces kopírování souborů, který je popsaný v části Nastavení komunikace ručně. 

* Skript, který spustíte z clusteru HBA, nahraje `hbase-site.xml` a přidá informace mapování IP na výchozí úložiště připojené ke clusteru Spark. 
* Skript, který spustíte z clusteru Spark, nastaví dvě úlohy cron, aby se pravidelně spouštěly dva pomocné skripty:  
    1.  HBA cron úlohu – umožňuje stáhnout nové `hbase-site.xml` soubory a HBA mapování IP adres z výchozího účtu úložiště Spark do místního uzlu.
    2.  Úloha Spark cron – kontroluje, jestli došlo k škálování Sparku a pokud je cluster zabezpečený. Pokud ano, upravte, `/etc/hosts` aby zahrnovaly místně uložené mapování IP adaptérů.

__Poznámka__: než budete pokračovat, ujistěte se, že jste přidali účet úložiště clusteru Spark do clusteru HBA jako sekundární účet úložiště. Ujistěte se, že jsou skripty v uvedeném pořadí, jak je uvedeno níže.


1. Použijte [akci skriptu](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) na svém clusteru HBA, aby se změny projevily s následujícími požadavky: 


    |Vlastnost | Hodnota |
    |---|---|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Typ (typy) uzlů|Oblast|
    |Parametry|`-s SECONDARYS_STORAGE_URL`|
    |Trvalé|ano|

    * `SECONDARYS_STORAGE_URL` je adresa URL výchozího úložiště na straně Spark. Příklad parametru: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Použijte akci skriptu v clusteru Spark pro použití změn s následujícími požadavky:

    |Vlastnost | Hodnota |
    |---|---|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Typ (typy) uzlů|Vedoucí pracovník, Zookeeper|
    |Parametry|`-s "SPARK-CRON-SCHEDULE"` (volitelné) `-h "HBASE-CRON-SCHEDULE"` volitelné|
    |Trvalé|ano|


    * Můžete určit, jak často chcete, aby tento cluster automaticky kontroloval aktualizace. Výchozí:-s "*/1 * * * *"-h 0 (v tomto příkladu se Spark cron spouští každou minutu, zatímco adaptéry HBA cron neběží)
    * Vzhledem k tomu, že adaptéry HBA cron nejsou nastavené ve výchozím nastavení, musíte tento skript spustit znovu při provádění škálování clusteru HBA. Pokud se vaše adaptéry HBA často škálují, můžete se rozhodnout nastavit úlohu HBA cron automaticky. Například: `-h "*/30 * * * *"` nakonfiguruje skript, aby prováděl kontroly každých 30 minut. Tím se v pravidelných intervalech cron plán HBA pro automatizaci stahování nových informací o adaptérech HBA na společném účtu úložiště do místního uzlu.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Ruční nastavení komunikace (nepovinný, pokud zadaný skript ve výše uvedeném kroku selže)

__Poznámka:__ Tyto kroky je potřeba provést pokaždé, když jeden z clusterů přechází do aktivity škálování.

1. Zkopírujte hbase-site.xml z místního úložiště do kořenového adresáře výchozího úložiště clusteru Spark.  Upravte následující příkaz tak, aby odrážel vaši konfiguraci.  Pak z otevřené relace SSH do clusteru HBA zadejte příkaz:

    | Hodnota syntaxe | Nová hodnota|
    |---|---|
    |[Schéma identifikátoru URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Upravte, aby odrážela vaše úložiště.  Níže uvedená syntaxe je pro úložiště objektů BLOB s povoleným zabezpečeným přenosem.|
    |`SPARK_STORAGE_CONTAINER`|Nahraďte výchozím názvem kontejneru úložiště použitým pro cluster Spark.|
    |`SPARK_STORAGE_ACCOUNT`|Nahraďte názvem výchozího účtu úložiště použitým pro cluster Spark.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Pak ukončete připojení SSH k vašemu clusteru HBA.

    ```bash
    exit
    ```


3. Připojte se k hlavnímu uzlu clusteru Spark pomocí SSH. Níže uvedený příkaz upravte nahrazením `SPARKCLUSTER` názvem vašeho clusteru Spark a zadáním příkazu:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Zadejte následující příkaz pro zkopírování `hbase-site.xml` z výchozího úložiště clusteru Spark do složky Konfigurace Spark 2 v místním úložišti clusteru:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spustit prostředí Spark odkazující na konektor Spark HBA

Po dokončení předchozího kroku byste měli být schopni spustit prostředí Spark, které odkazuje na příslušnou verzi konektoru Spark HBA. Nejnovější verzi jádra konektoru Spark HBA pro váš scénář najdete v tématu [úložiště SHC Core](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Příklad: v následující tabulce jsou uvedeny dvě verze a odpovídající příkazy, které tým HDInsight aktuálně používá. Pokud jsou verze adaptérů HBA a Spark stejné jako v tabulce, můžete pro své clustery použít stejné verze. 


1. V otevřené relaci SSH ke clusteru Spark zadejte následující příkaz, který spustí prostředí Spark:

    |Verze Sparku| Verze HDI HBA  | Verze SHC    |  Příkaz  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBA 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2,4    | HDI 4,0 (HBA 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Nechte tuto instanci prostředí Sparku otevřenou a pokračujte [definováním katalogu a dotazu](#define-a-catalog-and-query). Pokud nenajdete jar, který odpovídá vašim verzím v úložišti SHC Core, pokračujte ve čtení. 

JAR můžete vytvořit přímo z větve GitHubu [Spark-HBA-Connector](https://github.com/hortonworks-spark/shc) . Pokud například používáte se systémem Spark 2,3 a HBA 1,1, proveďte tyto kroky:

1. Naklonujte úložiště:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Přejít na větev-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Sestavení z větve (vytvoří soubor. jar):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Spusťte následující příkaz (nezapomeňte změnit název. jar, který odpovídá sestavenému souboru. jar):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Nechte tuto instanci prostředí Sparku otevřenou a pokračujte k další části. 



## <a name="define-a-catalog-and-query"></a>Definování katalogu a dotazu

V tomto kroku definujete objekt katalogu, který mapuje schéma z Apache Spark na Apache HBA.  

1. V otevřeném prostředí Spark zadejte následující `import` příkazy:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Zadejte následující příkaz pro definování katalogu pro tabulku kontaktů, kterou jste vytvořili v části HBA:

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

    Kód:  

    1. Definuje schéma katalogu pro tabulku HBA s názvem `Contacts` .  
    1. Identifikuje rowkey jako `key` a mapuje názvy sloupců používané ve Sparku na rodinu sloupců, název sloupce a typ sloupce, jak se používá v adaptérech HBA.  
    1. Definuje rowkey podrobně jako pojmenovaný sloupec ( `rowkey` ), který má konkrétní rodinu sloupců `cf` `rowkey` .  

1. Níže uvedeným příkazem Definujte metodu, která poskytuje objekt dataframe kolem `Contacts` tabulky v hbach:

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

1. Zaregistrujte dočasnou tabulku, abyste mohli zadat dotaz na tabulku HBA pomocí Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Vydejte dotaz SQL na `contacts` tabulku:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Měli byste vidět podobné výsledky:

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

1. Vytvořte instanci `ContactRecord` a vložte ji do pole:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Uložte pole nových dat do HBA:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Projděte si výsledky:

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

1. Zavřete prostředí Spark zadáním následujícího příkazu:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Další kroky

* [Konektor Apache Spark HBA](https://github.com/hortonworks-spark/shc)
