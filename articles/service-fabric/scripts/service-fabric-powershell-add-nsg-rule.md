---
title: Ukázkový skript Azure PowerShell – přidání pravidla skupiny zabezpečení sítě | Microsoft Docs
description: Ukázka skriptu Azure PowerShell – přidá skupinu zabezpečení sítě, která povolí příchozí provoz na určitém portu.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 44bb0e615453450c401949f0ce76f15cb82fab67
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680533"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Přidat pravidlo skupiny zabezpečení příchozí sítě

Tento ukázkový skript vytvoří pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz na portu 8081.  Skript získá skupinu zabezpečení sítě, vytvoří nové pravidlo konfigurace zabezpečení sítě a aktualizuje skupinu zabezpečení sítě. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředek `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Načte skupinu zabezpečení sítě podle názvu.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Nastaví cílový stav pro skupinu zabezpečení sítě.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).
