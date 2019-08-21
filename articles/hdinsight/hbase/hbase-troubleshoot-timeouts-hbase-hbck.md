---
title: Vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight
description: Problém s časovým limitem při odstraňování přiřazení oblastí pomocí příkazu ' HBA hbck '
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/16/2019
ms.openlocfilehash: 941a710e4c3be3e93263bb63a60c3e0fbcfc4fc4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638382"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scénář: Vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při opravě přiřazení oblastí `hbase hbck` dojde k vypršení časového limitu pomocí příkazu.

## <a name="cause"></a>Příčina

Možnou příčinou potíží s vypršením časového limitu `hbck` při použití příkazu může být, že několik oblastí je ve stavu "v přechodu" po dlouhou dobu. Tyto oblasti můžete zobrazit v uživatelském rozhraní HBase Master v režimu offline. Vzhledem k tomu, že se snaží přejít velký počet oblastí, HBase Master pravděpodobně vyprší časový limit a nebude možné tyto oblasti převést zpět do stavu online.

## <a name="resolution"></a>Řešení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení pomocí Apache Zookeeper Shell.

1. Spusťte `rmr /hbase/regions-in-transition` příkaz `rmr /hbase-unsecure/regions-in-transition` nebo.

1. Ukončete prostředí pomocí `exit`příkazu. `hbase zkcli`

1. V uživatelském rozhraní Apache Ambari restartujte službu Active HBase Master.

1. Spusťte příkaz `hbase hbck -fixAssignments`.

1. Sledujte HBase Master uživatelského rozhraní v oblasti přechodu "Tento oddíl, abyste se ujistili, že se neblokuje žádná oblast.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
