---
title: NoClassDefFoundError – Apache Spark s Apache Kafka daty ve službě Azure HDInsight
description: Úloha streamování Apache Spark, která načítá data z Apache Kafka clusteru, se nezdařila s NoClassDefFoundError ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946378"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Úloha streamování Apache Spark, která čte Apache Kafka data, se nezdařila s NoClassDefFoundError ve službě HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Cluster Apache Spark spustí úlohu streamování Sparku, která čte data z clusteru Apache Kafka. Úloha streamování Sparku se nezdařila, pokud je zapnutá komprese streamu Kafka. V takovém případě se application_1525986016285_0193 aplikace pro streamování Sparku nezdařila, protože došlo k chybě:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Příčina

Tato chyba se může vypříčinit zadáním verze `spark-streaming-kafka` souboru jar, který se liší od verze clusteru Kafka, který používáte.

Pokud například používáte cluster Kafka verze 0.10.1, následující příkaz způsobí chybu:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Řešení

Použijte příkaz Spark-submit s `–packages` možností a zajistěte, aby verze souboru jar-streaming-Kafka byla stejná jako verze clusteru Kafka, kterou používáte.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]