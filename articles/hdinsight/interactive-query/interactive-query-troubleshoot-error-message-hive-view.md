---
title: Chybová zpráva nezobrazená v zobrazení Apache Hive – Azure HDInsight
description: Dotaz se nezdařil v zobrazení Apache Hive bez jakýchkoli podrobností o clusteru Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930996"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scénář: chybová zpráva dotazu není zobrazená v zobrazení Apache Hive ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Chybová zpráva dotazu zobrazení Apache Hive bude vypadat podobně jako v tomto případě bez dalších informací:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Příčina

Někdy je chybová zpráva selhání dotazu pravděpodobně příliš velká a nelze ji zobrazit na hlavní stránce zobrazení podregistru.

## <a name="resolution"></a>Řešení

Podívejte se na kartu oznámení v pravém horním rohu Hive_view a zobrazte tak kompletní trasování zásobníku a chybovou zprávu.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]