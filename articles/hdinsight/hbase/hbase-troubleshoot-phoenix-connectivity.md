---
title: Problémy s připojením Apache Phoenix ve službě Azure HDInsight
description: Problémy s připojením mezi Apache HBA a Apache Phoenix ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887287"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scénář: Apache Phoenix problémy s připojením ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nepovedlo se připojit k Apache HBA pomocí Apache Phoenix. Důvody se mohou lišit.

## <a name="cause-incorrect-ip"></a>Příčina: nesprávná IP adresa

Nesprávná IP adresa aktivního uzlu Zookeeper

### <a name="resolution"></a>Rozlišení

IP adresa aktivního uzlu Zookeeper se dá identifikovat z uživatelského rozhraní Ambari pomocí odkazů na **hba** > **Rychlé odkazy** > **ZK (aktivní)**  > **Zookeeper informace**. Opravte IP adresu podle potřeby.

---

## <a name="cause-systemcatalog-table-offline"></a>Příčina: SYSTEM. Tabulka katalogu v režimu offline

Při spouštění příkazů, jako je například `!tables`, se zobrazí chybová zpráva podobná této:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Při spouštění příkazů, jako je například `count 'SYSTEM.CATALOG'`, se zobrazí chybová zpráva podobná této:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Rozlišení

V uživatelském rozhraní Apache Ambari proveďte následující kroky a restartujte službu HMaster na všech uzlech ZooKeeper:

1. V části **Souhrn** adaptérů HBA, přejít na **hba** > **aktivní HBase Master**.

1. V části **součásti** restartujte službu HBase Master.

1. Tento postup opakujte pro všechny zbývající **pohotovostní HBase Master** služby.

Může trvat až pět minut, než se služba HBase Master stabilizovat a dokončí obnovení. Až se tabulka `SYSTEM.CATALOG` vrátí do normálního režimu, bude se problém s připojením Apache Phoenix automaticky vyřešit.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
