---
title: Vázaný procesor v clusteru Apache HBase - Azure HDInsight
description: Poradce při potížích s vázaným procesorem na serveru oblastí v clusteru Apache HBase v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887304"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scénář: Zavěšený procesor na serveru oblastí v clusteru Apache HBase v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Proces serveru oblasti Apache HBase začíná zabírat téměř 200% procesoru, což způsobuje, že výstrahy se spouští v procesu HBase Master a clusteru nefungují na plnou kapacitu.

## <a name="cause"></a>Příčina

Pokud používáte cluster HBase verze 3.4, pravděpodobně vás zasáhla potenciální chyba způsobená upgradem sady JDK na verzi 1.7.0_151. Příznak vidíme, je oblast proces serveru začíná zabírat téměř 200% `top` CPU (chcete-li ověřit tento spustit příkaz; pokud je proces zabírající téměř `ps -aux | grep` 200% CPU získat jeho pid a potvrdit, že je oblast server proces spuštěním ).

## <a name="resolution"></a>Řešení

1. Nainstalujte jdk 1.8 na všechny uzly clusteru, jak je uvedeno níže:

    * Spusťte `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`akci skriptu . Nezapomeňte vybrat možnost spuštění na všech uzlech.

    * Případně se můžete přihlásit ke každému jednotlivému `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`uzlu a spustit příkaz .

1. Přejděte na UI Ambari - `https://<clusterdnsname>.azurehdinsight.net`.

1. Přejděte na **HBase->Configs->Upřesnit >Upřesnit** `hbase-env configs` a změňte proměnnou `JAVA_HOME` na `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Uložte změnu konfigurace.

1. [Volitelné, ale doporučené] [Vyprázdnění všech tabulek v clusteru](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Z ui Ambari znovu restartujte všechny služby HBase, které je třeba restartovat.

1. V závislosti na datech v clusteru může trvat několik minut až hodinu, než cluster dosáhne stabilního stavu. Potvrzujete, že cluster dosáhne stabilního stavu, a to buď kontrolou hlavního uhlavního panelu (všechny regionální servery by měly být aktivní) z Ambari (refresh) nebo z prostředí HBase se spuštěním hbédu a následným spuštěním příkazu stavu.

Chcete-li ověřit, zda byl upgrade úspěšný, zkontrolujte, zda jsou příslušné procesy HBase zahájeny pomocí příslušné verze java – například pro kontrolu serveru oblasti jako:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
