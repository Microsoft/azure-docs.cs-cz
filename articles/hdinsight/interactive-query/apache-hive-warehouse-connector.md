---
title: Apache Spark & – konektor pro datový sklad podregistru – Azure HDInsight
description: Přečtěte si, jak integrovat Apache Spark a Apache Hive pomocí konektoru skladu pro podregistr v Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 2448550cf35f92bc8d91bc6ad9d5b22cc90b5ae0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494315"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrace Apache Spark a Apache Hive s konektorem skladu podregistru

Konektor Apache Hive Warehouse (umožní) je knihovna, která umožňuje snadněji pracovat s Apache Spark a Apache Hive tím, že podporuje úkoly, jako je přesun dat mezi datovými tabulkami Sparku a tabulkami podregistru a také nasměrování dat pro streamování Sparku do tabulek podregistru. Konektor datového skladu pro podregistr funguje jako most mezi Sparkem a podregistrem. Podporuje Scala, Java a Python pro vývoj.

Konektor skladiště pro podregistr umožňuje využívat jedinečné funkce podregistru a Sparku k vytváření výkonných aplikací pro velké objemy dat. Apache Hive nabízí podporu pro databázové transakce, které jsou atomické, konzistentní, izolované a trvalé (KYSELé). Další informace o KYSELINě a transakcích v podregistru najdete v tématu [transakce podregistru](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Podregistr také nabízí podrobné kontrolní mechanismy zabezpečení prostřednictvím Apache Ranger a analytické zpracování s nízkou latencí, které není dostupné v Apache Spark.

Apache Spark má strukturované rozhraní API pro streamování, které poskytuje možnosti streamování, které nejsou dostupné v Apache Hive. Od HDInsight 4,0 Apache Spark 2.3.1 a Apache Hive 3.1.0 samostatné metaúložiště, což může zajistit obtížnou interoperabilitu. Konektor pro skladiště podregistru usnadňuje používání Sparku a úlů společně. Knihovna umožní načítá data ze LLAP démonů do paralelního vykonavatele paralelně, takže je efektivnější a škálovatelnější než použití standardního připojení JDBC z Sparku do podregistru.

![Architektura konektoru skladu podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Mezi operace podporované konektorem skladu podregistru patří:

* Popis tabulky
* Vytvoření tabulky pro data ve formátu ORC
* Výběr dat podregistru a načítání datového rámce
* Zápis datového rámce do podregistru ve Batch
* Spuštění příkazu aktualizace podregistru
* Čtení dat tabulky z podregistru, transformace v Sparku a její zápis do nové tabulky podregistru
* Zápis datového rámce nebo datového proudu Sparku do podregistru pomocí HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Nastavení konektoru pro skladiště v podregistru

Postupujte podle těchto kroků a nastavte konektor pro datový sklad mezi Sparkem a interaktivním dotazem v Azure HDInsight:

### <a name="create-clusters"></a>Vytváření clusterů

1. Vytvořte cluster HDInsight Spark **4,0** s účtem úložiště a vlastní virtuální sítí Azure. Informace o vytvoření clusteru ve virtuální síti Azure najdete v tématu [Přidání HDInsight do existující virtuální sítě](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Vytvořte cluster HDInsight Interactive Query (LLAP) **4,0** se stejným účtem úložiště a Azure Virtual Network jako cluster Spark.

### <a name="modify-hosts-file"></a>Upravit soubor hostitelů

Zkopírujte informace o uzlu ze souboru `/etc/hosts` v headnode0 vašeho clusteru interaktivních dotazů a zřetězte informace do souboru `/etc/hosts` na headnode0 clusteru Spark. Tento krok umožní vašemu clusteru Spark přeložit IP adresy uzlů v clusteru interaktivních dotazů. Zobrazte obsah aktualizovaného souboru pomocí `cat /etc/hosts`. Konečný výstup by měl vypadat nějak takto, jak je znázorněno na snímku obrazovky níže.

![soubor hostitelů konektoru skladu podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Shromáždit předběžné informace

#### <a name="from-your-interactive-query-cluster"></a>Z vašeho clusteru interaktivních dotazů

1. Přejděte na domovskou stránku Apache Ambari clusteru pomocí `https://LLAPCLUSTERNAME.azurehdinsight.net`, kde `LLAPCLUSTERNAME` je název vašeho clusteru interaktivních dotazů.

1. Přejděte do **podregistru** > **config** > **Advanced** > **Advanced podregistr-site** >  –**Zookeeper. kvorum** a poznamenejte si hodnotu. Hodnota může být podobná: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Přejděte do **podregistru** > **config** > **Advanced** > **General** > .**URI** a poznamenejte si hodnotu. Hodnota může být podobná: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>Z Apache Spark clusteru

1. Přejděte na domovskou stránku Apache Ambari clusteru pomocí `https://SPARKCLUSTERNAME.azurehdinsight.net`, kde `SPARKCLUSTERNAME` je název vašeho clusteru Apache Spark.

1. Přejděte do **podregistru** > **config** > **Advanced** > **Advanced podregistr-Interactive-site** >  –**llap. daemon. Service. Hosts** a poznamenejte si hodnotu. Hodnota může být podobná: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Konfigurace nastavení clusteru Spark

Z webového uživatelského rozhraní Spark Ambari přejděte na **Spark2** > **config** > **Custom Spark2-Defaults**.

![Konfigurace Spark2 Apache Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Vyberte **Přidat vlastnost...** podle potřeby přidejte nebo aktualizujte následující:

| Klíč | Hodnota |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Hodnota, kterou jste získali dříve z **podregistru. llap. démon. Service. Hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Nastavte na připojovací řetězec JDBC, který se připojí k Hiveserver2 v clusteru interaktivních dotazů. NAHRAĎte `LLAPCLUSTERNAME` názvem vašeho clusteru interaktivních dotazů. Nahraďte `PWD` skutečným heslem.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Nastavte vhodný přípravný adresář kompatibilní s HDFS. Pokud máte dva různé clustery, pracovní adresář by měl být složka v pracovním adresáři účtu úložiště LLAP clusteru, aby k němu měl přístup HiveServer2.  Místo `STORAGE_ACCOUNT_NAME` nahraďte názvem účtu úložiště použitým clusterem a `STORAGE_CONTAINER_NAME` názvem kontejneru úložiště.|
|`spark.datasource.hive.warehouse.metastoreUri`|Hodnota, kterou jste získali dříve z **podregistru. metastore. URI**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` pro režim nasazení klienta PŘÍZ.|
|`spark.hadoop.hive.zookeeper.quorum`|Hodnota, kterou jste získali dříve z **podregistru. Zookeeper. kvora**.|

Podle potřeby uložte změny a restartujte součásti.

## <a name="using-the-hive-warehouse-connector"></a>Použití konektoru skladu z podregistru

### <a name="connecting-and-running-queries"></a>Připojení a spuštění dotazů

Můžete si vybrat mezi několika různými způsoby, jak se připojit ke clusteru interaktivních dotazů a provádět dotazy pomocí konektoru skladu z podregistru. Mezi podporované metody patří následující nástroje:

* [Spark – prostředí](../spark/apache-spark-shell.md)
* PySpark
* Spark – odeslání
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Všechny příklady, které jsou uvedené v tomto článku, se spustí prostřednictvím Spark-Shell.

Chcete-li spustit relaci Spark-Shell, proveďte následující kroky:

1. SSH do hlavnímu uzlu pro váš cluster Apache Spark. Další informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Zadejte následující příkaz, který spustí prostředí Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Zobrazí se uvítací zpráva a výzva `scala>`, kde můžete zadat příkazy.

1. Po spuštění Spark-Shell můžete instanci konektoru skladu podregistru spustit pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Připojení a spuštění dotazů v clusterech Balíček zabezpečení podniku (ESP)

Balíček zabezpečení podniku (ESP) poskytuje podnikové funkce, jako je ověřování založené na službě Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro Apache Hadoop clustery ve službě Azure HDInsight. Další informace o protokolu ESP najdete v tématu [použití balíček zabezpečení podniku ve službě HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH do hlavnímu uzlu pro váš cluster Apache Spark. Další informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Zadejte `kinit` a přihlaste se jako uživatel domény.

1. Spusťte Spark-shell s úplným seznamem parametrů konfigurace, jak je znázorněno níže. V závislosti na vašem clusteru je nutné zadat všechny hodnoty ze všech velkých písmen mezi lomenými závorkami. Pokud potřebujete zjistit hodnoty, které se mají zadat pro některý z níže uvedených parametrů, přečtěte si část o [Nastavení konektoru ve skladu pro podregistr](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Vytváření Spark dataframes z dotazů na podregistr

Výsledky všech dotazů využívajících knihovnu umožní jsou vráceny jako datový rámec. Následující příklady ukazují, jak vytvořit základní dotaz.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Výsledky dotazu jsou Spark dataframes, které lze použít s knihovnami Spark jako MLIB a SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zápis datového rámce Spark do tabulek podregistru

Spark nebude nativně podporovat zápis do spravovaných tabulek KYSELosti podregistru. Pomocí umožní ale můžete napsat libovolný datový rámec do tabulky podregistru. Tuto funkci můžete zobrazit v práci v následujícím příkladu:

1. Vytvořte tabulku s názvem `sampletable_colorado` a určete její sloupce pomocí následujícího příkazu:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Vyfiltruje tabulku `hivesampletable`, kde sloupec `state` se rovná `Colorado`. Tento dotaz na tabulku podregistru se vrátí jako datový rámec Spark. Pak je datový rámec uložen v tabulce podregistru `sampletable_colorado` pomocí funkce `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Výsledky zobrazíte pomocí následujícího příkazu:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![konektor datového skladu pro zobrazení tabulky podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturované zápisy streamování

Pomocí konektoru Warehouse pro podregistr můžete pomocí streamování Spark zapisovat data do tabulek podregistru.

Pomocí následujících kroků vytvořte příklad konektoru skladu s podmnožinou, který ingestuje data z datového proudu Spark na portu hostitele 9999 do tabulky podregistru.

1. Postupujte podle kroků v části [připojení a spuštění dotazů](#connecting-and-running-queries).

1. Spusťte datový proud Spark pomocí následujícího příkazu:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Pomocí následujících kroků vygenerujte data pro datový proud Spark, který jste vytvořili:
    1. Otevřete druhou relaci SSH na stejném clusteru Spark.
    1. Do příkazového řádku zadejte `nc -lk 9999`. Tento příkaz používá nástroj Netcat k posílání dat z příkazového řádku na zadaný port.

1. Vraťte se k první relaci SSH a vytvořte novou tabulku podregistru pro ukládání dat streamování. V prostředí Spark zadejte následující příkaz:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Pak zapište streamovaná data do nově vytvořené tabulky pomocí následujícího příkazu:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Možnosti `metastoreUri` a `database` je nyní nutné nastavit ručně z důvodu známého problému v Apache Spark. Další informace o tomto problému naleznete v [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Vraťte se k druhé relaci SSH a zadejte následující hodnoty:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Vraťte se k první relaci SSH a poznamenejte si stručnou aktivitu. Data zobrazíte pomocí následujícího příkazu:

    ```scala
    hive.table("stream_table").show()
    ```

Pomocí **kombinace kláves CTRL + C** zastavte NetCat v druhé relaci SSH. Pomocí `:q` ukončíte prostředí Spark-Shell v první relaci SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpečení dat v clusterech Spark ESP

1. Vytvořte tabulku `demo` s ukázkovými daty zadáním následujících příkazů:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zobrazte obsah tabulky pomocí následujícího příkazu. Před použitím této zásady se v tabulce `demo` zobrazuje celý sloupec.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ukázková tabulka před použitím zásad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Použijte zásadu maskování sloupců, která zobrazuje jenom poslední čtyři znaky sloupce.  
    1. V `https://CLUSTERNAME.azurehdinsight.net/ranger/` přejdete do uživatelského rozhraní správce Ranger.
    1. Klikněte na podregistr Service pro váš cluster v **podregistru**.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klikněte na kartu **maskování** a pak **přidejte nové zásady** .

        ![seznam zásad podregistru Ranger konektoru skladu podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Zadejte požadovaný název zásad. Vyberte databázi: **výchozí**, tabulka podregistru: **Ukázka**, sloupec podregistru: **název**, uživatel: **Rsadmin2**, typy přístupu: **Vybrat**a **částečná maska: Zobrazit poslední 4** v nabídce **možností výběru maskování** . Klikněte na tlačítko **Přidat**.
                ![vytvoření zásady](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Znovu zobrazte obsah tabulky. Po použití zásad Ranger uvidíme jenom poslední čtyři znaky sloupce.

    ![Ukázková tabulka po použití zásad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Další kroky

* [Použití Interactive Query se službou HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started).
* [Příklady interakce s konektorem skladiště v podregistru pomocí Zeppelin, Livy, Spark-Submit a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
