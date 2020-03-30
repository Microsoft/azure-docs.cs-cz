---
title: Otevření portů k virtuálnímu počítači pomocí Azure PowerShellu
description: Zjistěte, jak otevřít port nebo vytvořit koncový bod pro virtuální počítač s Windows pomocí režimu nasazení Správce prostředků Azure a Azure PowerShellu
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 547ca9c98d77b2aaa6d3630bff4b2ec10dcc5be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754166"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Jak otevřít porty a koncové body k virtuálnímu počítači v Azure pomocí PowerShellu
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
Chcete-li vytvořit skupinu zabezpečení sítě a pravidla acl, potřebujete [nainstalovanou nejnovější verzi prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs). Tyto kroky můžete provést také [pomocí portálu Azure](nsg-quickstart-portal.md).

Přihlaste se ke svému účtu Azure:

```powershell
Connect-AzAccount
```

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami. Mezi příklady názvů parametrů patřily *myResourceGroup*, *myNetworkSecurityGroup*a *myVnet*.

Vytvořte pravidlo pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule,* které povolí přenos *protokolu TCP* na portu *80*:

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

Dále vytvořte skupinu zabezpečení sítě pomocí [skupiny New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) a přiřaďte právě vytvořené pravidlo HTTP následujícím způsobem. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nyní přiřazujeme skupinu zabezpečení sítě k podsíti. Následující příklad přiřadí existující virtuální síť s názvem *myVnet* proměnné *$vnet* s [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Přidružte skupinu zabezpečení sítě k podsíti k [programu Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Následující příklad přidruží podsíť s názvem *mySubnet* ke skupině zabezpečení sítě:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Nakonec aktualizujte virtuální síť pomocí [programu Set-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) aby se změny projevily:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy zde umožňují vstát a spustit s provozem proudícím do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a rozlišovací schopnost pro řízení přístupu k prostředkům. Další informace o [vytváření skupiny zabezpečení sítě a pravidel seznamu Řízení přístupu](tutorial-virtual-network.md#secure-network-traffic)naleznete zde .

Pro vysoce dostupné webové aplikace byste měli umístit virtuální počítače za Azure Balancer. Správce načítání distribuuje provoz do virtuálních pozemních disponujících pomocí skupiny zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [Jak vyrovnat vyrovnávání zatížení virtuálních počítačů Linuxu v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos http. Informace o vytváření podrobnějších prostředí naleznete v následujících článcích:

* [Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)
* [Přehled nástroje pro vyrovnávání zatížení Azure](../../load-balancer/load-balancer-overview.md)

