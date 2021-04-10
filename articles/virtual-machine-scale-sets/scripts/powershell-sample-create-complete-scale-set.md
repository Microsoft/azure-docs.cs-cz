---
title: Ukázky Azure PowerShell – vytvoření kompletní sady škálování virtuálních počítačů
description: Tento skript vytvoří sadu škálování virtuálního počítače s Windows serverem 2016, kde se nakonfigurují a vytvoří jednotlivé prostředky.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 31d779adc0ce08331486d615a6af2095323189bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935477"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Vytvoření úplné škálovací sady virtuálních počítačů pomocí PowerShellu

Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016. Místo použití [integrovaných možností vytváření prostředků, které jsou k dispozici v New-AzVmss](powershell-sample-create-simple-scale-set.md), se nakonfigurují a vytvoří jednotlivé prostředky. Po spuštění skriptu můžete k instancím virtuálních počítačů přistupovat přes SSH.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Vytvoří konfiguraci front-endových IP adres pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Vytvoří konfiguraci back-endového fondu adres pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Vytvoří konfiguraci příchozího pravidla NAT pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Vytvoří nástroj pro vyrovnávání zatížení. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Vytvoří konfiguraci sondy pro nástroj pro vyrovnávání zatížení. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Vytvoří konfiguraci pravidla pro nástroj pro vyrovnávání zatížení. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Aktualizujte nástroj pro vyrovnávání zatížení zadanými informacemi. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Vytvořte konfiguraci protokolu IP pro instance virtuálních počítačů pro škálovací sady. Instance virtuálních počítačů jsou připojené k fondu back-endu nástroje pro vyrovnávání zatížení, fondu NAT a podsíti virtuální sítě. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Vytvoří konfiguraci škálovací sady. Tato konfigurace zahrnuje informace, jako je počet instancí virtuálního počítače, které se mají vytvořit, SKU (velikost) virtuálních počítačů a režim zásad upgradování. Konfigurace se přidá pomocí dalších rutin a používá se během vytvoření škálovací sady. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definujte image, která se má pro instance virtuálních počítačů použít, a přidejte ji do konfigurace škálovací sady. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definujte přihlašovací údaje pro správu (uživatelské jméno a heslo) a předponu názvu virtuálního počítače. Tyto hodnoty přidejte do konfigurace škálovací sady. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Rozhraní virtuální sítě přidejte do instancí virtuálních počítačů na základě konfigurace protokolu IP. Tyto hodnoty přidejte do konfigurace škálovací sady. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Vytvořte škálovací sadu na základě informací zadaných v konfiguraci škálovací sady. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).
