---
title: Chyba InvalidNetworkSecurityGroupSecurityRules – Azure HDInsight
description: Vytvoření clusteru se nezdaří s chybouKód InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894148"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scénář: InvalidNetworkSecurityGroupSecurityRules – vytvoření clusteru se nezdaří v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazí se `InvalidNetworkSecurityGroupSecurityRules` kód chyby s popisem podobným "Pravidla zabezpečení ve skupině zabezpečení sítě nakonfigurovaná pomocí podsítě neumožňují požadované příchozí nebo odchozí připojení."

## <a name="cause"></a>Příčina

Pravděpodobně problém s pravidly [skupiny zabezpečení](../../virtual-network/virtual-network-vnet-plan-design-arm.md) příchozí sítě nakonfigurovaných pro váš cluster.

## <a name="resolution"></a>Řešení

Přejděte na portál Azure a identifikujte skupinu zabezpečení sítě, která je přidružená k podsíti, kde se cluster nasazuje. V části **Příchozí pravidla zabezpečení** zkontrolujte, zda pravidla umožňují příchozí přístup k portu 443 pro [zde](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)uvedené adresy IP .

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
