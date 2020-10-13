---
title: Otevření portů k virtuálnímu počítači pomocí Azure PowerShell
description: Přečtěte si, jak otevřít port/vytvořit koncový bod pro virtuální počítač s Windows pomocí režimu nasazení Azure Resource Manager a Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a417c271c16aabe91836f425f26c0a55e7103557
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973933"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Postup otevření portů a koncových bodů pro virtuální počítač v Azure pomocí PowerShellu
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
K vytvoření skupiny zabezpečení sítě a pravidel seznamu ACL potřebujete [nejnovější verzi Azure PowerShell nainstalovanou](/powershell/azure/). [Tyto kroky můžete provést také pomocí Azure Portal](nsg-quickstart-portal.md).

Přihlaste se ke svému účtu Azure:

```powershell
Connect-AzAccount
```

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myNetworkSecurityGroup*a *myVnet*.

Vytvořte pravidlo pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule* , které povoluje přenosy *tcp* na portu *80*:

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

Dále vytvořte skupinu zabezpečení sítě pomocí příkazu [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) a přiřaďte právě vytvořené pravidlo protokolu HTTP následujícím způsobem. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teď k podsíti přiřadíme skupinu zabezpečení sítě. Následující příklad přiřadí existující virtuální síť s názvem *myVnet* k proměnné *$VNet* pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Přidružte skupinu zabezpečení sítě k vaší podsíti pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Následující příklad přidruží podsíť s názvem *mySubnet* ke skupině zabezpečení sítě:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Nakonec aktualizujte svou virtuální síť pomocí [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) , aby se provedené změny projevily:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy, které vám pomůžou začít pracovat s přenosem dat do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitosti pro řízení přístupu k vašim prostředkům. Tady si můžete přečíst další informace o [vytváření skupin zabezpečení sítě a pravidel seznamu ACL](tutorial-virtual-network.md#secure-network-traffic).

Pro webové aplikace s vysokou dostupností byste měli své virtuální počítače umístit za Azure Load Balancer. Nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování přenosů. Další informace najdete v tématu [Postup při vyrovnávání zatížení virtuálních počítačů se systémem Linux v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto příkladu jste vytvořili jednoduché pravidlo pro povolení přenosů HTTP. Informace o vytváření podrobnějších prostředí najdete v následujících článcích:

* [Přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/network-security-groups-overview.md)
* [Přehled Azure Load Balancer](../../load-balancer/load-balancer-overview.md)