---
title: Vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight
description: Problém s časovým limitem při odstraňování přiřazení oblastí pomocí příkazu ' HBA hbck '
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737924"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scénář: Vypršení časových limitů s příkazem ' HBA hbck ' ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při opravě přiřazení oblastí `hbase hbck` dojde k vypršení časového limitu pomocí příkazu.

## <a name="cause"></a>Příčina

Potenciální příčina může být delší dobu několik oblastí v části "v přechodu". Tyto oblasti se dají zobrazit jako offline z uživatelského rozhraní Apache HBase Master. Kvůli vysokému počtu oblastí, které se pokoušejí přejít, HBase Master by mohlo dojít k vypršení časového limitu a nebude možné tyto oblasti převést zpátky do online stavu.

## <a name="resolution"></a>Řešení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.

1. Spusťte `hbase zkcli` příkaz pro připojení k prostředí Zookeeper Shell.

1. Spusťte `rmr /hbase/regions-in-transition` příkaz `rmr /hbase-unsecure/regions-in-transition` nebo.

1. Ukončete prostředí pomocí `exit`příkazu. `hbase zkcli`

1. Otevřete uživatelské rozhraní Ambari a restartujte službu Active HBase Master z Ambari.

1. Sledujte HBase Master uživatelského rozhraní v oblasti přechodu "Tento oddíl, abyste se ujistili, že se neblokuje žádná oblast.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
