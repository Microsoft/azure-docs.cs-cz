---
title: Apache Ambari Tez View se v Azure HDInsight načítá pomalu
description: Apache Ambari Tez View se může načítat pomalu nebo se nemusí vůbec načítat v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895094"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scénář: Apache Ambari Tez View se v Azure HDInsight načítá pomalu

Tento článek popisuje kroky řešení potíží a možná řešení problémů při použití komponent interaktivního dotazu v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Apache Ambari Tez View se může načítat pomalu nebo nemusí načíst vůbec. Při načítání Ambari Tez View, může vidět procesy na Headnodes přestane reagovat.

## <a name="cause"></a>Příčina

Přístup k ats api příze může mít někdy nízký výkon na clustery vytvořené před říjnem 2017, kdy cluster má velký počet úloh Hive spustit na něm.

## <a name="resolution"></a>Řešení

Jedná se o problém, který byl vyřešen v říjnu 2017. Opětovné vytvoření clusteru způsobí, že nebude znovu spuštěn a nebude spuštěn.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
