---
title: Problémy s připojením Apache Phoenix ve službě Azure HDInsight
description: Problémy s připojením Apache Phoenix ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/14/2019
ms.openlocfilehash: 66077416dca4048fc99047f7d6b967e55aab0a23
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575741"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scénář: Problémy s připojením Apache Phoenix ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nepovedlo se připojit k Apache HBA pomocí Apache Phoenix. Důvody se mohou lišit.

## <a name="cause-incorrect-ip"></a>Příčina: Nesprávná IP adresa

Nesprávná IP adresa aktivního uzlu Zookeeper

### <a name="resolution"></a>Řešení

IP adresu aktivního uzlu Zookeeper můžete identifikovat z uživatelského rozhraní Ambari pomocí odkazů na **adaptéry** > pro**Rychlé odkazy** > **ZK (aktivní)**  > **Zookeeper informace**. Opravte IP adresu podle potřeby.

---

## <a name="cause-systemcatalog-table-offline"></a>Příčina: Souborů. Tabulka katalogu v režimu offline

Při spouštění příkazů `!tables`, jako je, se zobrazí chybová zpráva podobná následující:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Při spouštění příkazů `count 'SYSTEM.CATALOG'`, jako je, se zobrazí chybová zpráva podobná následující:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Řešení

V uživatelském rozhraní Apache Ambari proveďte následující kroky a restartujte službu HMaster na všech uzlech ZooKeeper:

1. V části **Souhrn** adaptérů HBA, přejít na > HBA**aktivní HBase Master**.

1. V části **součásti** restartujte službu HBase Master.

1. Tento postup opakujte pro všechny zbývající **pohotovostní HBase Master** služby.

Může trvat až pět minut, než se služba HBase Master stabilizovat a dokončí obnovení. Až se `SYSTEM.CATALOG` tabulka vrátí do normálního režimu, problém s připojením k Apache Phoenix by se měl automaticky vyřešit.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
