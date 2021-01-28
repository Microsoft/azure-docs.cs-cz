---
title: Nepovedlo se přečíst protokol Apache nitě v Azure HDInsight.
description: Kroky řešení potíží a možná řešení pro problémy při komunikaci s clustery Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943047"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scénář: nejde číst protokol Apache nitě v Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Protokoly Apache nitě nalezené z účtu úložiště nejsou čitelné pro člověka. Analyzátor souborů nefunguje a vytvoří následující chybovou zprávu:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Příčina

Protokol Apache nitě je agregovaný do `IndexFile` formátu, který analyzátor souborů nepodporuje.

## <a name="resolution"></a>Řešení

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. V uživatelském rozhraní Ambari přejděte do **Konfigurace příze**  >    >  **Upřesnit** Upřesnit  >  **nitě-site**.

1. Pro úložiště WASB: výchozí hodnota pro `yarn.log-aggregation.file-formats` je `IndexedFormat,TFile` . Změňte hodnotu na `TFile` .

1. Pro úložiště ADLS: výchozí hodnota pro `yarn.nodemanager.log-aggregation.compression-type` je `gz` . Změňte hodnotu na `none` .

1. Uložte změnu a restartujte všechny ovlivněné služby.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).