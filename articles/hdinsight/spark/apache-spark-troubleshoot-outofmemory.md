---
title: Výjimky outOfMemoryError pro Apache Spark v Azure HDInsight
description: Různé výjimky OutOfMemoryError pro cluster Apache Spark v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271964"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Výjimky outOfMemoryError pro Apache Spark v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scénář: Výjimka OutOfMemoryError pro Apache Spark

### <a name="issue"></a>Problém

Vaše aplikace Apache Spark se nezdařila s neošetřenou výjimkou OutOfMemoryError. Může se zobrazit chybová zpráva podobná:

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

Nejpravděpodobnější příčinou této výjimky je, že není dostatek haldy paměti je přidělena virtuálnípočítače Java (JVMs). Tyto JVM spouštějí jako vykonavatelé nebo ovladače jako součást aplikace Apache Spark.

### <a name="resolution"></a>Řešení

1. Určete maximální velikost dat, která bude aplikace Spark zpracovávat. Proveďte odhad velikosti na základě maximální velikosti vstupních dat, průběžných údajů vytvořených transformací vstupních dat a výstupních dat, která byla dále transformována mezilehlými údaji. Pokud počáteční odhad není dostačující, zvětšete velikost mírně a iterate, dokud chyby paměti odezní.

1. Ujistěte se, že cluster HDInsight, který se má použít, má dostatek prostředků v podobě paměti a jader k obsluze aplikace Spark. To lze určit zobrazením části Metriky clusteru v uzlíku YARN clusteru pro hodnoty **použité paměti** vs. celkový **počet paměti** a **použitá virtuální jádra** vs. **virtuální jádra celkem**.

    ![zobrazení paměti jádra příze](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Nastavte následující konfigurace Spark na příslušné hodnoty. Vyvažte požadavky aplikace s dostupnými prostředky v clusteru. Tyto hodnoty by neměly přesáhnout 90 % dostupné paměti a jader zobrazených v yarnu a měly by také splňovat minimální požadavek na paměť aplikace Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Celková paměť využívaná všemi vykonavateli =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Celková paměť používaná ovladačem =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scénář: Chyba místa haldy Java při pokusu o otevření serveru historie Apache Spark

### <a name="issue"></a>Problém

Při otevírání událostí na serveru Historie sparku se zobrazí následující chyba:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Příčina

Tento problém je často způsoben nedostatkem prostředků při otevírání velkých souborů jiskřící události. Velikost haldy Spark je ve výchozím nastavení nastavena na 1 GB, ale velké soubory událostí Spark mohou vyžadovat více než toto.

Chcete-li ověřit velikost souborů, které se pokoušíte načíst, můžete provést následující příkazy:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Řešení

Paměť serveru Historie Spark můžete zvýšit `SPARK_DAEMON_MEMORY` úpravou vlastnosti v konfiguraci Spark a restartováním všech služeb.

Můžete to udělat z v rámci prohlížeče Ambari UI výběrem Spark2/Config/Advanced spark2-env sekce.

![Pokročilá sekce spark2-env](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Přidejte následující vlastnost, chcete-li změnit paměť serveru `SPARK_DAEMON_MEMORY=4g`Historie spark ze 1g na 4g: .

![Spark, vlastnost](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Ujistěte se, že restartujete všechny ovlivněné služby z Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scénář: Livy Server se nepodařilo spustit v clusteru Apache Spark

### <a name="issue"></a>Problém

Livy Server nelze spustit na Apache Spark [(Spark 2.1 na Linuxu (HDI 3.6)]. Při pokusu o restartování výsledky v následujícím zásobníku chyb, z livy protokoly:

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

`java.lang.OutOfMemoryError: unable to create new native thread`zdůrazňuje, že operační program nemůže přiřadit více nativních vláken jvm. Potvrzeno, že tato výjimka je způsobena porušením limitu počtu vláken na proces.

Když Livy Server neočekávaně ukončí, všechna připojení k clustery Spark jsou také ukončeny, což znamená, že všechny úlohy a související data budou ztraceny. V HDP 2.6 session recovery mechanism byl zaveden, Livy ukládá podrobnosti relace v Zookeeper obnovit po Livy Server je zpět.

Při velké množství úloh jsou odeslány prostřednictvím Livy, jako součást vysoké dostupnosti pro Livy Server ukládá tyto stavy relace v ZK (na HDInsight clustery) a obnovit tyto relace při restartování služby Livy. Při restartování po neočekávaném ukončení livy vytvoří jedno vlákno na relaci a to hromadí určitý počet relací, které mají být obnoveny, což způsobuje vytváření příliš mnoha vláken.

### <a name="resolution"></a>Řešení

Odstraňte všechny položky pomocí kroků popsaných níže.

1. Získejte IP adresu zookeeper nodes pomocí

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Výše uvedený příkaz uvedeny všechny zookeepers pro můj cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Získat všechny IP adresy zookeeper uzlů pomocí ping Nebo se můžete také připojit k zookeeper z headnode pomocí zk jméno

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Jakmile jste připojeni k zookeeper spustit následující příkaz pro seznam všech relací, které se pokoušíte restartovat.

    1. Ve většině případů by se mohlo jedná o seznam více než 8000 relací ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Následující příkaz je odebrat všechny relace k obnovení. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Počkejte na dokončení výše uvedeného příkazu a kurzor vrátí výzvu a restartuje službu Livy z Ambari, která by měla být úspěšná.

> [!NOTE]
> `DELETE`livy po dokončení jeho provádění. Dávkové relace Livy nebudou automaticky odstraněny, jakmile se aplikace spark dokončí, což je záměrné. Relace Livy je entita vytvořená požadavkem POST proti serveru Livy Rest. K `DELETE` odstranění této entity je potřeba volání. Nebo bychom měli počkat, až začne gc.

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Ladění aplikace Spark v clusterech HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
