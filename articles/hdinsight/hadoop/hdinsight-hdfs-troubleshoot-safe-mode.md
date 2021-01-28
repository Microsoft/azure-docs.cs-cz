---
title: Místní HDFS se zablokoval v bezpečném režimu v clusteru Azure HDInsight.
description: Řešení potíží s místním systémem Apache HDFS zablokování v bezpečném režimu na clusteru Apache ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944451"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scénář: místní HDFS se zablokoval v bezpečném režimu v clusteru Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Místní Apache Hadoop systém souborů DFS (Distributed File System) (HDFS) se zablokuje v bezpečném režimu v clusteru HDInsight. Zobrazí se chybová zpráva podobná následující:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Příčina

Cluster HDInsight se škáloval na několik uzlů níže, nebo je počet uzlů blízko faktoru replikace HDFS.

## <a name="resolution"></a>Řešení

1. Pomocí následujícího příkazu ohlaste stav HDFS v clusteru HDInsight:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Pomocí následujícího příkazu Ověřte integritu HDFS v clusteru HDInsight:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Pokud se zjistilo, že chybí, jsou poškozené nebo v replikovaných blocích, nebo se můžou tyto bloky ignorovat, spusťte následující příkaz, aby se název uzlu převzal v bezpečném režimu:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]