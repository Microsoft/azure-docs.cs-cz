---
title: Azure ukázkový skript prostředí PowerShell - přenosy Vyrovnávání zatížení pro virtuální počítače pro zajištění vysoké dostupnosti | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 61d65cbdcf7867cc7a2a225648dc8b1ab7137bcb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595969"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost

Tento ukázkový skript vytvoří vše potřebné ke spuštění několika virtuálních počítačů Windows nakonfigurovaných v s vysokou dostupností a načíst vyrovnáváním konfiguraci. Po spuštění skriptu budete mít tři virtuální počítače připojené ke skupině dostupnosti Azure a přístupné přes službu Azure Load Balancer.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) a pak spuštěním rutiny `Connect-AzureRmAccount` vytvořte připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Vytvoří virtuální síť Azure a podsíť. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Vytvoří k nástroji pro vyrovnávání zatížení Azure. |
| [Nové AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sondu nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [Nové AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Vytvoří pravidlo Vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Jako HTTP přenos dorazí na nástroje pro vyrovnávání zatížení, se směruje na portu 80 mezi virtuálními počítači v sadě nástroje pro vyrovnávání zatížení. |
| [Nové AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Vytvoří pravidlo překladu adres (NAT) pro vyrovnávání zatížení.  Pravidla NAT namapovat port služby Vyrovnávání zatížení na port na virtuálním počítači. V této ukázce se vytvoří pravidlo NAT pro provoz SSH pro každý virtuální počítač v sadě nástroje pro vyrovnávání zatížení.  |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě (NSG), což je hranice zabezpečení mezi internetem a virtuálním počítačem. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Vytvoří pravidlo NSG, které povolí příchozí provoz. V této ukázce se otevře port 22 pro provoz SSH. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti, podsíti a skupině NSG. |
| [Nové AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajišťují dostupnost aplikace rozmístěním virtuálních počítačů napříč fyzickými prostředky, aby v případě selhání nebyla ovlivněná celá sada. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

Další ukázky sítě skript prostředí PowerShell najdete v [přehled sítě Azure dokumentaci](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
