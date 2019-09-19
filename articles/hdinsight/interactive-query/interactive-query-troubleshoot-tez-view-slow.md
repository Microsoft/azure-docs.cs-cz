---
title: Zobrazení tez Apache Ambari se ve službě Azure HDInsight pomalu načítají
description: Zobrazení tez Apache Ambari se může načítat pomalu nebo se nemusí načítat ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 218f1b66152354cbf7f23b48e0ebf01be62e5550
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091269"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scénář: Zobrazení tez Apache Ambari se ve službě Azure HDInsight pomalu načítají

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazení tez Apache Ambari se může načítat pomalu nebo se nemusí vůbec načíst. Při načítání zobrazení Ambari tez se můžete setkat s tím, že procesy v hlavních přestane reagovat.

## <a name="cause"></a>Příčina

Přístup k rozhraním API pro příze ATS může někdy mít špatný výkon u clusterů vytvořených před ZZÚ 2017, pokud je v clusteru spuštěný velký počet úloh podregistru.

## <a name="resolution"></a>Řešení

Jedná se o problém, který byl opraven v říjnu 2017. Po opětovném vytvoření clusteru se tento problém znovu nespustí.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
