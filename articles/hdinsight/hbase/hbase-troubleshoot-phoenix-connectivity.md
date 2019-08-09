---
title: Problémy s připojením Apache Phoenix ve službě Azure HDInsight
description: Problémy s připojením Apache Phoenix ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887037"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scénář: Problémy s připojením Apache Phoenix ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nepovedlo se připojit k Apache HBA pomocí Apache Phoenix. Důvody se mohou lišit.

## <a name="cause-incorrect-ip"></a>Příčina: Nesprávná IP adresa

Nesprávná IP adresa aktivního uzlu Zookeeper

### <a name="resolution"></a>Řešení

IP adresa aktivního uzlu Zookeeper se dá identifikovat z uživatelského rozhraní Ambari, a to pomocí odkazů na **adaptéry HBA-> Rychlé odkazy – > ZK***  **(aktivní) – informace o > Zookeeper**. Podle potřeby opravte.

---

## <a name="cause-systemcatalog-table-offline"></a>Příčina: Souborů. Tabulka katalogu v režimu offline

Při spouštění příkazů `!tables`, jako je, se zobrazí chybová zpráva podobná následující:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Při spouštění příkazů `count 'SYSTEM.CATALOG'`, jako je, se zobrazí chybová zpráva podobná následující:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Řešení

Restartujte službu HMaster na všech uzlech Zookeeper z uživatelského rozhraní Ambari.

1. Přejít na **HBA – > aktivní HBase Master** propojit v části Souhrn adaptérů HBA.

1. V části **součásti** restartujte službu HBase Master.

1. Výše uvedený postup opakujte pro zbývající **pohotovostní HBase Master** služby.

Aby služba HBase Master mohla stabilizovat a dokončit obnovení, může trvat až 5 minut. Až se `SYSTEM.CATALOG` tabulka vrátí do normálního režimu, problém s připojením k Apache Phoenix by se měl automaticky vyřešit.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
