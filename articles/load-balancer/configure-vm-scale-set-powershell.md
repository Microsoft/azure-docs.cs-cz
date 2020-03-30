---
title: Konfigurace škálovací sady virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure – Azure PowerShell
description: Zjistěte, jak nakonfigurovat škálovací sadu virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349993"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Konfigurace škálovací sady virtuálních strojů pomocí existujícího nástroje Pro vyrovnávání zatížení Azure pomocí Azure PowerShellu

V tomto článku se dozvíte, jak nakonfigurovat škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Existující standardní nástroj pro vyrovnávání zatížení sku v předplatném, kde se nasadí škálovací sada virtuálního počítače.
- Virtuální síť Azure pro škálovací sadu virtuálních strojů.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Přihlaste se do Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení škálovací sady virtuálních strojů s existujícím nástrojem pro vyrovnávání zatížení

Nahraďte hodnoty v závorkách názvy prostředků v konfiguraci.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

Níže uvedený příklad nasazuje škálovací sadu virtuálních strojů s:

- Škálovací sada virtuálních strojů s názvem **myVMSS**
- Azure Load Balancer s názvem **myLoadBalancer**
- Back-endový fond vyrovnávání zatížení s názvem **myBackendPool**
- Virtuální síť Azure s názvem **myVnet**
- Podsíť s názvem **mySubnet**
- Skupina prostředků s názvem **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Po vytvoření škálovací sady nelze port back-endu změnit pro pravidlo vyrovnávání zatížení používané sondou stavu systému vyrovnávání zatížení. Chcete-li změnit port, můžete odebrat sondu stavu aktualizací škálovací sady virtuálního počítače Azure, aktualizovat port a pak znovu nakonfigurovat sondu stavu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure.  Další informace o škálovacích sadách virtuálních strojů a nástroj pro vyrovnávání zatížení najdete v tématu:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
                                