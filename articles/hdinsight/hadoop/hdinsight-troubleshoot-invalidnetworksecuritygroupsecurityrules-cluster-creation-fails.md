---
title: Chyba InvalidNetworkSecurityGroupSecurityRules – Azure HDInsight
description: Vytvoření clusteru se nepovede s chybou ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944373"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]