---
title: Problémy se servery oblastí ve službě Azure HDInsight
description: Problémy se servery oblastí ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936618"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problémy se servery oblastí ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scénář: nepřiřazené oblasti

### <a name="issue"></a>Problém

Při spuštění `hbase hbck` příkazu se zobrazí chybová zpráva podobná této:

```
multiple regions being unassigned or holes in the chain of regions
```

V uživatelském rozhraní Apache HBase Master uvidíte počet oblastí, které jsou nevyvážené napříč všemi oblastmi serverů. Pak můžete spustit `hbase hbck` příkaz pro zobrazení děr v řetězci oblasti.

### <a name="cause"></a>Příčina

Otvory můžou být výsledkem offline oblastí.

### <a name="resolution"></a>Řešení

Opravte přiřazení. Podle následujících kroků přeneste nepřiřazené oblasti zpět do normálního stavu:

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí Zookeeper Shell.

1. Spusťte `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` příkaz nebo.

1. Ukončete prostředí Zookeeper pomocí `exit` příkazu.

1. Otevřete uživatelské rozhraní Apache Ambari a pak restartujte službu Active HBase Master.

1. Spusťte `hbase hbck` příkaz znovu (bez dalších možností). Zkontrolujte výstup a zajistěte, aby byly přiřazeny všechny oblasti.

---

## <a name="scenario-dead-region-servers"></a>Scénář: servery nedoručené oblasti

### <a name="issue"></a>Problém

Servery oblasti se nedaří spustit.

### <a name="cause"></a>Příčina

Několik rozdělených adresářů WAL

1. Získat seznam aktuálních WALs: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Zkontrolujte `wals.out` soubor. Pokud je k dispozici příliš mnoho rozdělených adresářů (počínaje oddělovači), server oblasti se pravděpodobně nedaří kvůli těmto adresářům.

### <a name="resolution"></a>Řešení

1. Z portálu Ambari zastavte HBA.

1. Spusťte `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` , abyste získali nový seznam WALs.

1. Přesuňte rozdělené adresáře * na dočasnou složku, `splitWAL` a odstraňte složky s oddělovači (*).

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí Zookeeper Shell.

1. Provést `rmr /hbase-unsecure/splitWAL` .

1. Restartujte službu HBA.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).