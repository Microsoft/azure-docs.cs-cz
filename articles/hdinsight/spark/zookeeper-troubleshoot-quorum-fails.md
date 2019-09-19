---
title: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
description: Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 7091e638743fb8cd1488fe7e332378bf89304af1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087068"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper Server nemůže vytvořit kvorum ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Apache Zookeeper Server není v pořádku, příznaky by mohly zahrnovat: Správce prostředků/názvové uzly jsou v pohotovostním režimu, jednoduché operace HDFS nefungují, `zkFailoverController` je zastaveno a nelze je spustit, úlohy příz/Spark/Livy selžou z důvodu chyb Zookeeper. Může se zobrazit chybová zpráva podobná této:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>Příčina

Když je objem souborů snímků velký nebo jsou soubory snímků poškozené, ZooKeeper Server nevytvoří kvorum, což způsobí, že ZooKeeper související služby nejsou v pořádku. ZooKeeper Server neodstraní staré soubory snímků z jeho datového adresáře, ale jedná se o pravidelný úkol, který budou provádět uživatelé, aby zachoval healthiness ZooKeeper. Další informace najdete v tématu [silné Zookeeper a omezení](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Řešení

Ověřte datový adresář `/hadoop/zookeeper/version-2` Zookeeper a `/hadoop/hdinsight-zookeepe/version-2` Zjistěte, jestli je velikost souboru snímků velká. Pokud existují velké snímky, proveďte následující kroky:

1. Zálohování snímků v `/hadoop/zookeeper/version-2` a `/hadoop/hdinsight-zookeepe/version-2`.

1. Vyčištění snímků v `/hadoop/zookeeper/version-2` a `/hadoop/hdinsight-zookeepe/version-2`.

1. Restartujte všechny ZooKeeper servery z uživatelského rozhraní Apache Ambari.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
