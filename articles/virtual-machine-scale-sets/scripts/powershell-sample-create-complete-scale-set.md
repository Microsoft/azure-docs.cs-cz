---
title: Ukázky Azure PowerShellu – Vytvoření úplné škálovací sady virtuálních počítačů | Microsoft Docs
description: Ukázky Azure PowerShellu
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 7466bebddd186bfe4e78d25cd8c4ccba84a1c390
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465300"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Vytvoření úplné škálovací sady virtuálních počítačů pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016. Jednotlivé prostředky se konfigurují a vytvářejí. Nepoužívají [předdefinované možnosti vytvoření prostředků dostupné v New-AzureRmVmss](powershell-sample-create-simple-scale-set.md). Po spuštění skriptu můžete k instancím virtuálních počítačů přistupovat přes SSH.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Vytvoří virtuální síť. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Vytvoří konfiguraci front-endových IP adres pro nástroj pro vyrovnávání zatížení. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Vytvoří konfiguraci back-endového fondu adres pro nástroj pro vyrovnávání zatížení. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Vytvoří konfiguraci příchozího pravidla NAT pro nástroj pro vyrovnávání zatížení. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Vytvoří nástroj pro vyrovnávání zatížení. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Vytvoří konfiguraci sondy pro nástroj pro vyrovnávání zatížení. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Vytvoří konfiguraci pravidla pro nástroj pro vyrovnávání zatížení. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | Aktualizujte nástroj pro vyrovnávání zatížení zadanými informacemi. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | Vytvořte konfiguraci protokolu IP pro instance virtuálních počítačů pro škálovací sady. Instance virtuálních počítačů jsou připojené k fondu back-endu nástroje pro vyrovnávání zatížení, fondu NAT a podsíti virtuální sítě. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | Vytvoří konfiguraci škálovací sady. Tato konfigurace zahrnuje informace, jako je počet instancí virtuálního počítače, které se mají vytvořit, SKU (velikost) virtuálních počítačů a režim zásad upgradování. Konfigurace se přidá pomocí dalších rutin a používá se během vytvoření škálovací sady. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Definujte image, která se má pro instance virtuálních počítačů použít, a přidejte ji do konfigurace škálovací sady. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Definujte přihlašovací údaje pro správu (uživatelské jméno a heslo) a předponu názvu virtuálního počítače. Tyto hodnoty přidejte do konfigurace škálovací sady. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | Rozhraní virtuální sítě přidejte do instancí virtuálních počítačů na základě konfigurace protokolu IP. Tyto hodnoty přidejte do konfigurace škálovací sady. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | Vytvořte škálovací sadu na základě informací zadaných v konfiguraci škálovací sady. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../powershell-samples.md).
