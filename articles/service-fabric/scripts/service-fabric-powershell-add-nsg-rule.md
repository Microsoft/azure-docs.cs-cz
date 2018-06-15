---
title: Azure skript prostředí PowerShell ukázkový – přidat pravidlo pro skupinu zabezpečení sítě | Microsoft Docs
description: Ukázkový skript prostředí PowerShell Azure – přidá skupinu zabezpečení sítě chcete povolit příchozí přenosy na určitém portu.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2017
ms.locfileid: "25987381"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Přidání skupiny pravidla zabezpečení příchozích síťových

Tento ukázkový skript vytvoří pravidlo skupiny zabezpečení sítě chcete povolit příchozí přenosy na portu 8081.  Získá skript `Microsoft.Network/networkSecurityGroups` prostředek, který je součástí clusteru, vytvoří nové pravidlo konfigurace zabezpečení sítě a aktualizuje skupinu zabezpečení sítě. Podle potřeby upravte parametry.

V případě potřeby nainstalujte pomocí pokynů v prostředí Azure PowerShell [prostředí Azure PowerShell průvodce](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Získá `Microsoft.Network/networkSecurityGroups` prostředků. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Načte skupinu zabezpečení sítě podle názvu.|
|[Přidat AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Přidá skupinu zabezpečení sítě konfiguraci pravidla zabezpečení sítě. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Nastaví cíl stavu pro skupinu zabezpečení sítě.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).
