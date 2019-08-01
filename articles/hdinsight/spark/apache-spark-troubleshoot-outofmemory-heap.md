---
title: Výjimka OutOfMemoryError pro Apache Spark ve službě Azure HDInsight
description: Výjimka OutOfMemoryError pro Apache Spark ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620984"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Scénář: Výjimka OutOfMemoryError pro Apache Spark ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

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

## <a name="cause"></a>Příčina

Nejpravděpodobnější příčinou této výjimky je nedostatek paměti haldy. Vaše aplikace Spark vyžaduje při spuštění jako vykonavatelů nebo ovladačů dostatečnou paměť haldy Java Virtual Machines (JVM).

## <a name="resolution"></a>Řešení

1. Určete maximální velikost dat, která bude aplikace Spark zpracovávat. Vyhodnotit odhad velikosti na základě maximální velikosti vstupních dat, zprostředkujících dat vytvořených transformací vstupních dat a výstupními daty vytvořenými dalšími transformacemi mezilehlých dat. Pokud počáteční odhad není dostačující, zvětšete velikost mírně a Iterujte, dokud neproběhne Chyba paměti.

1. Ujistěte se, že cluster HDInsight, který se má použít, má dostatek prostředků v podobě paměti a jader k obsluze aplikace Spark. To se dá určit zobrazením části Cluster Metrics (Metriky clusteru) uživatelského rozhraní YARN clusteru, kde najdete hodnoty využité paměti vs. celkové paměti a využitých virtuálních jader vs. celkových virtuálních jader.

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

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)

* [Ladění aplikace Spark v clusterech HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
