---
title: Časový limit zobrazení Apache Hive z výsledku dotazu – Azure HDInsight
description: Časový limit pro zobrazení Apache Hive při načítání výsledku dotazu v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930734"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]