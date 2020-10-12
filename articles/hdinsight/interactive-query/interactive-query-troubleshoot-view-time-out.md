---
title: Časový limit zobrazení Apache Hive z výsledku dotazu – Azure HDInsight
description: Časový limit pro zobrazení Apache Hive při načítání výsledku dotazu v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f00f70e674ac0b83b737d6b2a4bf9d20400736fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77672017"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scénář: časový limit zobrazení Apache Hive při načítání výsledku dotazu v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spouštění určitých dotazů ze zobrazení Apache Hive může dojít k následující chybě:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Příčina

Výchozí hodnota časového limitu zobrazení podregistru nemusí být vhodná pro dotaz, který používáte. Zadané časové období je příliš krátké pro zobrazení podregistru, aby bylo možné načíst výsledek dotazu.

## <a name="resolution"></a>Řešení

Zvyšte časový limit zobrazení podregistru Apache Ambari nastavením následujících vlastností v `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Hodnota `HIVE_VIEW_INSTANCE_NAME` je k dispozici na konci adresy URL zobrazení podregistru.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
