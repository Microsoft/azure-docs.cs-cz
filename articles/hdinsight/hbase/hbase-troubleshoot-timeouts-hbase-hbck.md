---
title: Vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight
description: Problém s časovým limitem při odstraňování přiřazení oblastí pomocí příkazu ' HBA hbck '
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887185"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scénář: vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při odstraňování přiřazení oblastí dojde k vypršení časového limitu u `hbase hbck` příkazu.

## <a name="cause"></a>Příčina

Možnou příčinou potíží s vypršením časového limitu při použití `hbck` příkazu může být, že po dlouhou dobu je ve stavu "v přechodu" několik oblastí. Tyto oblasti můžete zobrazit v uživatelském rozhraní HBase Master v režimu offline. Vzhledem k tomu, že se snaží přejít velký počet oblastí, HBase Master pravděpodobně vyprší časový limit a nebude možné tyto oblasti převést zpět do stavu online.

## <a name="resolution"></a>Rozlišení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spuštěním příkazu `hbase zkcli` se připojte pomocí prostředí Apache ZooKeeper Shell.

1. Spusťte příkaz `rmr /hbase/regions-in-transition` nebo `rmr /hbase-unsecure/regions-in-transition`.

1. Ukončete z `hbase zkcli` Shell pomocí příkazu `exit`.

1. V uživatelském rozhraní Apache Ambari restartujte službu Active HBase Master.

1. Spusťte příkaz `hbase hbck -fixAssignments`.

1. Sledujte HBase Master uživatelského rozhraní v oblasti přechodu "Tento oddíl, abyste se ujistili, že se neblokuje žádná oblast.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
