---
title: Otevření portů k virtuálnímu počítači pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak otevřít port / vytvořit koncový bod vašeho virtuálního počítače s Windows pomocí režimu nasazení Azure resource Manageru a Azure Powershellu
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 1bd5c63db63bea24e5cf088cf9974233d3535912
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976466"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Postup otevření portů a koncových bodů pro virtuální počítač v Azure pomocí Powershellu
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
Vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla, je třeba [nejnovější verzi prostředí Azure PowerShell nainstalovaný](/powershell/azureps-cmdlets-docs). Můžete také [tyto kroky provést pomocí webu Azure portal](nsg-quickstart-portal.md).

Přihlaste se ke svému účtu Azure:

```powershell
Connect-AzAccount
```

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých *myResourceGroup*, *myNetworkSecurityGroup*, a *myVnet*.

Vytvořte pravidlo pomocí [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující příklad vytvoří pravidlo *myNetworkSecurityGroupRule* umožňující *tcp* přenosy na portu *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

V dalším kroku vytvoříte skupinu zabezpečení sítě s [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) a přiřaďte pravidlo pro protokol HTTP jste právě vytvořili následujícím způsobem. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nyní můžeme přiřadit skupinu zabezpečení sítě k podsíti. Následující příklad přiřadí existující virtuální síť s názvem *myVnet* proměnné *$vnet* s [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Přidružení skupiny zabezpečení sítě s vaší podsítě s [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Následující příklad přiřadí podsíť s názvem *mySubnet* skupinou zabezpečení sítě:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Nakonec aktualizujte vaše virtuální síť s [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) vaše změny se projeví:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožní začít pracovat s přenosy směřující do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitost řízení přístupu k vašim prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](tutorial-virtual-network.md#secure-network-traffic).

U vysoce dostupných webových aplikací by měl umístěte své virtuální počítače za nástrojem Azure Load Balancer. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [zatížení vyrovnávat virtuální počítače s Linuxem v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo pro povolení provozu HTTP. Můžete najít informace o vytváření podrobnější prostředí v následujících článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)
* [Přehled Azure Resource Manageru pro nástroje pro vyrovnávání zatížení](../../load-balancer/load-balancer-arm.md)

