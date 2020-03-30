---
title: Do clusteru Azure HDInsight nelze přidat uzly.
description: Řešení potíží s tím, proč nelze přidat uzly do clusteru Apache Hadoop v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894097"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scénář: Nelze přidat uzly do clusteru Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Do clusteru Azure HDInsight nelze přidat uzly.

## <a name="cause"></a>Příčina

Důvody se mohou lišit.

## <a name="resolution"></a>Řešení

Pomocí funkce [Velikost clusteru](../hdinsight-scaling-best-practices.md) vypočítejte počet dalších jader potřebných pro cluster. Tato hodnota je založená na celkovém počtu jader v nových pracovních uzlech. Potom vyzkoušejte jeden nebo více z následujících kroků:

* Zkontrolujte, zda jsou v umístění clusteru k dispozici nějaká jádra.

* Podívejte se na počet dostupných jader v ostatních umístěních. Zvažte znovu vytvoření clusteru v jiném umístění, které má dostatek dostupných jader.

* Pokud chcete pro konkrétní umístění zvýšit základní kvótu, vytvořte lístek podpory pro zvýšení základní kvóty jader HDInsight.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
