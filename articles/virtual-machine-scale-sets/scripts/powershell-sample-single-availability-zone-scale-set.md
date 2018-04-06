---
title: Ukázky Azure PowerShellu – Škálovací sada s jednou zónou | Microsoft Docs
description: Ukázky Azure PowerShellu
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Vytvoření škálovací sady virtuálních počítačů s jednou zónou pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 v jedné zóně dostupnosti. Po spuštění skriptu můžete k virtuálnímu počítači přistupovat přes protokol RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Vytvoří objekt konfigurace, který definuje podsíť virtuální sítě. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Vytvoří virtuální síť a podsíť. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Vytvoří statickou veřejnou IP adresu. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Vytvoří objekt konfigurace, který definuje front-end nástroje pro vyrovnávání zatížení včetně veřejné IP adresy. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Vytvoří objekt konfigurace, který definuje back-end nástroje pro vyrovnávání zatížení. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Vytvoří nástroj pro vyrovnávání zatížení na základě objektů konfigurace front-endu a back-endu. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Vytvoří sondu stavu nástroje pro vyrovnávání zatížení pro monitorování provozu na portu TCP 80. Pokud dojde ke dvěma selháním po sobě v 15sekundových intervalech, koncový bod se považuje za poškozený. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Vytvoří objekt konfigurace, který definuje pravidla nástroje pro vyrovnávání zatížení pro distribuci provozu na portu TCP 80 z front-endového fondu do back-endového fondu. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Aktualizuje nástroj pro vyrovnávání zatížení s použitím sondy stavu a pravidel nástroje pro vyrovnávání zatížení. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Vytvoří objekt konfigurace, který definuje pravidlo skupiny zabezpečení sítě povolující provoz na portu TCP 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě a pravidlo. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Aktualizuje podsíť virtuální sítě tak, aby se přidružila ke skupině zabezpečení sítě. Všechny instance virtuálních počítačů připojené k této podsíti zdědí pravidla skupiny zabezpečení sítě. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Aktualizuje virtuální síť s použitím konfigurace podsítě a skupiny zabezpečení sítě. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Vytvoří objekt konfigurace, který definuje informace o IP adrese škálovací sady virtuálních počítačů pro připojení instancí virtuálních počítačů k back-endovému fondu nástroje pro vyrovnávání zatížení a fondu příchozího překladu adres.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Vytvoří objekt konfigurace, který definuje počet a velikost instancí virtuálních počítačů ve škálovací sadě a režim zásad upgradu. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Vytvoří objekt konfigurace, který definuje image virtuálního počítače, která se má použít pro instance virtuálních počítačů. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Vytvoří profil konfigurace, který definuje název instance virtuálního počítače a přihlašovací údaje uživatele. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Vytvoří objekt konfigurace, který pro jednotlivé instance virtuálních počítačů definuje adaptér virtuální sítě. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Zkombinuje všechny objekty konfigurace a vytvoří škálovací sadu virtuálních počítačů. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Získá informace o škálovací sadě virtuálních počítačů. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Přidá do virtuálního počítače rozšíření vlastních skriptů pro instalaci základní webové aplikace. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aktualizuje model škálovací sady virtuálních počítačů, aby se použilo rozšíření virtuálního počítače. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../powershell-samples.md).