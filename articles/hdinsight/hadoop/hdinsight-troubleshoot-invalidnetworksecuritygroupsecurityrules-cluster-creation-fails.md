---
title: Chyba InvalidNetworkSecurityGroupSecurityRules – Azure HDInsight
description: Vytvoření clusteru se nepovede s chybou ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 1662be3bedb9ec0f2dc21e98ffbd57d8f8ed5777
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540614"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scénář: InvalidNetworkSecurityGroupSecurityRules-vytvoření clusteru se nepovedlo ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazí se kód chyby `InvalidNetworkSecurityGroupSecurityRules` s popisem podobným "pravidla zabezpečení ve skupině zabezpečení sítě nakonfigurovaná s podsítí nepovolují požadované příchozí nebo odchozí připojení."

## <a name="cause"></a>Příčina

Pravděpodobnou příčinou je problém s pravidly pro příchozí [síť zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) nakonfigurovanými pro váš cluster.

## <a name="resolution"></a>Řešení

Přejít na Azure Portal a Identifikujte NSG, která je přidružená k podsíti, ve které se cluster nasazuje. V části **příchozí pravidla zabezpečení** zajistěte, aby pravidla povolovala příchozí přístup k portu 443 pro IP adresy, které jsou [tady](../control-network-traffic.md)uvedené.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).