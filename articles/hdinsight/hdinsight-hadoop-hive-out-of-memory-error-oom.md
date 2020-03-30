---
title: Oprava chyby nedostatku paměti úlu v Azure HDInsight
description: Oprava chyby nedostatku paměti hive v HDInsight. Scénář zákazníka je dotaz v mnoha velkých tabulkách.
keywords: chyba nedostatku paměti, Nastavení OOM, Hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687773"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Oprava chyby apache úlu bez paměti v Azure HDInsight

Zjistěte, jak opravit chybu Apache Hive bez paměti (OOM) při zpracování velkých tabulek konfigurací nastavení paměti Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Spuštění dotazu Apache Hive proti velkým tabulkám

Zákazník spustil dotaz Hive:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Některé nuance tohoto dotazu:

* T1 je alias velké tabulky TABLE1, která má velké typy sloupců STRING.
* Ostatní tabulky nejsou tak velké, ale mají mnoho sloupců.
* Všechny tabulky se vzájemně spojují, v některých případech s více sloupci v TABLE1 a další.

Dotaz Hive trvalo 26 minut dokončit v clusteru 24 uzlů A3 HDInsight. Zákazník si všiml následujících varovných zpráv:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Pomocí apache tez spuštění motoru. Stejný dotaz běžel po dobu 15 minut a pak hodil následující chybu:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Chyba zůstane při použití většího virtuálního počítače (například D12).

## <a name="debug-the-out-of-memory-error"></a>Ladění chyby nedostatku paměti

Naše týmy podpory a inženýrství společně zjistily, že jedním z problémů, které způsobují chybu nedostatku paměti, byl [známý problém popsaný v Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

"Když hive.auto.convert.join.noconditionaltask = true kontrolujeme noconditionaltask.size a pokud je součet tabulek velikostí v spojení mapy menší než noconditionaltask.size plán by vygeneroval spojení mapy, problém s tím je, že výpočet netrvá v úvahu režie zavedené různé HashTable implementace jako výsledky, pokud součet vstupní velikosti je menší než noconditionaltask velikost i malé dotazy marže narazí na OOM."

Úloha **hive.auto.convert.join.noconditionaltask** v souboru hive-site.xml byla nastavena na **hodnotu true**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Je pravděpodobné, že spojení mapy bylo příčinou chyby nedostatku paměti haldy java haldy. Jak je vysvětleno v blogu [Hadoop Yarn nastavení paměti v HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), když tez spuštění motoru se používá haldy místo používá skutečně patří do kontejneru Tez. Podívejte se na následující obrázek popisující paměť kontejneru Tez.

![Diagram paměti kontejneru Tez: Chyba nedostatku paměti hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak naznačuje příspěvek blogu, následující dvě nastavení paměti definují paměť kontejneru pro haldu: **hive.tez.container.size** a **hive.tez.java.opts**. Z našich zkušeností výjimka z nedostatku paměti neznamená, že velikost kontejneru je příliš malá. To znamená, že velikost haldy Java (hive.tez.java.opts) je příliš malá. Takže kdykoli vidíte z paměti, můžete se pokusit zvýšit **hive.tez.java.opts**. V případě potřeby budete muset zvýšit **hive.tez.container.size**. **Java.opts** nastavení by mělo být kolem 80% **container.size**.

> [!NOTE]  
> Nastavení **hive.tez.java.opts** musí být vždy menší než **hive.tez.container.size**.

Vzhledem k tomu, že počítač D12 má paměť 28 GB, rozhodli jsme se použít kontejner o velikosti 10 GB (10240 MB) a přiřadit 80% java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

S novým nastavením byl dotaz úspěšně spuštěn za méně než 10 minut.

## <a name="next-steps"></a>Další kroky

Získání chyby OOM nemusí nutně znamenat, že velikost kontejneru je příliš malá. Místo toho byste měli nakonfigurovat nastavení paměti tak, aby se zvětšila velikost haldy a byla alespoň 80 % velikosti paměti kontejneru. Informace o optimalizaci dotazů Hive najdete [v tématu Optimalizace dotazů Apache Hive pro Apache Hadoop v HDInsight](hdinsight-hadoop-optimize-hive-query.md).
