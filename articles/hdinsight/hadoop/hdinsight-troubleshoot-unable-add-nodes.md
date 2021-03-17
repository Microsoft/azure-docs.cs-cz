---
title: Do clusteru Azure HDInsight se nedají přidat uzly.
description: Řešení potíží, proč nelze přidat uzly do clusteru Apache Hadoop v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944332"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scénář: nejde přidat uzly do clusteru Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Do clusteru Azure HDInsight se nedají přidat uzly.

## <a name="cause"></a>Příčina

Důvody se mohou lišit.

## <a name="resolution"></a>Řešení

Pomocí funkce [Velikost clusteru](../hdinsight-scaling-best-practices.md) Vypočítejte počet dalších jader potřebných pro cluster. Tato hodnota je založená na celkovém počtu jader v nových pracovních uzlech. Pak zkuste použít jeden nebo několik následujících kroků:

* Zkontrolujte, jestli v umístění clusteru nejsou k dispozici žádná jádra.

* Podívejte se na počet dostupných jader v ostatních umístěních. Zvažte znovu vytvoření clusteru v jiném umístění, které má dostatek dostupných jader.

* Pokud chcete pro konkrétní umístění zvýšit základní kvótu, vytvořte lístek podpory pro zvýšení základní kvóty jader HDInsight.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]