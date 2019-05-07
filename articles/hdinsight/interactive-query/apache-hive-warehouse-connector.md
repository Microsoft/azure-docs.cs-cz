---
title: Integrujte Apache Spark a Apache Hivu se službou konektoru sklad Hive
description: Zjistěte, jak integrovat konektoru sklad Hive v Azure HDInsight Apache Spark a Apache Hive.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b2b3d1ac0a7c0e917f87be1dd131120f63a70f8e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142812"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrujte Apache Spark a Apache Hivu se službou konektoru sklad Hive

Apache Hive skladu konektoru (HWC) je knihovna, která umožňuje snadněji pracovat s Apache Spark a Apache Hive, díky podpoře úlohy, například přesun dat mezi Spark datových rámců a tabulek Hive a taky směrování Spark Streamovat data do tabulek Hive. Hive skladu konektor funguje jako most mezi Spark a Hive. Podporuje Scala, Java nebo Python pro vývoj.

Konektor Hive Warehouse umožňuje využívat jedinečné funkce Hive a Sparku a vytvářejte výkonné aplikace pro velké objemy dat. Apache Hive nabízí podporu pro databázové transakce, které jsou ten, konzistentní, izolované a trvalý (kyseliny). Další informace o kyseliny a transakcí v Hive najdete v tématu [Hive transakce](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive také nabízí podrobné zabezpečení ovládání pomocí Apache Ranger a nízkou latencí Analytical Processing není k dispozici v Apache Spark.

Apache Spark má o strukturované streamování rozhraní API, která poskytuje funkce streamování není k dispozici v Apache Hive. Začátek s Hortonworks Data Platform (HDP) 3.0, Apache Spark a Apache Hive mít samostatné metaúložiště, což může ztěžovat vzájemná funkční spolupráce. Konektor Hive Warehouse usnadňuje používat Spark a Hive společně. Knihovna HWC načítá data z procesy démon LLAP do Spark prováděcí moduly paralelně, takže efektivní a škálovatelnost než použití standardní JDBC propojení Spark a Hive.

![Architektura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Zde jsou některé operace podporována konektorem sklad Hive:

* Popis tabulky
* Vytváření tabulek pro data ve formátu ORC
* Výběr dat Hive a načítání datový rámec
* Zápis datový rámec k Hivu ve službě batch
* Spouštění příkazu update Hive
* Čtení dat tabulky Hive, transformace ve Sparku a zápis do nové tabulky Hive
* Zapisuje se stream datového rámce nebo Spark do Hive s využitím HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Instalace konektoru sklad Hive

Použijte následující postup instalace konektoru sklad Hive mezi clusteru Sparku a interaktivního dotazu v Azure HDInsight:

1. Vytvoření clusteru HDInsight Spark 4.0 pomocí webu Azure portal s účtem služby storage a vlastní virtuální síť Azure. Informace týkající se vytvoření clusteru ve službě Azure virtual network najdete v tématu [HDInsight přidat do existující virtuální síť](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Vytvoření clusteru HDInsight interaktivní dotazy (LLAP) 4.0 pomocí webu Azure portal pomocí stejného účtu úložiště a virtuální síť Azure jako clusteru Spark.
1. Zkopírujte obsah `/etc/hosts` souboru na headnode0 svého clusteru Interactive Query `/etc/hosts` souboru na headnode0 váš cluster Spark. Tento krok vám umožní váš cluster Spark na přeložit IP adresy uzlů v clusteru Interactive Query. Zobrazení obsahu aktualizovaný soubor s `cat /etc/hosts`. Výstup by měl vypadat podobně jako informace zobrazené na snímku obrazovky níže.

    ![v souboru hostitelů](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Konfigurace nastavení clusteru Sparku provedením následujících kroků: 
    1. Přejděte na web Azure portal, vyberte clustery HDInsight a potom klikněte na název clusteru.
    1. Na pravé straně v části **řídicí panely clusteru**vyberte **Ambari domácí**.
    1. Ve webové uživatelské rozhraní Ambari, klikněte na tlačítko **SPARK2** > **CONFIGS** > **vlastní spark2 – výchozí hodnoty**.

        ![Konfigurace Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Nastavte `spark.hadoop.hive.llap.daemon.service.hosts` na stejnou hodnotu jako vlastnost **název aplikace funkcí LLAP** pod **Advanced hive interaktivní env**. Například `@llap0`.

    1. Nastavte `spark.sql.hive.hiveserver2.jdbc.url` na připojovací řetězec JDBC, která se připojí k serveru Hiveserver2 v clusteru Interactive Query. Připojovací řetězec pro váš cluster bude vypadat jako následující identifikátor URI. `CLUSTERNAME` je název vašeho clusteru Spark a `user` a `password` parametry jsou nastavené na správné hodnoty pro váš cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > Adresa URL JDBC by měl obsahovat přihlašovací údaje pro připojení k serveru Hiveserver2 včetně uživatelského jména a hesla.

    1. Nastavte `spark.datasource.hive.warehouse.load.staging.dir` vhodný pracovní adresář HDFS kompatibilní. Pokud máte dva různé clustery, pracovní adresář by měl být složka v pracovním adresáři účet úložiště clusteru LLAP tak, aby měl přístup k němu HiveServer2. Například `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` kde `STORAGE_ACCOUNT_NAME` je název používaný v clusteru, účet úložiště a `STORAGE_CONTAINER_NAME` je název kontejneru úložiště.

    1. Nastavte `spark.datasource.hive.warehouse.metastoreUri` hodnotou identifikátoru URI clusteru Interactive Query metastore. Chcete-li najít metastoreUri clusteru LLAP, vyhledejte **hive.metastore.uris** vlastnosti v uživatelském rozhraní Ambari pro vaše LLAP clusteru v rámci **Hive** > **UPŘESNIT**  >  **Obecné**. Hodnota bude vypadat podobně jako následující identifikátor URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Nastavte `spark.security.credentials.hiveserver2.enabled` k `false` YARN klienta nasazení režimu.
    1. Nastavte `spark.hadoop.hive.zookeeper.quorum` na kvorum Zookeeper clusteru LLAP. Chcete-li najít kvorum Zookeeper clusteru LLAP, vyhledejte **hive.zookeeper.quorum** vlastnosti v uživatelském rozhraní Ambari pro vaše LLAP clusteru v rámci **Hive** > **UPŘESNIT**  >  **Advanced hive lokality**. Hodnota bude vypadat podobně jako následující řetězec:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Pokud chcete otestovat konfiguraci konektoru sklad Hive, postupujte podle kroků v části [připojení a spuštění dotazů](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Pomocí konektoru sklad Hive

### <a name="connecting-and-running-queries"></a>Připojení a spuštění dotazů

Můžete zvolit mezi několika různými způsoby připojení k vašemu clusteru Interactive Query a spouštění dotazů pomocí konektoru sklad Hive. Podporované metody jsou následující nástroje:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* skriptu Spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Všechny příklady uvedené v tomto článku se spustí do prostředí spark.

Spustit relaci prostředí sparku, proveďte následující kroky:

1. Připojte se přes SSH hlavního uzlu clusteru. Další informace o připojení ke clusteru pomocí SSH najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Přejděte do správného adresáře tak, že zadáte `cd /usr/hdp/current/hive_warehouse_connector` nebo zadat úplnou cestu pro všechny soubory jar použít jako parametry v příkazovém prostředí spark.
1. Zadejte následující příkaz pro spuštění prostředí sparku:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Zobrazí se zobrazení uvítací zprávy a `scala>` řádku, kde můžete zadávat příkazy.

1. Po spuštění prostředí spark, můžete spustit instanci konektoru sklad Hive pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Připojení a spouštění dotazů v clusterech Enterprise Security Package (ESP)

Balíček zabezpečení podniku (ESP) poskytuje funkce na podnikové úrovni, jako je ověřování pomocí služby Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro clustery systému Apache Hadoop v Azure HDInsight. Další informace o ESP najdete v tématu [Úvod do Apache Hadoop zabezpečení s balíčkem Enterprise Security Package](../domain-joined/apache-domain-joined-introduction.md).

1. Proveďte počáteční kroky 1 a 2 v rámci [připojení a spuštění dotazů](#connecting-and-running-queries).
1. Typ `kinit` a přihlaste se jako uživatel domény.
1. Spuštění prostředí spark se na seznam všech konfigurační parametry, jak je znázorněno níže. Všechny hodnoty velkými písmeny mezi ostrých závorek je třeba zadat podle vašeho clusteru. Pokud potřebujete zjistit hodnoty tak, aby vstup pro některý z níže uvedených parametrů, naleznete v části [instalace konektoru sklad Hive](#hive-warehouse-connector-setup).:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Vytváření datových rámců Spark z dotazů Hive

Výsledky všech dotazů pomocí knihovny HWC se vrátí jako datový rámec. Následující příklady ukazují, jak vytvořit základní dotaz.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Výsledky dotazu jsou DataFrames Spark, který je možné pomocí knihovny Spark jako MLIB a SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zápis datových rámců Sparku do tabulek Hive

Spark nativně nepodporuje zápis do spravované odpovídající zásadám ACID tabulek Hive pro. Pomocí HWC, ale můžete napsat na libovolný datový rámec do tabulky Hive. Zobrazí se tato funkce v práci v následujícím příkladu:

1. Vytvořte tabulku s názvem `sampletable_colorado` a určit její sloupce pomocí následujícího příkazu:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Tabulku můžete filtrovat `hivesampletable` kde sloupci `state` rovná `Colorado`. Tento dotaz tabulku Hive se vrátí jako Spark DataFrame. Pak datový rámec se uloží do tabulky Hive `sampletable_colorado` pomocí `write` funkce.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Zobrazí výsledné tabulky v následujícím snímku obrazovky.

![zobrazit výslednou tabulku](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturované streamování zápisy

Pomocí konektoru sklad Hive, můžete použít k zápisu dat do tabulek Hive streamování Sparku.

Postupujte podle pokynů můžete vytvořit příklad konektor sklad Hive, které ingestují data z datového proudu Spark na port místního hostitele 9999 do tabulky Hive.

1. Otevřete terminál na svém clusteru Spark.
1. Začněte datového proudu spark pomocí následujícího příkazu:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Generování dat pro datový proud Spark, který jste vytvořili, pomocí následujících kroků:
    1. Otevřete další terminálu na stejném clusteru Spark.
    1. Na příkazovém řádku zadejte `nc -lk 9999`. Tento příkaz používá nástroj netcat k odesílání dat z příkazového řádku pro zadaný port.
    1. Zadejte slova, která byste chtěli Spark stream pro ingestování, následovaný zalomením.
        ![vstupní data do datového proudu spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Vytvořte novou tabulku Hive pro streamovaná data. V prostředí sparku zadejte následující příkazy:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Zápis datových proudů dat do nově vytvořenou databázi pomocí následujícího příkazu:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` a `database` možnosti musí být aktuálně ručně nastavit kvůli známému problému v Apache Spark. Další informace o tomto problému najdete v tématu [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Můžete zobrazit data vložená do tabulky pomocí následujícího příkazu:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Zabezpečení dat v clusterech Spark ESP

1. Vytvořit tabulku `demo` s ukázkovými daty tak, že zadáte následující příkazy:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zobrazte obsah pomocí následujícího příkazu. Před použitím zásad, `demo` tabulka ukazuje celý sloupec.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ukázka tabulky před použitím zásad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Použijte sloupec maskování zásadu, která zobrazuje jenom poslední čtyři znaky sloupce.  
    1. Přejděte na uživatelské rozhraní správce Ranger na `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klikněte na podregistr služby pro váš cluster pod **Hive**.
        ![Ukázka tabulky před použitím zásad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klikněte na **maskování** kartu a potom **přidat nové zásady** ![seznam zásad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Zadejte název požadované zásady. Vyberte databázi: **Výchozí**, tabulky Hive: **ukázka**, sloupec Hive: **název**, uživatel: **rsadmin2**, typy přístupu: **vyberte**a **Částečné masky: Zobrazit poslední 4** z **možnost vybrat maskování** nabídky. Klikněte na tlačítko **Add** (Přidat).
                ![seznam zásad](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Znovu zobrazte obsah v tabulce. Po použití zásad ranger, můžeme vidět jenom poslední čtyři znaky sloupce.

    ![Ukázka tabulky po použití zásad ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Další postup

* [Pomocí HDInsight Interactive Query](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Příklady interakci s konektorem Hive skladu použití Zeppelin Livy, skriptu spark-submit a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
