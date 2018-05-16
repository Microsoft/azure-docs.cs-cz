---
title: Řešení potíží Spark pomocí Azure HDInsight | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se práce s Apache Spark a Azure HDInsight.
keywords: Azure HDInsight Spark, – nejčastější dotazy, řešení potíží s průvodce, běžné problémy, konfigurace aplikace, Ambari
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 15fe5e6d2acdb8d782342b21f5db81443c44843d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Řešení potíží Spark pomocí Azure HDInsight

Další informace o hlavních problémů a jejich řešení při práci s Apache Spark datové části v Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Konfigurování aplikací Spark pomocí Ambari v clusterech

### <a name="resolution-steps"></a>Postup řešení

V prostředí HDInsight byly dříve nastavené hodnoty konfigurace pro tento postup. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu [co způsobí, že Spark výjimka OutofMemoryError aplikace](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Vyberte v seznamu clusterů **Spark2**.

    ![Vyberte cluster, ze seznamu](./media/apache-troubleshoot-spark/update-config-1.png)

2. Vyberte **konfigurací** kartě.

    ![Vyberte kartu konfigurací](./media/apache-troubleshoot-spark/update-config-2.png)

3. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**.

    ![Vyberte výchozí nastavení vlastní spark](./media/apache-troubleshoot-spark/update-config-3.png)

4. Vyhledejte nastavení hodnoty, které je nutné upravit, jako například **spark.executor.memory**. V tomto případě hodnotu **4608m** je příliš vysoká.

    ![Vyberte pole spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Nastavte hodnotu na doporučené nastavení. Hodnota **2 048 m** se doporučuje pro toto nastavení.

    ![Změňte hodnotu na 2 048 m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Uložit hodnotu a pak konfiguraci uložte. Na panelu nástrojů vyberte **Uložit**.

    ![Uložte nastavení a konfigurace](./media/apache-troubleshoot-spark/update-config-6a.png)

    Budete upozorněni, pokud všechny konfigurace vyžadují pozornost. Položky a pak vyberte **přesto pokračovat**. 

    ![Vyberte přesto pokračovat](./media/apache-troubleshoot-spark/update-config-6b.png)

    Napsat poznámku o změny konfigurace a potom vyberte **Uložit**.

    ![Zadejte poznámku o provedené změny](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Vždy, když je uložit konfiguraci, zobrazí se výzva k restartování služby. Vyberte **restartujte**.

    ![Vyberte možnost restartování](./media/apache-troubleshoot-spark/update-config-7a.png)

    Potvrďte restartování.

    ![Vyberte potvrďte restartujte](./media/apache-troubleshoot-spark/update-config-7b.png)

    Můžete zkontrolovat procesy, které jsou spuštěny.

    ![Zkontrolujte spuštěných procesů](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Můžete přidat konfigurace. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**a potom vyberte **přidat vlastnost**.

    ![Vyberte Přidat vlastnost](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definujte novou vlastnost. Pomocí dialogového okna pro konkrétní nastavení, jako je datový typ, můžete definovat vlastnosti jediné. Nebo můžete definovat více vlastností pomocí jednu definici na každý řádek. 

    V tomto příkladu **spark.driver.memory** vlastnost je definován s hodnotou **4g**.

    ![Definovat nové vlastnosti](./media/apache-troubleshoot-spark/update-config-9.png)

10. Uložte nastavení a potom restartujte službu, jak je popsáno v kroku 6 a 7.

Tyto změny jsou platné pro celý cluster, ale je možné přepsat při odesílání úlohy Spark.

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Spark v clusterech prostředí HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Konfigurování aplikací Spark v clusterech pomocí poznámkového bloku Jupyter

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu [co způsobí, že Spark výjimka OutofMemoryError aplikace](#what-causes-a-spark-application-outofmemoryerror-exception).

2. V první buňky blok Jupyter po **%% konfigurace** – direktiva, zadejte konfigurace Spark v platném formátu JSON. Podle potřeby změňte skutečnými hodnotami:

    ![Přidání konfigurace](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Spark v clusterech prostředí HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Konfigurování aplikací Spark pomocí Livy v clusterech

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu [co způsobí, že Spark výjimka OutofMemoryError aplikace](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Odesílání aplikací Spark pro Livy pomocí klienta REST jako cURL. Použijte příkaz podobný následujícímu. Podle potřeby změňte skutečnými hodnotami:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Spark v clusterech prostředí HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Konfigurování odeslání spark aplikace pomocí Spark v clusterech

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu [co způsobí, že Spark výjimka OutofMemoryError aplikace](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Spusťte prostředí spark pomocí příkazu, který je podobný následujícímu. Podle potřeby změňte skutečné hodnoty konfigurace: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Spark v clusterech prostředí HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Co způsobí, že Spark výjimka OutofMemoryError aplikace?

### <a name="detailed-description"></a>Podrobný popis

Aplikace Spark selže s následujícími typy nezachycená výjimky:

```apache
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

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

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

### <a name="probable-cause"></a>Pravděpodobná příčina

Nejpravděpodobnější příčinou této výjimky je, že není dostatek paměti haldy je přidělen k virtuálním počítačům Java (JVMs). Tyto JVMs jsou spouštěny jako vykonavatelů nebo ovladače jako součást aplikací Spark. 

### <a name="resolution-steps"></a>Postup řešení

1. Určit maximální velikost dat Spark aplikace zpracovává. Můžete nastavit odhad, založené na maximální velikost vstupních dat, mezilehlá data, která je produkovaný transformace dat vstupní a výstupní data, která je vytvořena, když aplikace je další transformace mezilehlá data. Tento proces může být iterační, pokud nelze provádět počáteční formální odhad. 

2. Ujistěte se, že cluster HDInsight, který se chystáte použít má dostatek paměti a počet jader, aby dokázala pojmout aplikací Spark zdroje. Této služby můžete zjistit zobrazením části clusteru metriky rozhraní YARN pro hodnoty **paměti používá** vs. **Celkem paměti**, a **VCores používá** vs. **Celkový počet VCores**.

3. Nastavte následující konfigurace Spark na odpovídající hodnoty, které by neměl být delší než 90 % dostupné paměti a počet jader. Hodnoty by měla být i v rámci požadavky na paměť Spark aplikace: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Pro výpočet celkové množství paměti používané všechny vykonavatelů: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Pro výpočet celkové množství paměti používané ovladače:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Další čtení

- [Přehled správy paměti Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Ladění aplikací Spark v clusteru HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

