---
title: Konfigurace sady škálování virtuálních počítačů pomocí existující Azure Load Balancer-Azure PowerShell
description: Naučte se konfigurovat sadu škálování virtuálního počítače s existujícím Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88510058"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Konfigurace sady škálování virtuálních počítačů s existujícím Azure Load Balancer pomocí Azure PowerShell

V tomto článku se dozvíte, jak nakonfigurovat sadu škálování virtuálních počítačů s existujícím Azure Load Balancer.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Existující Nástroj pro vyrovnávání zatížení Standard SKU v předplatném, kde se bude nasazovat sada škálování virtuálního počítače.
- Virtual Network Azure pro sadu škálování virtuálního počítače.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Přihlaste se k Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení sady škálování virtuálních počítačů s existujícím nástrojem pro vyrovnávání zatížení

Hodnoty v závorkách nahraďte názvy prostředků v konfiguraci.

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

Následující příklad nasadí sadu škálování virtuálního počítače pomocí:

- Sada škálování virtuálních počítačů s názvem **myVMSS**
- Azure Load Balancer s názvem **myLoadBalancer**
- Back-end fond nástroje pro vyrovnávání zatížení s názvem **myBackendPool**
- Azure Virtual Network s názvem **myVnet**
- Podsíť s názvem **mySubnet**
- Skupina prostředků s názvem **myResourceGroup**

```azurepowershell-interactive

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
> Po vytvoření sady škálování se port back-endu nedá změnit pro pravidlo vyrovnávání zatížení používané sondou stavu nástroje pro vyrovnávání zatížení. Pokud chcete změnit port, můžete odstranit sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače Azure, aktualizujete port a pak znovu nakonfigurujete test stavu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili sadu škálování virtuálního počítače s existujícím Azure Load Balancer.  Další informace o službě Virtual Machine Scale Sets a nástroji pro vyrovnávání zatížení najdete v těchto tématech:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
