---
title: Apache Hive připojení k Apache Zookeeper – Azure HDInsight
description: Zobrazení Apache Hive nedostupné kvůli problémům s Apache Zookeeper v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939306"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scénář: Apache Hive se nepodařilo navázat připojení k Apache Zookeeper v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazení podregistru je nedostupné a v protokolech se `/var/log/hive` zobrazuje chyba podobná této:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Příčina

Může dojít k selhání podregistru při navázání připojení k Zookeeper, které brání spuštění zobrazení podregistru.

## <a name="resolution"></a>Řešení

1. Ověřte, že je služba Zookeeper v pořádku.

1. Ověřte, zda má služba Zookeeper záznam ZNode pro podregistr Server2. Hodnota bude chybět nebo bude nesprávná.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Pokud chcete znovu vytvořit připojení, restartujte uzly Zookeeper a restartujte HiveServer2.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]