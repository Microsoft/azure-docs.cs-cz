---
title: Do clusteru Azure HDInsight se nedají přidat uzly.
description: Řešení potíží, proč nelze přidat uzly do clusteru Apache Hadoop v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894097"
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

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
