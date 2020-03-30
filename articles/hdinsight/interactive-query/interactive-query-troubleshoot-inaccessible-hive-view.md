---
title: Připojení Apache Hive k Apache Zookeeper - Azure HDInsight
description: Apache Hive View nepřístupné kvůli problémům Apache Zookeeper v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 6e000d31ffbacd7cb716bd59dde4f935638b0810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895198"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scénář: Apache Hive se nepodaří navázat připojení k Apache Zookeeper v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při použití komponent interaktivního dotazu v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazení Hive je nepřístupné a protokoly v `/var/log/hive` zobrazení zobrazit chybu podobnou následující:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Příčina

Je možné, že Hive může selhat navázat spojení s Zookeeper, který brání Zobrazení Hive ze spuštění.

## <a name="resolution"></a>Řešení

1. Zkontrolujte, zda je služba Zookeeper zdravá.

1. Zkontrolujte, zda má služba Zookeeper položku ZNode pro Hive Server2. Hodnota bude chybět nebo nesprávná.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Chcete-li obnovit připojení, restartujte uzly Zookeeper a restartujte HiveServer2.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
