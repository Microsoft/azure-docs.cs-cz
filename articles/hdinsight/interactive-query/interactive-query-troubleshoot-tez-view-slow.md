---
title: Zobrazení tez Apache Ambari se ve službě Azure HDInsight pomalu načítají
description: Zobrazení tez Apache Ambari se může načítat pomalu nebo se nemusí načítat ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288836"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scénář: zobrazení tez Apache Ambari v Azure HDInsight pomalu

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazení tez Apache Ambari se může načítat pomalu nebo se nemusí vůbec načíst. Při načítání zobrazení Ambari tez se můžete setkat s tím, že procesy v hlavních přestane reagovat.

## <a name="cause"></a>Příčina

Přístup k rozhraním API pro příze ATS může někdy mít špatný výkon u clusterů vytvořených před ZZÚ 2017, pokud je v clusteru spuštěný velký počet úloh podregistru.

## <a name="resolution"></a>Řešení

Jedná se o problém, který byl opraven v říjnu 2017. Po opětovném vytvoření clusteru se tento problém znovu nespustí.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]