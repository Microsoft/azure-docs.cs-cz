---
title: Chybová zpráva nezobrazená v zobrazení Apache Hive – Azure HDInsight
description: Dotaz se nezdařil v zobrazení Apache Hive bez jakýchkoli podrobností o clusteru Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288949"
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