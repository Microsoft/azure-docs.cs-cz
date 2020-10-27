---
title: Vypršení časových limitů při použití příkazu hbase hbck ve službě Azure HDInsight
description: Problém s časovým limitem při odstraňování přiřazení oblastí pomocí příkazu ' HBA hbck '
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: c1269f159a23d5fd23e08e7abfbd9a05ddd533ba
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539934"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scénář: vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při opravě přiřazení oblastí dojde k vypršení časového limitu pomocí `hbase hbck` příkazu.

## <a name="cause"></a>Příčina

Možnou příčinou potíží s vypršením časového limitu při použití `hbck` příkazu může být, že několik oblastí je ve stavu "v přechodu" po dlouhou dobu. Tyto oblasti můžete zobrazit v uživatelském rozhraní HBase Master v režimu offline. Vzhledem k tomu, že se snaží přejít velký počet oblastí, HBase Master pravděpodobně vyprší časový limit a nebude možné tyto oblasti převést zpět do stavu online.

## <a name="resolution"></a>Řešení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení pomocí Apache Zookeeper Shell.

1. Spusťte `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` příkaz nebo.

1. Ukončete `hbase zkcli` prostředí pomocí `exit` příkazu.

1. V uživatelském rozhraní Apache Ambari restartujte službu Active HBase Master.

1. Spusťte příkaz `hbase hbck -fixAssignments`.

1. Sledujte HBase Master uživatelského rozhraní v oblasti přechodu "Tento oddíl, abyste se ujistili, že se neblokuje žádná oblast.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).