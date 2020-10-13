---
title: Ukázkový skript Azure PowerShellu – Vytvoření virtuálního počítače s Windows a NLB
description: Ukázkový skript Azure PowerShellu – Vytvoření virtuálního počítače s Windows a NLB
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 2501a2476c1a26b09cbc33bb4e12463eb2c734d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078797"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Vyrovnávání zatížení provozu mezi vysoce dostupnými virtuálními počítači

Tento ukázkový skript vytvoří vše potřebné pro spuštění několika virtuálních počítačů s Windows Serverem 2016 v konfiguraci s vysokou dostupností a vyrovnáváním zatížení. Po spuštění skriptu budete mít tři virtuální počítače připojené ke skupině dostupnosti Azure a přístupné přes službu Azure Load Balancer.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
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
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Vytvoří konfiguraci sondy pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Vytvoří konfiguraci pravidla pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Vytvoří konfiguraci příchozího pravidla NAT pro nástroj pro vyrovnávání zatížení. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Vytvoří nástroj pro vyrovnávání zatížení. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Vytvoří konfiguraci pravidla skupiny zabezpečení sítě. Tato konfigurace se použije k vytvoření pravidla skupiny zabezpečení sítě při jejím vytváření. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá informace o podsíti. Tyto informace se použijí při vytváření síťového rozhraní. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Vytvoří síťové rozhraní. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

Virtuální počítače můžete také vytvořit pomocí vlastní spravované image. V konfiguraci virtuálního počítače použijte pro `Set-AzVMSourceImage` použití `-Id` parametrů a `-VM` místo `-PublisherName` , `-Offer` , `-Skus` a `-Version` .

Například vytvoření konfigurace virtuálního počítače by bylo:

```powershell
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 -AvailabilitySetId $as.Id | `
  Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred | `
  Set-AzVMSourceImage -Id <Image.ID of the custom managed image> | Add-AzVMNetworkInterface -Id $nicVM3.Id
 ```

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
