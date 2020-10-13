---
title: Apache Spark operace podporované konektorem skladu podregistru ve službě Azure HDInsight
description: Přečtěte si o různých schopnostech konektoru datového skladu v Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: ed3dbe4cb5b9d8b50c028a68feeded170130bfb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085833"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Spark operace podporované konektorem skladu podregistru ve službě Azure HDInsight

Tento článek ukazuje operace založené na Sparku, které podporuje konektor Warehouse pro podregistr (umožní). Všechny níže uvedené příklady budou spouštěny prostřednictvím prostředí Apache Spark.

## <a name="prerequisite"></a>Požadavek

Dokončete kroky pro [instalaci konektoru skladu podregistru](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Začínáme

Chcete-li spustit relaci Spark-Shell, proveďte následující kroky:

1. Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru Apache Spark. Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte následující příkaz, abyste si poznamenali `hive-warehouse-connector-assembly` verzi:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Níže uvedený kód upravte s `hive-warehouse-connector-assembly` výše uvedenou verzí. Pak spusťte příkaz pro spuštění prostředí Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Po spuštění Spark-Shell můžete instanci konektoru skladu podregistru spustit pomocí následujících příkazů:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Vytváření Spark dataframes pomocí dotazů na podregistry

Výsledky všech dotazů využívajících knihovnu umožní jsou vráceny jako datový rámec. Následující příklady ukazují, jak vytvořit základní dotaz na podregistr.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Výsledky dotazu jsou Spark dataframes, které lze použít s knihovnami Spark jako MLIB a SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Zápis datového rámce Spark do tabulek podregistru

Spark nebude nativně podporovat zápis do spravovaných tabulek KYSELosti podregistru. Pomocí umožní ale můžete napsat libovolný datový rámec do tabulky podregistru. Tuto funkci můžete zobrazit v práci v následujícím příkladu:

1. Vytvořte tabulku s názvem `sampletable_colorado` a určete její sloupce pomocí následujícího příkazu:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Vyfiltruje tabulku `hivesampletable` , ve které se sloupec `state` rovná `Colorado` . Tento dotaz na podregistr vrátí objekt ANS sis, který je uložený v tabulce podregistru, `sampletable_colorado` pomocí `write` funkce.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Výsledky zobrazíte pomocí následujícího příkazu:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![konektor datového skladu pro zobrazení tabulky podregistru](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>Strukturované zápisy streamování

Pomocí konektoru Warehouse pro podregistr můžete pomocí streamování Spark zapisovat data do tabulek podregistru.

> [!IMPORTANT]
> Strukturované zápisy streamování nejsou v clusterech Spark 4,0 s povoleným protokolem ESP podporovány.

Dodržujte následující postup a ingestujte data ze streamu Spark na portu hostitele 9999 do tabulky s podregistry prostřednictvím. Konektor skladu pro podregistr

1. V otevřeném prostředí Spark spusťte datový proud Spark pomocí následujícího příkazu:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Pomocí následujících kroků vygenerujte data pro datový proud Spark, který jste vytvořili:
    1. Otevřete druhou relaci SSH na stejném clusteru Spark.
    1. Do příkazového řádku zadejte `nc -lk 9999` . Tento příkaz používá nástroj Netcat k posílání dat z příkazového řádku na zadaný port.

1. Vraťte se k první relaci SSH a vytvořte novou tabulku podregistru pro ukládání dat streamování. V prostředí Spark zadejte následující příkaz:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Pak zapište streamovaná data do nově vytvořené tabulky pomocí následujícího příkazu:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`Možnosti a se `database` musí aktuálně nastavit ručně z důvodu známého problému v Apache Spark. Další informace o tomto problému naleznete v [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

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

Pomocí **kombinace kláves CTRL + C** zastavte NetCat v druhé relaci SSH. Pomocí `:q` příkazu můžete v první relaci SSH ukončit prostředí Spark.

## <a name="next-steps"></a>Další kroky

* [Integrace HWC s Apache Sparkem a Apache Hivem](./apache-hive-warehouse-connector.md)
* [Použití Interactive Query se službou HDInsight](./apache-interactive-query-get-started.md).
* [Integrace HWC s Apache Zeppelinem](./apache-hive-warehouse-connector-zeppelin.md)
