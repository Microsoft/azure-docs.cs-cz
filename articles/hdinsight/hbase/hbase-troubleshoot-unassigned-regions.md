---
title: Problémy se servery oblastí ve službě Azure HDInsight
description: Problémy se servery oblastí ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935429"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problémy se servery oblastí ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scénář: Nepřiřazené oblasti

### <a name="issue"></a>Problém

Při spuštění `hbase hbck` příkazu se zobrazí chybová zpráva podobná této:

```
multiple regions being unassigned or holes in the chain of regions
```

Z uživatelského rozhraní Apache HBase Master se může zobrazit, že počet oblastí, které jsou nevyvážené napříč všemi servery oblastí.

### <a name="cause"></a>Příčina

Otvory můžou být výsledkem offline oblastí.

### <a name="resolution"></a>Řešení

Opravte přiřazení. Podle následujících kroků přeneste nepřiřazené oblasti zpět do normálního stavu:

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí Zookeeper Shell.

1. Spusťte `rmr /hbase/regions-in-transition` příkaz `rmr /hbase-unsecure/regions-in-transition` nebo.

1. Ukončete prostředí Zookeeper pomocí `exit` příkazu.

1. Otevřete uživatelské rozhraní Ambari a restartujte službu Active HBase Master z Ambari.

1. Spusťte `hbase hbck` příkaz znovu (bez dalších možností). Zkontrolujte výstup a zajistěte, aby byly přiřazeny všechny oblasti.

---

## <a name="scenario-dead-region-servers"></a>Scénář: Servery neaktivních oblastí

### <a name="issue"></a>Problém

Servery oblasti se nedaří spustit.

### <a name="cause"></a>Příčina

Několik rozdělených adresářů WAL

1. Získat seznam aktuálních Wals: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. `wals.out` Zkontrolujte soubor. Pokud je k dispozici příliš mnoho rozdělených adresářů (počínaje oddělovači), server oblasti se pravděpodobně nedaří kvůli těmto adresářům.

### <a name="resolution"></a>Řešení

1. Z portálu Ambari zastavte HBA.

1. Spusťte `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` , abyste získali nový seznam WALs.

1. Přesuňte rozdělené adresáře * na dočasnou složku, `splitWAL`a odstraňte složky s oddělovači (*).

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí Zookeeper Shell.

1. Provést `rmr /hbase-unsecure/splitWAL`.

1. Restartujte službu HBA.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
