---
title: Nízký výkon Apache Hive dotazy LLAP ve službě Azure HDInsight
description: Dotazy v Apache Hive LLAP ve službě Azure HDInsight běží pomaleji, než se očekává.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288870"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scénář: nízký výkon Apache Hive dotazy LLAP ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Výchozí konfigurace clusteru nejsou dostatečně vyladěny pro vaše zatížení. Dotazy v LLAP podregistru jsou spouštěny pomaleji, než se čekalo.

## <a name="cause"></a>Příčina

K tomu může dojít z nejrůznějších důvodů.

## <a name="resolution"></a>Řešení

LLAP je optimalizovaná pro dotazy, které zahrnují spojení a agregace. Dotazy, jako jsou následující, se v interaktivním clusteru podregistru neprovádějí správně:

```
select * from table where column = "columnvalue"
```

Chcete-li zlepšit výkon dotazů na body v LLAPu podregistru, nastavte následující konfigurace:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Můžete také zvýšit využití mezipaměti LLAP, aby se zvýšil výkon s následující změnou konfigurace:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]