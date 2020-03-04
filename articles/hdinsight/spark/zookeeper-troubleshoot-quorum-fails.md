---
title: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
description: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250239"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Apache ZooKeeper Server není v pořádku, příznaky by mohly zahrnovat: Správce prostředků/uzly názvů jsou v pohotovostním režimu, jednoduché operace HDFS nefungují, `zkFailoverController` je zastaveno a nelze je spustit, úlohy příz/Spark/Livy selžou z důvodu chyb Zookeeper. V rámci zabezpečených clusterů Sparku nebo interaktivních podregistrů se můžou spustit i procesy démona LLAP. Může se zobrazit chybová zpráva podobná této:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

V protokolu serveru Zookeeper na jakémkoli hostiteli Zookeeper na/var/log/Zookeeper/Zookeeper-Zookeeper-Server-\*. out se může zobrazit také následující chyba:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Příčina

Když je objem souborů snímků velký nebo jsou soubory snímků poškozené, ZooKeeper Server nevytvoří kvorum, což způsobí, že ZooKeeper související služby nejsou v pořádku. ZooKeeper Server neodstraní staré soubory snímků z jeho datového adresáře, ale jedná se o pravidelný úkol, který budou provádět uživatelé, aby zachoval healthiness ZooKeeper. Další informace najdete v tématu [silné Zookeeper a omezení](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Řešení

Chcete-li zjistit, zda je velikost souboru snímků velká, ověřte `/hadoop/zookeeper/version-2` adresář dat ZooKeeper a `/hadoop/hdinsight-zookeeper/version-2`. Pokud existují velké snímky, proveďte následující kroky:

1. Zálohování snímků v `/hadoop/zookeeper/version-2` a `/hadoop/hdinsight-zookeeper/version-2`

1. Vyčistit snímky v `/hadoop/zookeeper/version-2` a `/hadoop/hdinsight-zookeeper/version-2`.

1. Restartujte všechny ZooKeeper servery z uživatelského rozhraní Apache Ambari.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
