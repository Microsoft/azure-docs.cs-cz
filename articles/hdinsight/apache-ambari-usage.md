---
title: Využití Apache Ambari v Azure HDInsight
description: Diskuse o tom, jak se Apache Ambari používá v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067393"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Využití Apache Ambari v Azure HDInsight

HDInsight používá Apache Ambari pro nasazení a správu clusteru. Ambari agenti spustit na každém uzlu (headnode, uzel pracovníka, zookeeper, a edgenode, pokud existuje). Ambari server běží pouze na headnode (hn0 nebo hn1). Současně se spustí pouze jedna instance serveru Ambari. To je řízeno řadičem převzetí služeb při selhání HDInsight. Když jeden z headnodes je dolů pro restart nebo údržbu, bude druhý headnode aktivní a Ambari server na druhé headnode bude spuštěn.

Všechny konfigurace clusteru by měla být provedena prostřednictvím [ui Ambari](./hdinsight-hadoop-manage-ambari.md), všechny místní změny budou přepsány při restartování uzlu.

## <a name="failover-controller-services"></a>Služby řadiče převzetí služeb při selhání

Řadič převzetí služeb při selhání HDInsight je také zodpovědný za aktualizaci IP adresy hostitele headnode, která odkazuje na aktuální aktivní hlavní uzel. Všichni agenti Ambari jsou konfigurováni tak, aby hlásili jeho stav a prezenční signál hostiteli headnode. Řadič převzetí služeb při selhání je sada služeb spuštěných na každém uzlu v clusteru, pokud nejsou spuštěny, převzetí služeb při selhání headnode nemusí fungovat správně a při pokusu o přístup k serveru Ambari skončíte s http 502.

Chcete-li zkontrolovat, který headnode je aktivní, jedním ze způsobů je ssh na jeden z uzlů v clusteru, pak spustit `ping headnodehost` a porovnat IP s dvěma headnodes.

Pokud služby řadiče s podporou převzetí služeb při selhání nejsou spuštěny, převzetí služeb při selhání headnode nemusí dojít správně, což může skončit není spuštěn server Ambari. Chcete-li zkontrolovat, zda jsou spuštěny služby řadiče s podporou převzetí služeb při selhání, spusťte:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Protokoly

Na aktivním headnode můžete zkontrolovat protokoly serveru Ambari na adrese:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Na libovolném uzlu v clusteru můžete zkontrolovat protokoly agenta Ambari na adrese:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sekvence spuštění služby

Toto je posloupnost spuštění služby při spuštění:

1. Hdinsight-agent spustí služby řadiče s podporou převzetí služeb při selhání.
1. Služby řadiče s podporou převzetí služeb při selhání spouštějí agenta Ambari na každém uzlu a serveru Ambari na aktivním headnode.

## <a name="ambari-database"></a>Databáze Ambari

HDInsight vytvoří databázi SQL Azure pod kapotou, která bude sloužit jako databáze pro server Ambari. Výchozí [úroveň služby je S0](../sql-database/sql-database-elastic-pool-scale.md).

Pro každý cluster s počtem pracovních uzlů větší než 16 při vytváření clusteru, S2 je vrstva databázové služby.

## <a name="takeaway-points"></a>Body s sebou

Nikdy ručně spustit / zastavit ambari-server nebo ambari-agent služby, pokud se pokoušíte restartovat službu obejít problém. Chcete-li vynutit převzetí služeb při selhání, můžete restartovat aktivní headnode.

Nikdy ručně upravovat žádné konfigurační soubory v žádném uzlu clusteru, ať Ambari UI dělat práci za vás.

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
