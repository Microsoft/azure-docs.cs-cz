---
title: Opravte Hive nedostatek paměti v Azure HDInsight | Microsoft Docs
description: Opravte Hive nedostatek paměti v HDInsight. Scénář zákazníka je dotaz napříč mnoha velké tabulky.
keywords: Nedostatek paměti chyby, OOM, Hive nastavení
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: d34ef0828b64ea32adbef8c49cdad1d3bcce6268
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Opravte Hive nedostatek paměti v Azure HDInsight

Zjistěte, jak opravit Hive nedostatek paměti při zpracování velkých tabulky nakonfigurováním nastavení paměti Hive.

## <a name="run-hive-query-against-large-tables"></a>Spusťte dotaz Hive na velké tabulky

Zákazník spustili dotazů Hive:

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

Některé drobné odlišnosti tohoto dotazu:

* T1 je alias na velkých tabulku tabulky1, který má spoustu typy sloupců řetězec.
* Ostatní tabulky není, jsou velký, ale máte mnoho sloupců.
* Všechny tabulky jsou propojení mezi sebou, v některých případech s více sloupců v tabulky1 a ostatní.

Dotaz Hive trvalo 26 minut na dokončení na 24 uzlu clusteru A3 HDInsight. Zákazník si všimli následující upozornění:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Pomocí modulu Tez. Stejný dotaz spustili 15 minut a pak vrátil následující chybovou zprávu:

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

Chyba zůstává při použití větší virtuální počítač (například D12).


## <a name="debug-the-out-of-memory-error"></a>Ladění mimo Chyba paměti

Jeden z problémů, příčinou chyba nedostatku paměti bylo nalezeno naše podporu a vývojové týmy společně [známý problém popsaný v Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

**Hive.auto.convert.join.noconditionaltask** ve hive-site.xml souboru byla nastavena na **true**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Je pravděpodobné, mapy připojení byl příčinou haldy prostoru Java naše chyby paměti. Jak je popsáno v příspěvku na blogu [nastavení paměti Hadoop Yarn v HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), při použití Tez je modul pro vykonání halda místo využité ve skutečnosti patří ke kontejneru Tez. Viz následující obrázek popisující paměti kontejneru Tez.

![Diagram paměti kontejneru tez: Hive nedostatek paměti](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak v příspěvku blogu naznačuje, následující nastavení dva paměti definování paměti kontejner pro halda: **hive.tez.container.size** a **hive.tez.java.opts**. Z našich zkušeností se výjimka paměti neznamená, že velikost kontejneru je příliš malá. Znamená to, že velikost haldy Java (hive.tez.java.opts) je příliš malá. Proto vždy, když se zobrazí nedostatek paměti, můžete zkusit zvýšit **hive.tez.java.opts**. V případě potřeby budete možná muset zvýšit **hive.tez.container.size**. **Java.opts** nastavení by mělo být přibližně 80 % **container.size**.

> [!NOTE]
> Nastavení **hive.tez.java.opts** musí být menší než **hive.tez.container.size**.
> 
> 

D12 počítač obsahuje 28GB paměti, a proto jsme se rozhodli, přidělte 80 % java.opts pomocí kontejneru velikost 10 GB (10240MB):

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

S novým nastavením dotaz byl úspěšně spuštěn v části 10 minut.

## <a name="next-steps"></a>Další postup

Získávání chybu OOM není nutně znamenat, že velikost kontejneru je příliš malá. Místo toho by měl nakonfigurovat nastavení paměti, aby velikost haldy je vyšší a je alespoň 80 % velikost paměti kontejneru. Optimalizace dotazů Hive, najdete v části [optimalizovat Hive dotazy pro Hadoop v HDInsight](hdinsight-hadoop-optimize-hive-query.md).
