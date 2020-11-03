---
title: Chyba InvalidNetworkSecurityGroupSecurityRules – Azure HDInsight
description: Vytvoření clusteru se nepovede s chybou ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289091"
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