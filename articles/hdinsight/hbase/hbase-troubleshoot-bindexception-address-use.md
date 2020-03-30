---
title: BindException – adresa, která se už používá v Azure HDInsightu
description: BindException – adresa, která se už používá v Azure HDInsightu
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887338"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scénář: BindException – adresa, která se už používá v Azure HDInsightu

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Operace restartování na serveru Apache HBase Region se nepodařilo dokončit. Z `region-server.log` adresáře in `/var/log/hbase` na pracovních uzlech, kde se nezdaří spuštění serveru oblasti, se může zobrazit chybová zpráva podobná takto:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Příčina

Restartování serverů Apache HBase Region během velké aktivity pracovního vytížení. Níže je uvedeno, co se děje na pozadí, když uživatel zahájí operaci restartování na serveru oblasti HBase z uživatelského rozhraní Apache Ambari:

1. Agent Ambari odešle požadavek na zastavení na server oblasti.

1. Agent Ambari čeká 30 sekund, než se server oblasti řádně vypne.

1. Pokud se aplikace nadále připojuje k serveru oblasti, server se okamžitě nevypne. Časový limit 30 sekund vyprší před vypnutím dojde.

1. Po 30 sekundách odešle agent Ambari`kill -9`příkaz force-kill ( ) na oblastní server.

1. Z důvodu tohoto náhlého vypnutí, přestože proces serveru oblasti je ukončen, port přidružený `AddressBindException`k procesu nemusí být uvolněna, což nakonec vede k .

## <a name="resolution"></a>Řešení

Před zahájením restartování snižte zatížení serverů oblasti HBase. Také je vhodné nejprve spláchnout všechny stoly. Informace o tom, jak vyprázdnit tabulky, najdete v [tématu HDInsight HBase: Jak zlepšit čas restartování clusteru Apache HBase vyprázdněním tabulek](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Případně zkuste ručně restartovat servery oblastí v pracovních uzlech pomocí následujících příkazů:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
