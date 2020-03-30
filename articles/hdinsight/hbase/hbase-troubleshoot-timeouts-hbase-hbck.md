---
title: Vypršení časových limitů při použití příkazu hbase hbck ve službě Azure HDInsight
description: Problém s časovým odnožím s příkazem hbase hbck při opravě přiřazení oblastí
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887185"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scénář: Časové limity s příkazem hbase hbck v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při opravě `hbase hbck` přiřazení oblastí dochází k časovým časům s příkazem.

## <a name="cause"></a>Příčina

Potenciální příčinou problémů s časovým `hbck` časem při použití příkazu může být, že několik oblastí je ve stavu "v přechodu" po dlouhou dobu. Tyto oblasti můžete zobrazit jako offline v hlavním unovém uzlech HBase. Vzhledem k tomu, že vysoký počet oblastí se pokouší o přechod, může být časový režim serveru HBase Master a nebude možné tyto oblasti znovu uvést do režimu online.

## <a name="resolution"></a>Řešení

1. Přihlaste se ke clusteru HDInsight HBase pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení k shellu Apache ZooKeeper.

1. Spustit `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` nebo příkaz.

1. Ukončete `hbase zkcli` `exit` z prostředí pomocí příkazu.

1. Z ui Apache Ambari restartujte službu Active HBase Master.

1. Spusťte příkaz `hbase hbck -fixAssignments`.

1. Sledujte hbase hlavní uj., že "oblast v přechodu" této části, aby se ujistil, žádná oblast uvízne.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
