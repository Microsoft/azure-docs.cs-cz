---
title: Server Apache ZooKeeper nevytvoří v Azure HDInsight kvorum
description: Server Apache ZooKeeper nevytvoří v Azure HDInsight kvorum
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250239"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Server Apache ZooKeeper nevytvoří v Azure HDInsight kvorum

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Apache ZooKeeper server není v pořádku, příznaky by mohly zahrnovat: oba Resource Managers / Name `zkFailoverController` Uzly jsou v pohotovostním režimu, jednoduché HDFS operace nefungují, je zastaven a nelze spustit, Příze / Spark / Livy úlohy nezdaří kvůli chybám Zookeeper. Llap daemons může také selhat ke spuštění na Secure Spark nebo interaktivní hive clustery. Může se zobrazit chybová zpráva podobná:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

V Zookeeper Server přihlásí na každém hostiteli Zookeeper na /var/log/zookeeper/zookeeper-zookeeper-server-\*.out, můžete také vidět následující chybu:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Příčina

Pokud je objem souborů snímků velký nebo jsou soubory snímků poškozeny, server ZooKeeper nevytvoří kvorum, což způsobí, že služby související se zookeeperem nejsou v pořádku. ZooKeeper server neodstraní staré soubory snímků ze svého datového adresáře, místo toho je to periodická úloha, kterou mají uživatelé provádět, aby zachovali zdraví ZooKeeper. Další informace naleznete v [tématu ZooKeeper Silné a omezení](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Řešení

Zkontrolujte adresář `/hadoop/zookeeper/version-2` dat `/hadoop/hdinsight-zookeeper/version-2` ZooKeeper a zjistěte, zda je velikost souboru snímků velká. Pokud existují velké snímky, postupujte takto:

1. Zálohování snímků v `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`a .

1. Vyčistěte snímky `/hadoop/zookeeper/version-2` v `/hadoop/hdinsight-zookeeper/version-2`a .

1. Restartujte všechny zookeeperské servery z ui Apache Ambari.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
