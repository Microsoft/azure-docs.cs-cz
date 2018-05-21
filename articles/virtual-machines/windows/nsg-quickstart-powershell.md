---
title: Otevřete porty, které se virtuální počítač pomocí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak otevřít port / create koncového bodu váš virtuální počítač s Windows pomocí režimu nasazení Azure resource Manageru a prostředí Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: a7564c19f8318d62260d03b92f8115c8f3fa887a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Postup otevření portů a koncové body k virtuálnímu počítači v Azure pomocí prostředí PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
Vytvořte skupinu zabezpečení sítě a seznamu ACL pravidel potřebujete [nejnovější verzi prostředí Azure PowerShell nainstalovaný](/powershell/azureps-cmdlets-docs). Můžete také [proveďte tyto kroky, pomocí webu Azure portal](nsg-quickstart-portal.md).

Přihlaste se k účtu Azure:

```powershell
Connect-AzureRmAccount
```

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. Názvy parametrů příklad zahrnuté *myResourceGroup*, *myNetworkSecurityGroup*, a *myVnet*.

Vytvořit pravidlo s [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule* umožňující *tcp* přenosy na portu *80*:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
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

Dále vytvořte skupině zabezpečení sítě s [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) a přiřaďte pravidlo HTTP jste právě vytvořili, se následujícím způsobem. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teď umožňuje přiřadit skupině zabezpečení sítě k podsíti. Následující příklad přiřadí existující virtuální síť s názvem *myVnet* proměnnou *$vnet* s [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Vaše skupina zabezpečení sítě přidružit podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). Následující příklad přiřadí podsíť s názvem *mySubnet* s vaší skupiny zabezpečení sítě:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Nakonec aktualizujte virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) aby změny vstoupily v platnost:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožňují zprovoznění s provoz do virtuálního počítače. Skupiny zabezpečení sítě zadejte mnoho funkcí a členitosti pro řízení přístupu k prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](tutorial-virtual-network.md#secure-network-traffic).

Pro vysokou dostupnost webové aplikace měli byste umístit virtuální počítače za pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů s skupinu zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [jak načíst vyvážit virtuální počítače s Linuxem v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos HTTP. Můžete najít informace o vytváření podrobnější prostředí v těchto článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)
* [Přehled Azure Resource Manageru pro vyrovnávání zatížení](../../load-balancer/load-balancer-arm.md)

