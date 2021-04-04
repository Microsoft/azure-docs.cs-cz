---
title: Zobrazení tez Apache Ambari se ve službě Azure HDInsight pomalu načítají
description: Zobrazení tez Apache Ambari se může načítat pomalu nebo se nemusí načítat ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930760"
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