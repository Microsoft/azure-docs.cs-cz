---
title: Integrace Apache Spark a Apache Hive s konektorem skladu podregistru
description: Přečtěte si, jak integrovat Apache Spark a Apache Hive pomocí konektoru skladu pro podregistr v Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 98b97abf9fe0d8e71f93022dcbfeff0a5339d5a1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983085"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrace Apache Spark a Apache Hive s konektorem skladu podregistru

Konektor Apache Hive Warehouse (umožní) je knihovna, která umožňuje snadněji pracovat s Apache Spark a Apache Hive tím, že podporuje úkoly, jako je přesun dat mezi datovými tabulkami Sparku a tabulkami podregistru a také nasměrování dat pro streamování Sparku do tabulek podregistru. Konektor datového skladu pro podregistr funguje jako most mezi Sparkem a podregistrem. Podporuje Scala, Java a Python pro vývoj.

Konektor skladiště pro podregistr umožňuje využívat jedinečné funkce podregistru a Sparku k vytváření výkonných aplikací pro velké objemy dat. Apache Hive nabízí podporu pro databázové transakce, které jsou atomické, konzistentní, izolované a trvalé (KYSELé). Další informace o KYSELINě a transakcích v podregistru najdete v tématu [transakce podregistru](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Podregistr také nabízí podrobné kontrolní mechanismy zabezpečení prostřednictvím Apache Ranger a analytické zpracování s nízkou latencí, které není dostupné v Apache Spark.

Apache Spark má strukturované rozhraní API pro streamování, které poskytuje možnosti streamování, které nejsou dostupné v Apache Hive. Od HDInsight 4,0 Apache Spark 2.3.1 a Apache Hive 3.1.0 samostatné metaúložiště, což může zajistit obtížnou interoperabilitu. Konektor pro skladiště podregistru usnadňuje používání Sparku a úlů společně. Knihovna umožní načítá data ze LLAP démonů do paralelního vykonavatele paralelně, takže je efektivnější a škálovatelnější než použití standardního připojení JDBC z Sparku do podregistru.

![Architektura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

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

1. Vytvořte cluster HDInsight Spark 4,0 pomocí Azure Portal s účtem úložiště a vlastní virtuální sítí Azure. Informace o vytvoření clusteru ve virtuální síti Azure najdete v tématu [Přidání HDInsight do existující virtuální sítě](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).
1. Vytvořte cluster HDInsight Interactive Query (LLAP) 4,0 pomocí Azure Portal se stejným účtem úložiště a Azure Virtual Network jako cluster Spark.
1. Zkopírujte obsah `/etc/hosts` souboru do headnode0 vašeho clusteru interaktivních dotazů `/etc/hosts` do souboru na headnode0 clusteru Spark. Tento krok umožní vašemu clusteru Spark přeložit IP adresy uzlů v clusteru interaktivních dotazů. Zobrazit obsah aktualizovaného souboru pomocí `cat /etc/hosts`. Výstup by měl vypadat nějak takto, jak je znázorněno na snímku obrazovky níže.

    ![zobrazení souboru hostitelů](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Nakonfigurujte nastavení clusteru Spark pomocí následujících kroků: 
    1. Přejděte na Azure Portal, vyberte clustery HDInsight a pak klikněte na název clusteru.
    1. Na pravé straně v části **řídicí panely clusteru**vyberte **Ambari Home (domů**).
    1. Ve webovém uživatelském rozhraní Ambari klikněte na **SPARK2** > **Konfigurace** > **vlastní SPARK2 – výchozí**.

        ![Konfigurace Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Nastavte `spark.hadoop.hive.llap.daemon.service.hosts` na stejnou hodnotu jako **podregistr Property. llap. démon. Service. Hosts** v umístění * * rozšířený podregistr-Interactive-site * *. Například `@llap0`.

    1. Nastavte `spark.sql.hive.hiveserver2.jdbc.url` na připojovací řetězec JDBC, který se připojí k Hiveserver2 v clusteru interaktivních dotazů. Připojovací řetězec pro váš cluster bude vypadat jako URI níže. `CLUSTERNAME`je název vašeho clusteru Spark a `user` parametry a `password` jsou nastavené na správné hodnoty pro váš cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > Adresa URL JDBC by měla obsahovat přihlašovací údaje pro připojení k Hiveserver2, včetně uživatelského jména a hesla.

    1. Nastavte `spark.datasource.hive.warehouse.load.staging.dir` vhodný přípravný adresář kompatibilní s HDFS. Pokud máte dva různé clustery, pracovní adresář by měl být složka v pracovním adresáři účtu úložiště LLAP clusteru, aby k němu měl přístup HiveServer2. Například, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` kde `STORAGE_ACCOUNT_NAME` je název účtu úložiště používaného clusterem a `STORAGE_CONTAINER_NAME` je název kontejneru úložiště.

    1. Nastavte `spark.datasource.hive.warehouse.metastoreUri` s hodnotou identifikátoru URI metastore clusteru interaktivních dotazů. Pokud chcete najít metastoreUri pro svůj cluster LLAP, vyhledejte vlastnost **podregistr. metastore. URI** v uživatelském rozhraní Ambari pro váš cluster LLAP v části **podregistr** > **Advanced** > **General General**. Hodnota bude vypadat přibližně jako následující identifikátor URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Nastavte `spark.security.credentials.hiveserver2.enabled` na`false` režim nasazení klienta příz.
    1. Nastavte `spark.hadoop.hive.zookeeper.quorum` na kvorum Zookeeper clusteru LLAP. Pokud chcete najít Zookeeper kvora pro cluster LLAP, vyhledejte vlastnost **podregistr. Zookeeper. kvora** v uživatelském rozhraní Ambari pro váš cluster LLAP v části **podregistr** > **Advanced** > **Advanced Advanced podregistr-site**. Hodnota bude vypadat přibližně jako v následujícím řetězci:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Chcete-li otestovat konfiguraci konektoru datového skladu podregistru, postupujte podle kroků v části [připojení a spuštění dotazů](#connecting-and-running-queries).

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

1. Přihlaste se přes SSH do hlavnímu uzlu pro váš cluster. Další informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Přejděte do správného adresáře tak, že `cd /usr/hdp/current/hive_warehouse_connector` zadáte nebo zadáte úplnou cestu ke všem souborům jar použitým jako parametry v příkazu Spark-Shell.
1. Zadejte následující příkaz, který spustí prostředí Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Zobrazí se uvítací zpráva a `scala>` výzva, kde můžete zadat příkazy.

1. Po spuštění Spark-Shell můžete instanci konektoru skladu podregistru spustit pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Připojení a spuštění dotazů v clusterech Balíček zabezpečení podniku (ESP)

Balíček zabezpečení podniku (ESP) poskytuje podnikové funkce, jako je ověřování založené na službě Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro Apache Hadoop clustery ve službě Azure HDInsight. Další informace o protokolu ESP najdete v tématu [použití balíček zabezpečení podniku ve službě HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Postupujte podle prvních kroků 1 a 2 v části [připojení a spuštění dotazů](#connecting-and-running-queries).
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

2. Vyfiltruje `hivesampletable` tabulku, ve `state` které se `Colorado`sloupec rovná. Tento dotaz na tabulku podregistru se vrátí jako datový rámec Spark. Pak je datový rámec uložen v tabulce `sampletable_colorado` podregistr `write` pomocí funkce.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Výslednou tabulku můžete zobrazit na snímku obrazovky níže.

![Zobrazit výslednou tabulku](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturované zápisy streamování

Pomocí konektoru Warehouse pro podregistr můžete pomocí streamování Spark zapisovat data do tabulek podregistru.

Pomocí následujících kroků vytvořte příklad konektoru skladu s podmnožinou, který ingestuje data z datového proudu Spark na portu hostitele 9999 do tabulky podregistru.

1. Otevřete terminál v clusteru Spark.
1. Spusťte datový proud Spark pomocí následujícího příkazu:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Pomocí následujících kroků vygenerujte data pro datový proud Spark, který jste vytvořili:
    1. Otevřete jiného terminálu na stejném clusteru Spark.
    1. Do příkazového řádku zadejte `nc -lk 9999`. Tento příkaz používá nástroj Netcat k posílání dat z příkazového řádku na zadaný port.
    1. Zadejte slova, která chcete, aby datový proud Spark sledoval a následovaný návratem na začátek řádku.
        ![vstupní data do Spark Stream](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Vytvořte novou tabulku podregistru pro ukládání dat streamování. V prostředí Spark zadejte následující příkazy:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Zapište streamovaná data do nově vytvořené tabulky pomocí následujícího příkazu:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Možnosti `metastoreUri` a`database` se musí aktuálně nastavit ručně z důvodu známého problému v Apache Spark. Další informace o tomto problému naleznete v [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Data vložená do tabulky můžete zobrazit pomocí následujícího příkazu:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpečení dat v clusterech Spark ESP

1. Vytvořte tabulku `demo` s ukázkovými daty zadáním následujících příkazů:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zobrazte obsah tabulky pomocí následujícího příkazu. Před použitím této zásady `demo` tabulka zobrazuje celý sloupec.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ukázková tabulka před použitím zásad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Použijte zásadu maskování sloupců, která zobrazuje jenom poslední čtyři znaky sloupce.  
    1. Přejít do uživatelského rozhraní správce Ranger na `https://CLUSTERNAME.azurehdinsight.net/ranger/`adrese.
    1. Klikněte na podregistr Service pro váš cluster v **podregistru**.
        ![Ukázková tabulka před použitím zásad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klikněte na kartu **maskování** a pak na **Přidat nový** ![seznam zásad zásad.](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Zadejte požadovaný název zásad. Vybrat databázi: **Výchozí**, tabulka podregistru: **Ukázka**, sloupec podregistru: **název**, uživatel: **Rsadmin2**, typy přístupu: **Vybrat**a **částečná maska: Zobrazit poslední 4** v nabídce **možností výběru maskování** . Klikněte na **Přidat**.
                ![seznam zásad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Znovu zobrazte obsah tabulky. Po použití zásad Ranger uvidíme jenom poslední čtyři znaky sloupce.

    ![Ukázková tabulka po použití zásad Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Další postup

* [Použití Interactive Query se službou HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started).
* [Příklady interakce s konektorem skladiště v podregistru pomocí Zeppelin, Livy, Spark-Submit a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
