---
title: Apache Spark & Hive – konektor pro sklad y Hive – Azure HDInsight
description: Přečtěte si, jak integrovat Apache Spark a Apache Hive s konektorem Hive Warehouse Connector na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252406"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrace Apache Spark a Apache Hive s konektorem Hive Warehouse Connector

Apache Hive Warehouse Connector (HWC) je knihovna, která vám umožní snadněji pracovat s Apache Spark a Apache Hive tím, že podporuje úlohy, jako je přesouvání dat mezi tabulkami Spark DataFrames a Hive a také nasměrování streamovaných dat Spark do tabulek Hive. Hive Warehouse Connector funguje jako most mezi Spark a Hive. Podporuje Scala, Java a Python pro vývoj.

Konektor hive skladu umožňuje využívat jedinečné funkce Hive a Spark k vytváření výkonných aplikací pro velká data. Apache Hive nabízí podporu pro databázové transakce, které jsou atomic, konzistentní, izolované a trvanlivé (ACID). Další informace o ACID a transakcích v Hive naleznete v [tématu Transakce Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive také nabízí podrobné bezpečnostní ovládací prvky prostřednictvím Apache Ranger a analytické zpracování s nízkou latencí, které nejsou k dispozici v Apache Spark.

Apache Spark, má strukturované streaming API, které poskytuje možnosti streamování není k dispozici v Apache Hive. Počínaje HDInsight 4.0, Apache Spark 2.3.1 a Apache Hive 3.1.0 mají samostatné metastores, což může ztížit interoperabilitu. Konektor pro sklad Hive usnadňuje společné používání Spark a Hive. Knihovna HWC načítá data z daemonů LLAP do vykonavatelů Spark paralelně, takže je efektivnější a škálovatelnější než použití standardního připojení JDBC ze Spark u Hive.

![architektura konektorů ve skladu úlu](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Některé operace podporované konektorem skladu Hive jsou:

* Popis tabulky
* Vytvoření tabulky pro data ve formátu ORC
* Výběr dat Hive a načítání datového rámce
* Dávkové zápisu datového rámce do podregistru
* Spuštění příkazu aktualizace Hive
* Čtení dat tabulky z Úlu, jejich transformace v Sparku a jejich zápis do nové tabulky Hive
* Zápis datového rámce nebo streamu Spark do Hive pomocí HiveStreamingu

## <a name="hive-warehouse-connector-setup"></a>Nastavení konektoru skladu Hive

Podle následujících kroků nastavte konektor skladu Hive mezi clusterem Spark a interactive query ve službě Azure HDInsight:

### <a name="create-clusters"></a>Vytváření clusterů

1. Vytvořte cluster HDInsight Spark **4.0** s účtem úložiště a vlastní virtuální sítí Azure. Informace o vytvoření clusteru ve virtuální síti Azure najdete v tématu [Přidání HDInsightu do existující virtuální sítě](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Vytvořte cluster **4.0** pro interaktivní dotaz HDInsight (LLAP) se stejným účtem úložiště a virtuální sítí Azure jako cluster Spark.

### <a name="modify-hosts-file"></a>Upravit soubor hostitelů

Zkopírujte informace o `/etc/hosts` uzlu ze souboru v clusteru interaktivních dotazů a `/etc/hosts` zřetězte je do souboru v hlavním uzlu clusteru Spark. Tento krok umožní clusteru Spark vyřešit IP adresy uzlů v clusteru interaktivních dotazů. Zobrazení obsahu aktualizovaného souboru pomocí aplikace `cat /etc/hosts`. Konečný výstup by měl vypadat podobně jako to, co je znázorněno na následujícím snímku obrazovky.

![konektor u jevu, který je hostitelem](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Shromažďujte předběžné informace

#### <a name="from-your-interactive-query-cluster"></a>Z clusteru interaktivních dotazů

1. Přejděte na stránku Apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive clusteru pomocí `LLAPCLUSTERNAME` názvu clusteru interaktivních dotazů.

1. Přejděte na **soubor Advanced** > **General** > **hive.metastore.uris** a poznamenejte si hodnotu. Hodnota může být podobná: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Přejděte na **stránku Advanced** > **Advanced hive-site** > **hive.zookeeper.quorum** a poznamenejte si hodnotu. Hodnota může být podobná: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>Z clusteru Apache Spark

1. Přejděte na stránku Apache Ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive clusteru pomocí místa, kde `SPARKCLUSTERNAME` je název vašeho clusteru Apache Spark.

1. Přejděte na **soubor Advanced** > **Advanced hive-interactive-site** > **hive.llap.daemon.service.hosts** a poznamenejte si hodnotu. Hodnota může být podobná: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Konfigurace nastavení clusteru Spark

Z webového uživatelského uživatelského uživatelského nastavení Spark Ambari přejděte na Výchozí hodnoty **Spark2** > **CONFIGS** > **spark2**.

![Konfigurace Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Vyberte **Přidat vlastnost...** podle potřeby přidat nebo aktualizovat následující:

| Klíč | Hodnota |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Hodnota, kterou jste získali dříve od **společnosti hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Nastavte na připojovací řetězec JDBC, který se připojuje k Hiveserver2 v clusteru interaktivních dotazů. NAHRAĎTE `LLAPCLUSTERNAME` názvem clusteru interaktivních dotazů. Nahraďte `PWD` skutečným heslem.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Nastavte na vhodný pracovní adresář kompatibilní s HDFS. Pokud máte dva různé clustery, pracovní adresář by měl být složka v pracovním adresáři účtu úložiště clusteru LLAP tak, aby k němu měl přístup HiveServer2.  Nahraďte `STORAGE_ACCOUNT_NAME` název účtu úložiště používaného clusterem `STORAGE_CONTAINER_NAME` a názvem kontejneru úložiště.|
|`spark.datasource.hive.warehouse.metastoreUri`|Hodnota, kterou jste získali dříve z **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`pro režim nasazení klienta YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|Hodnota, kterou jste získali dříve od **hive.zookeeper.quorum**.|

Podle potřeby uložte změny a restartujte součásti.

## <a name="using-the-hive-warehouse-connector"></a>Použití konektoru skladu Hive

### <a name="connecting-and-running-queries"></a>Připojení a spuštění dotazů

Můžete si vybrat mezi několika různými způsoby připojení ke clusteru interaktivních dotazů a spouštět dotazy pomocí konektoru skladu Hive. Podporované metody zahrnují následující nástroje:

* [jiskra-shell](../spark/apache-spark-shell.md)
* PySpark (PySka
* jiskra-předložit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Všechny příklady uvedené v tomto článku budou provedeny prostřednictvím jiskry.All examples provided in this article will be executed through spark-shell.

Chcete-li zahájit relaci jiskrového prostředí, postupujte takto:

1. SSH do headnode pro cluster Apache Spark. Další informace o připojení ke clusteru pomocí SSH najdete v [tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Chcete-li spustit prostředí jiskry, zadejte následující příkaz:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Zobrazí se uvítací zpráva `scala>` a výzva, kde můžete zadat příkazy.

1. Po spuštění jiskry-prostředí, Hive warehouse connector instance lze spustit pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Připojení a spuštění dotazů v clusterech balíčků zabezpečení rozlehlé sítě (ESP)

Balíček zabezpečení rozlehlé sítě (ESP) poskytuje funkce na podnikové úrovni, jako je ověřování na základě služby Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro clustery Apache Hadoop v Azure HDInsight. Další informace o protokolu ESP naleznete [v tématu Použití balíčku enterprise security package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH do headnode pro cluster Apache Spark. Další informace o připojení ke clusteru pomocí SSH najdete v [tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Zadejte `kinit` a přihlaste se pomocí uživatele domény.

1. Začněte jiskrovou skořepinu s úplným seznamem konfiguračních parametrů, jak je znázorněno níže. Všechny hodnoty ve všech velkých písmenmezi úhlovými závorkami musí být určeny na základě clusteru. Pokud potřebujete zjistit hodnoty, které chcete zadat pro některý z níže uvedených parametrů, podívejte se do části [nastavení konektoru skladu Hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Vytváření datových rámců Spark z dotazů Hive

Výsledky všech dotazů pomocí knihovny HWC jsou vráceny jako datový rámec. Následující příklady ukazují, jak vytvořit základní dotaz.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Výsledky dotazu jsou Spark DataFrames, které lze použít s knihovnami Spark, jako je MLIB a SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zápis datových rámců Spark do tabulek Hive

Spark nativně nepodporuje zápis do spravovaných tabulek ACID společnosti Hive. Pomocí HWC však můžete zapsat libovolný datový rámec do tabulky Hive. Tuto funkci můžete vidět při práci v následujícím příkladu:

1. Vytvořte tabulku `sampletable_colorado` s názvem a zadejte její sloupce pomocí následujícího příkazu:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrujte `hivesampletable` tabulku, `state` kde `Colorado`se rovná sloupec . Tento dotaz tabulky Hive je vrácen jako datový rámec Spark. Potom je datový rámec uložen v `sampletable_colorado` tabulce `write` Hive pomocí funkce.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Výsledky můžete zobrazit pomocí následujícího příkazu:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![konektor ve skladu úlu zobrazuje tabulku podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturované zápisy datových proudů

Pomocí konektoru Hive Warehouse Connector můžete pomocí streamování Spark zapisovat data do tabulek Hive.

Podle následujících kroků vytvořte příklad konektoru skladu Hive, který ingestuje data z datového proudu Spark na portu localhost 9999 do tabulky Hive.

1. Postupujte podle pokynů v části [Připojení a spuštění dotazů](#connecting-and-running-queries).

1. Začněte jiskřicí proud následujícím příkazem:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Vygenerujte data pro stream Spark, který jste vytvořili, a to takto:
    1. Otevřete druhou relaci SSH ve stejném clusteru Spark.
    1. Na příkazovém `nc -lk 9999`řádku zadejte příkaz . Tento příkaz používá nástroj netcat k odesílání dat z příkazového řádku do zadaného portu.

1. Vraťte se k první relaci SSH a vytvořte novou tabulku Hive pro uložení streamovaných dat. Na jiskrovém prostředí zadejte následující příkaz:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Potom zapište streamovaná data do nově vytvořené tabulky pomocí následujícího příkazu:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` Možnosti `database` a musí být aktuálně nastaveny ručně kvůli známému problému v Apache Spark. Další informace o tomto problému naleznete v tématu [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Vraťte se do druhé relace SSH a zadejte následující hodnoty:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Vraťte se k první relaci SSH a poznamenejte si stručnou aktivitu. K zobrazení dat použijte následující příkaz:

    ```scala
    hive.table("stream_table").show()
    ```

Pomocí **kombinace kláves Ctrl + C** můžete zastavit netcat v druhé relaci SSH. Slouží `:q` k ukončení jiskry-shell na první relaci SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpečení dat v clusterech Spark ESP

1. Vytvořte `demo` tabulku s ukázkovými daty zadáním následujících příkazů:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zobrazení obsahu tabulky pomocí následujícího příkazu. Před použitím zásady `demo` se v tabulce zobrazí celý sloupec.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![ukázková tabulka před použitím zásad rangeru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Použijte zásadu maskování sloupců, která zobrazuje pouze poslední čtyři znaky sloupce.  
    1. Přejděte na verzi uživatelského rozhraní správce rangeru na adrese `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klikněte na službu Hive pro váš cluster v části **Hive**.
        ![správce služeb ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klikněte na kartu **Maskování** a pak **přidat nové zásady**

        ![seznam zásad zásad podregistrů rangerových spojů](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Zadejte požadovaný název zásady. Vybrat databázi: **Výchozí**tabulka , Hive: **ukázka**, Sloupec Hive: **název**, Uživatel: **rsadmin2**, Typy přístupu: **vybrat**a **Částečná maska: zobrazit poslední 4** z nabídky Vybrat **možnost maskování.** Klikněte na **Přidat**.
                ![vytvořit zásadu](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Znovu zobrazit obsah tabulky. Po použití zásadranger, můžeme vidět pouze poslední čtyři znaky sloupce.

    ![demo tabulka po použití zásad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Další kroky

* [Použití Interactive Query se službou HDInsight](./apache-interactive-query-get-started.md).
* [Příklady interakce se konektorem Hive Warehouse Connector pomocí Zeppelin, Livy, spark-submit a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
