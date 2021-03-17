---
title: Nízký výkon Apache Hive dotazy LLAP ve službě Azure HDInsight
description: Dotazy v Apache Hive LLAP ve službě Azure HDInsight běží pomaleji, než se očekává.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 80fe51c2131da5a6fcb69eb5526b5d4f33a45e03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930902"
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