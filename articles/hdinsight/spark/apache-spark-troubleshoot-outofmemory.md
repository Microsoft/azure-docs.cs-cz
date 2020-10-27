---
title: OutOfMemoryError výjimky pro Apache Spark ve službě Azure HDInsight
description: Různé výjimky OutOfMemoryError pro cluster Apache Spark ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 156d3ce4b7f8b6eaa297f2cddd0d5a93f382f78e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547278"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError výjimky pro Apache Spark ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scénář: OutOfMemoryError výjimka pro Apache Spark

### <a name="issue"></a>Problém

Vaše aplikace Apache Spark se nezdařila s neošetřenou výjimkou OutOfMemoryError. Může se zobrazit chybová zpráva podobná této:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Příčina

Nejpravděpodobnější příčinou této výjimky je, že virtuálním počítačům Java (JVMs) není přidělena dostatečná paměť haldy. Tyto JVMs jsou spouštěny jako vykonavatelé nebo ovladače jako součást aplikace Apache Spark.

### <a name="resolution"></a>Řešení

1. Určete maximální velikost dat, která bude aplikace Spark zpracovávat. Vyhodnotit odhad velikosti na základě maximální velikosti vstupních dat, zprostředkujících dat vytvořených transformací vstupních dat a výstupními daty vytvořenými dalšími transformacemi mezilehlých dat. Pokud počáteční odhad není dostačující, zvětšete velikost mírně a Iterujte, dokud neproběhne Chyba paměti.

1. Ujistěte se, že cluster HDInsight, který se má použít, má dostatek prostředků v podobě paměti a jader k obsluze aplikace Spark. To lze určit zobrazením oddílu metriky clusteru v uživatelském rozhraní PŘÍZe clusteru pro hodnoty **využité paměti** vs. **Celková velikost paměti** a virtuální jádra a **Celkový počet** **využitých** dat. virtuální jádra.

    ![zobrazení základní paměti příze](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Nastavte následující konfigurace Sparku na odpovídající hodnoty. Vyrovnávat požadavky na aplikace s dostupnými prostředky v clusteru. Tyto hodnoty by neměly překročit 90% dostupné paměti a jader zobrazených pomocí PŘÍZe a měly by taky splňovat minimální požadavky na paměť aplikace Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Celková paměť využívaná všemi prováděcími moduly =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Celková paměť využívaná ovladačem =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scénář: Chyba prostoru v haldě Java při pokusu o otevření serveru historie Apache Spark

### <a name="issue"></a>Problém

Při otevírání událostí na serveru historie Sparku se zobrazí následující chyba:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Příčina

Tato chyba je často způsobena nedostatkem prostředků při otevírání rozsáhlých souborů Spark-Event. Velikost haldy Sparku je ve výchozím nastavení nastavená na 1 GB, ale velké soubory událostí Spark můžou vyžadovat víc než tento postup.

Pokud chcete ověřit velikost souborů, které se pokoušíte načíst, můžete provést následující příkazy:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Řešení

Velikost paměti serveru Spark můžete zvýšit úpravou `SPARK_DAEMON_MEMORY` vlastnosti v konfiguraci Sparku a restartováním všech služeb.

To můžete provést v uživatelském rozhraní prohlížeče Ambari, a to tak, že vyberete oddíl Spark2/config/Advanced Spark2-env.

![Oddíl Advanced spark2-ENV](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Přidejte následující vlastnost pro změnu paměti serveru Spark historie z 1G na 4G: `SPARK_DAEMON_MEMORY=4g` .

![Spark – vlastnost](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Ujistěte se, že jste všechny zasažené služby restartovali z Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scénář: Livy Server se nepovede spustit v clusteru Apache Spark.

### <a name="issue"></a>Problém

Livy Server nelze spustit v Apache Spark [(Spark 2,1 on Linux (HDI 3,6)]. Při pokusu o restartování výsledků z protokolů Livy se zobrazí následující chybová zpráva:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Příčina

`java.lang.OutOfMemoryError: unable to create new native thread` Hlavní operační systém nemůže přiřadit více nativních vláken pro JVMs. Potvrzuje, že je tato výjimka způsobena porušením limitu počtu vláken pro procesy.

Při neočekávaném ukončení serveru Livy se také ukončí všechna připojení ke clusterům Spark, což znamená, že všechny úlohy a související data budou ztracena. V rámci mechanismu obnovení relace HDP 2,6 Livy ukládá podrobnosti o relaci v Zookeeper po obnovení serveru Livy.

Při odeslání velkého počtu úloh prostřednictvím Livy jako součást vysoké dostupnosti pro Livy server ukládá tyto stavy relací v ZK (v clusterech HDInsight) a obnovuje tyto relace při restartování služby Livy. Při restartování po neočekávaném ukončení vytvoří Livy jedno vlákno na relaci a tato hodnota sestaví určitý počet relací na obnovení, což způsobí, že se vytváří příliš mnoho vláken.

### <a name="resolution"></a>Řešení

Všechny položky odstraňte pomocí kroků popsaných níže.

1. Získat IP adresu uzlů Zookeeper pomocí

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Výše uvedený příkaz, který uvádí všechny uzly Zookeeper pro můj cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Získejte všechny IP adresy uzlů Zookeeper pomocí nástroje test nebo se můžete připojit k Zookeeper z hlavnímu uzlu pomocí ZK názvu.

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Po připojení k Zookeeper spusťte následující příkaz, který vypíše všechny relace, které se pokusily o restartování.

    1. Ve většině případů se může jednat o seznam více než 8000 relací. ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Následující příkaz umožňuje odebrat všechny relace, které mají být obnoveny. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Počkejte, než se výše uvedený příkaz dokončí, a kurzorem vraťte výzvu a pak restartujte službu Livy z Ambari, která by měla být úspěšná.

> [!NOTE]
> `DELETE` relace Livy po dokončení jejího provádění. Relace Livy Batch se neodstraní automaticky hned po dokončení aplikace Spark, což je záměrné. Relace Livy je entita vytvořená požadavkem POST proti serveru Livy REST. `DELETE`K odstranění této entity je potřeba volání. Nebo bychom měli počkat na navázání GC.

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)

* [Ladění aplikace Spark v clusterech HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).