---
title: Skript Azure Powershellu ukázkový – přidání pravidla skupiny zabezpečení sítě | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – přidá skupinu zabezpečení sítě, které povolí příchozí provoz na určitém portu.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 526a568bbcd7249e4f8917e8cdd82a0de71bfb0a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664994"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Přidejte pravidlo skupiny zabezpečení příchozích dat

Tento ukázkový skript vytvoří pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz na portu 8081.  Získá skript `Microsoft.Network/networkSecurityGroups` prostředek, který je součástí clusteru, vytvoří nové pravidlo konfigurace zabezpečení sítě a aktualizuje skupinu zabezpečení sítě. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů v [příručce k Azure Powershellu](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředek `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Načte skupinu zabezpečení sítě s názvem.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Konfigurace pravidla zabezpečení sítě se přidá do skupiny zabezpečení sítě. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Nastaví cílový stav pro skupinu zabezpečení sítě.|

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).
