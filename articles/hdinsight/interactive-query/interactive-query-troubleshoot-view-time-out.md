---
title: Časový limit zobrazení Apache Hive z výsledku dotazu – Azure HDInsight
description: Časový limit pro zobrazení Apache Hive při načítání výsledku dotazu v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c642760a5f6bfa7e59d42237e1583617e322ece3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288831"
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