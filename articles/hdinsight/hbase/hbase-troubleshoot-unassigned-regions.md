---
title: Problémy se servery oblastí v Azure HDInsight
description: Problémy se servery oblastí v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272757"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problémy se servery oblastí v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scénář: Nepřiřazené oblasti

### <a name="issue"></a>Problém

Při `hbase hbck` spuštění příkazu se zobrazí chybová zpráva podobná:

```
multiple regions being unassigned or holes in the chain of regions
```

Z hlavního ui Apache HBase můžete zobrazit počet oblastí, které jsou nevyvážené na všech serverech oblasti. Potom můžete spustit `hbase hbck` příkaz zobrazíte díry v řetězci oblasti.

### <a name="cause"></a>Příčina

Díry mohou být výsledkem offline oblastí.

### <a name="resolution"></a>Řešení

Opravte přiřazení. Postupujte podle následujících kroků a přepnete nepřiřazené oblasti zpět do normálního stavu:

1. Přihlaste se ke clusteru HDInsight HBase pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí ZooKeeper.

1. Spustit `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` nebo příkaz.

1. Ukončete zookeeper shell pomocí `exit` příkazu.

1. Otevřete ui Apache Ambari a restartujte službu Active HBase Master.

1. Spusťte `hbase hbck` příkaz znovu (bez dalších možností). Zkontrolujte výstup a ujistěte se, že jsou přiřazeny všechny oblasti.

---

## <a name="scenario-dead-region-servers"></a>Scénář: Servery mrtvé oblasti

### <a name="issue"></a>Problém

Servery oblasti se nepodařilo spustit.

### <a name="cause"></a>Příčina

Více rozdělení WAL adresářů.

1. Získat seznam aktuálních vapů: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Zkontrolujte `wals.out` soubor. Pokud existuje příliš mnoho rozdělení adresářů (počínaje *-rozdělení), server oblasti pravděpodobně selhává z důvodu těchto adresářů.

### <a name="resolution"></a>Řešení

1. Zastavte HBase z portálu Ambari.

1. Provést `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` získat nový seznam WALs.

1. Přesuňte adresáře *-rozdělení do `splitWAL`dočasné složky a odstraňte *-rozdělení adresářů.

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí zookeeper.

1. Spusťte `rmr /hbase-unsecure/splitWAL`.

1. Restartujte službu HBase.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
