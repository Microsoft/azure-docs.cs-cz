---
title: Řešení potíží s využitím služby HBA pomocí Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s adaptéry HBA a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573956"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Řešení potíží s Apache HBA pomocí Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešeních, pokud pracujete s webovými částmi Apache HBA v Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Návody spustit sestavy příkazů hbck s více nepřiřazenými oblastmi?

Častá chybová zpráva, kterou se vám může zobrazit při spuštění `hbase hbck` příkazu, je nepřiřazené více oblastí nebo díry v řetězci oblastí.

V uživatelském rozhraní HBase Master uvidíte počet oblastí, které jsou nevyvážené napříč všemi oblastmi serverů. Pak můžete spustit `hbase hbck` příkaz pro zobrazení děr v řetězci oblasti.

Díry mohou být způsobeny offline oblastmi, proto nejprve opravte přiřazení. 

Chcete-li nepřiřazené oblasti převést zpět do normálního stavu, proveďte následující kroky:

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.
2. Pokud se chcete připojit pomocí prostředí Apache Zookeeper, spusťte `hbase zkcli` příkaz.
3. `rmr /hbase/regions-in-transition` Spusťte příkaz`rmr /hbase-unsecure/regions-in-transition` nebo příkaz.
4. Pro ukončení `hbase zkcli` prostředí `exit` použijte příkaz.
5. Otevřete uživatelské rozhraní Apache Ambari a pak restartujte službu Active HBase Master.
6. `hbase hbck` Spusťte příkaz znovu (bez jakýchkoli možností). Zkontrolujte výstup tohoto příkazu a ujistěte se, že jsou přiřazeny všechny oblasti.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Návody opravit problémy s časovým limitem při použití příkazů hbck pro přiřazení oblastí?

### <a name="issue"></a>Problém

Možnou příčinou potíží s vypršením časového limitu `hbck` při použití příkazu může být, že několik oblastí je ve stavu "v přechodu" po dlouhou dobu. Tyto oblasti můžete zobrazit v uživatelském rozhraní HBase Master v režimu offline. Vzhledem k tomu, že se snaží přejít velký počet oblastí, HBase Master může mít časový limit a nebude možné tyto oblasti převést zpět do stavu online.

### <a name="resolution-steps"></a>Postup řešení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.
2. Pokud se chcete připojit pomocí prostředí Apache Zookeeper, spusťte `hbase zkcli` příkaz.
3. `rmr /hbase/regions-in-transition` Spusťte příkaz`rmr /hbase-unsecure/regions-in-transition` nebo.
4. K ukončení `hbase zkcli` prostředí `exit` použijte příkaz.
5. V uživatelském rozhraní Ambari restartujte službu Active HBase Master.
6. Znovu spusťte `hbase hbck -fixAssignments` příkaz.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
