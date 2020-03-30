---
title: Problémy s připojením Apache Phoenix v Azure HDInsight
description: Problémy s připojením mezi Apache HBase a Apache Phoenix v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887287"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scénář: Problémy s připojením Apache Phoenix v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nelze se připojit k Apache HBase s Apache Phoenix. Důvody se mohou lišit.

## <a name="cause-incorrect-ip"></a>Příčina: Nesprávná ADRESA IP

Nesprávná IP adresa aktivního uzlu Zookeeper.

### <a name="resolution"></a>Řešení

IP adresu aktivního uzlu Zookeeper lze identifikovat z uzla Ambari pomocí odkazů na **HBase** > **Quick Links** > **ZK (Active)** > **Zookeeper Info**. Opravte IP podle potřeby.

---

## <a name="cause-systemcatalog-table-offline"></a>Příčina: SYSTÉM. TABULKA CATALOG offline

Při spouštění `!tables`příkazů, například , se zobrazí chybová zpráva podobná:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Při spouštění `count 'SYSTEM.CATALOG'`příkazů, například , se zobrazí chybová zpráva podobná:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Řešení

Z ui Apache Ambari proveďte následující kroky k restartování služby HMaster na všech uzlech ZooKeeper:

1. V části **Souhrn** v hbázě přejděte na hlavní server **HBase** > **Active HBase**.

1. V části **Součásti** restartujte službu HBase Master.

1. Opakujte tyto kroky pro všechny zbývající **služby HBase Master v pohotovostním režimu.**

Může trvat až pět minut, než služba HBase Master stabilizuje a dokončí obnovení. Poté, `SYSTEM.CATALOG` co je tabulka zpět do normálu, problém s připojením apache phoenix by měl být vyřešen automaticky.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
