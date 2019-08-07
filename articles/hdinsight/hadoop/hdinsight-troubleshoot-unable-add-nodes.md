---
title: Do clusteru Azure HDInsight se nedají přidat uzly.
description: Do clusteru Azure HDInsight se nedají přidat uzly.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 6c2e2c7395fcc45fe74c50beb3624eabb1d395c9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828024"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scénář: Do clusteru Azure HDInsight se nedají přidat uzly.

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

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
