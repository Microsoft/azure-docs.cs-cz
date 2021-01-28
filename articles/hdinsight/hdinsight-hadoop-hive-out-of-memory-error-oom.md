---
title: Oprava chyby nedostatku paměti u registru ve službě Azure HDInsight
description: Oprava chyby nedostatku paměti u registru v HDInsight Scénář zákazníka je dotaz napříč mnoha velkými tabulkami.
keywords: Chyba při nedostatku paměti, OOM, nastavení podregistru
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: c0810d33f3ac939b9382bf321448ed72b6d87474
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945710"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Oprava chyby nedostatku paměti Apache Hive ve službě Azure HDInsight

Přečtěte si, jak Apache Hive vyřešit chybu OOM (nedostatek paměti) při zpracování velkých tabulek konfigurací nastavení paměti podregistru.

## <a name="run-apache-hive-query-against-large-tables"></a>Spuštění dotazu Apache Hive pro velké tabulky

Zákazník spustil dotaz na podregistr:

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

Některé drobné odlišnosti tohoto dotazu:

* T1 je alias pro velkou tabulku (Tabulka1), která má spoustu typů ŘETĚZCOVého sloupce.
* Jiné tabulky nejsou velké, ale mají mnoho sloupců.
* Všechny tabulky se vzájemně spojí, v některých případech s více sloupci v tabulce Tabulka1 a dalších.

Dotaz na podregistr trval 26 minut, než se dokončí na clusteru HDInsight se 24 uzly a3. Zákazník si všiml následující zprávy upozornění:

```output
    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product
```

Pomocí spouštěcího modulu Apache Tez. Stejný dotaz běžel po dobu 15 minut a poté vyvolal následující chybu:

```output
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
```

Tato chyba zůstane při použití většího virtuálního počítače (například D12).

## <a name="debug-the-out-of-memory-error"></a>Ladit chybu nedostatku paměti

Naše technické podpory a technické týmy společně nalezly jeden z problémů, které způsobily chybu nedostatku paměti, byl známý problém, který je [popsaný v Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

"Při každém podregistru. auto. Convert. Join. noconditionaltask = true kontrolujeme noconditionaltask. Size a Pokud součet velikostí tabulek ve spojení map je menší než noconditionaltask. velikost plánu by generovala spojení s mapou, problém s tímto je tím, že výpočet nebere v úvahu režii, kterou zavedla jiná implementace zatřiďovací tabulky, jako výsledky, pokud je součet vstupních velikostí menší než velikost noconditionaltask na dotazech malého okraje, budou mít OOM."

**Podregistr. auto. Convert. Join. noconditionaltask** v souboru hive-site.xml byl nastaven na **hodnotu true**:

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

Je pravděpodobnější, že připojení k mapě je příčinou chyby nedostatku paměti v haldě Java. Jak je vysvětleno v blogu v příspěvku [nastavení paměti Hadoop příze ve službě HDInsight](/archive/blogs/shanyu/hadoop-yarn-memory-settings-in-hdinsight), když se tez prováděcí modul používá ke skutečnému využití prostoru haldy, patří do kontejneru TEZ. Podívejte se na následující obrázek popisující paměť kontejneru TEZ.

![Paměťový diagram kontejneru tez: chyba nedostatek paměti v podregistru](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak ukazuje Blogový příspěvek, definuje následující dvě nastavení paměti paměť kontejneru pro haldu: **podregistr. TEZ. Container. Size** a **podregistr. TEZ. Java. výslovný**. Z našeho prostředí neznamená výjimka nedostatku paměti, že velikost kontejneru je příliš malá. Znamená to, že velikost haldy Java (podregistr. TEZ. Java. výslovný) je moc malá. Takže kdykoli se zobrazí nedostatek paměti, můžete se pokusit zvětšit **podregistr. TEZ. Java. výslovný**. V případě potřeby možná budete muset zvětšit **podregistr. TEZ. Container. Size**. Nastavení **Java. výslovný** by mělo být přibližně 80% **kontejneru. Size**.

> [!NOTE]  
> Nastavení **podregistr. TEZ. Java. výslovný** musí být vždy menší než **podregistr. TEZ. Container. Size**.

Vzhledem k tomu, že počítač s D12 má 28 GB paměti, rozhodli jste se použít velikost kontejneru 10 GB (10240 MB) a přiřadit 80% k Java. výslovný:

```console
SET hive.tez.container.size=10240
SET hive.tez.java.opts=-Xmx8192m
```

S novým nastavením se dotaz úspěšně spustil za méně než 10 minut.

## <a name="next-steps"></a>Další kroky

Pokud se OOM chyba, neznamená to, že velikost kontejneru je moc malá. Místo toho byste měli nakonfigurovat nastavení paměti tak, aby se velikost haldy zvýšila a byla aspoň 80% velikosti paměti kontejneru. Informace o optimalizaci dotazů na podregistry najdete v tématu věnovaném [optimalizaci Apache Hive dotazů pro Apache Hadoop ve službě HDInsight](hdinsight-hadoop-optimize-hive-query.md).