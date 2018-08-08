---
title: Oprava Hive nedostatek paměti při Azure HDInsight
description: Opravte Hive nedostatek paměti při HDInsight. Situaci u zákazníka je dotaz napříč mnoha velké tabulky.
keywords: Nedostatek paměti při, typu, Hive nastavení
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 24b0258bac8c33b84b48655d8ecddd9061368b9a
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592835"
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Oprava Hive nedostatek paměti při Azure HDInsight

Další informace o opravách Hive chyba – nedostatek paměti při zpracování velkých tabulek tím, že nakonfigurujete nastavení paměti Hive.

## <a name="run-hive-query-against-large-tables"></a>Spuštění dotazu Hive proti velké tabulky

Zákazník spuštění dotazu Hive:

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

Některé odlišnosti tohoto dotazu:

* T1 se alias pro velké objemy tabulku TABLE1, který má spoustu typy sloupců řetězec.
* Jiné tabulky nejsou, která jsou velké objemy, ale mají mnoho sloupců.
* Všechny tabulky jsou propojení mezi sebou, v některých případech s více sloupci v Tabulka1 a další.

Dotaz Hive trvalo dokončení 24 uzlu clusteru HDInsight A3 26 minut. Zákazník si všimli následující upozornění:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Pomocí modulu Tez. Stejný dotaz běželo 15 minut a pak vrátil následující chybovou zprávu:

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


## <a name="debug-the-out-of-memory-error"></a>Ladění z důvodu nedostatku paměti

Naše podpora a vývojové týmy společně nalezen byl jeden ze potíže způsobující, že z důvodu nedostatku paměti [známé problémy popsané ve službě JIRA Apache](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

**Hive.auto.convert.join.noconditionaltask** v podregistru site.xml souboru byla nastavena na **true**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Je pravděpodobné, připojení k mapování byl příčinou prostor haldy Java naše paměti. Jak je popsáno v blogovém příspěvku [nastavení paměti Hadoop Yarn v HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), při použití haldy je prováděcí modul Tez místo využité ve skutečnosti patří do kontejneru Tez. Viz následující obrázek popisující kontejneru pamětí Tez.

![Diagram paměti kontejneru tez: Hive chyba – nedostatek paměti](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak tento blogový příspěvek naznačuje, následující nastavení dvou paměti definování paměti kontejneru pro haldu: **hive.tez.container.size** a **hive.tez.java.opts**. Z našich zkušeností mimo paměť výjimka neznamená, že je příliš malá velikost kontejneru. To znamená, že velikost haldy Java (hive.tez.java.opts) je příliš malá. Takže pokaždé, když se zobrazí nedostatek paměti, můžete zkusit zvýšit **hive.tez.java.opts**. V případě potřeby může být nutné zvýšit **hive.tez.container.size**. **Java.opts** nastavení by mělo být přibližně 80 % **container.size**.

> [!NOTE]
> Nastavení **hive.tez.java.opts** musí být menší než **hive.tez.container.size**.
> 
> 

Protože D12 počítač má 28GB paměti, jsme se rozhodli použít velikost kontejneru z 10GB (10240MB) a 80 % přiřadit java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

S novými nastaveními dotaz byl úspěšně spuštěn za méně než 10 minut.

## <a name="next-steps"></a>Další postup

Zobrazuje chyba typu neznamená nutně, že je příliš malá velikost kontejneru. Místo toho by měl nakonfigurovat nastavení paměti tak, aby se zvýší velikost haldy a je alespoň 80 % kontejneru velikost paměti. Optimalizace dotazů Hive naleznete v tématu [dotazy Hive optimalizovat pro Hadoop v HDInsight](hdinsight-hadoop-optimize-hive-query.md).
