---
title: Filtrování provozu sítě přenosů - prostředí Azure PowerShell | Microsoft Docs
description: V tomto článku zjistěte, jak k filtrování provozu sítě pro podsíť s skupinu zabezpečení sítě pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 53406150cfc2ec4229ecd9cf3356ad03d60f8e7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrování provozu sítě s skupinu zabezpečení sítě pomocí prostředí PowerShell

Síťový provoz na příchozí a odchozí z podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, které umožňují filtrovat provoz sítě podle IP adresy, portu a protokolu. Pro prostředky nasazené v podsíti, platí pravidla zabezpečení. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvořit skupinu a zabezpečení pravidla zabezpečení sítě
> * Vytvoření virtuální sítě a přidružte skupinu zabezpečení sítě pro podsíť
> * Nasazení virtuálních počítačů (VM) do podsítě
> * Filtry přenosů testu

Pokud dáváte přednost, můžete dokončit pomocí tohoto článku [rozhraní příkazového řádku Azure](tutorial-filter-network-traffic-cli.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení zadejte zdroj a cíl. Zdroje a cíle může být skupin zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky, které jsou vytvořené v tomto článku se [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků v *eastus* umístění: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvoření skupiny zabezpečení aplikace s [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Skupina zabezpečení aplikace umožňuje skupiny serverů s podobnou port filtrování požadavků. Následující příklad vytvoří dvě skupiny zabezpečení aplikace.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Vytvoření pravidla zabezpečení

Vytvořit pravidlo zabezpečení s [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Následující příklad vytvoří pravidlo, které umožňuje přenos příchozí z Internetu, aby *myWebServers* skupiny zabezpečení aplikací přes porty 80 a 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

V tomto článku je protokolu RDP (portu 3389) přístup k Internetu pro *myAsgMgmtServers* virtuálních počítačů. Pro provozní prostředí, místo vystavení portu 3389 k Internetu, se doporučuje, že se připojit k prostředky Azure, které chcete spravovat pomocí [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátní](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťové připojení.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořit skupinu zabezpečení sítě s [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální s názvem *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)a pak zapsat konfiguraci podsítě do virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). Následující příklad přidá podsíť s názvem *mySubnet* do virtuální sítě a známými *myNsg* skupinu zabezpečení sítě k němu:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Před vytvořením virtuálních počítačů se načíst objekt virtuální síť s podsítí s [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Vytvoření veřejné IP adresy pro každý virtuální počítač s [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Vytvořit dvě síťové rozhraní s [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)a veřejnou IP adresu přiřadit síťové rozhraní. Následující příklad vytvoří rozhraní sítě, partnerů *myVmWeb* veřejnou IP adresu a je členem skupiny *myAsgWebServers* skupiny zabezpečení aplikací:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Následující příklad vytvoří rozhraní sítě, partnerů *myVmMgmt* veřejnou IP adresu a je členem skupiny *myAsgMgmtServers* skupiny zabezpečení aplikací:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit filtrování v pozdější fázi provozu. 

Vytvoření konfigurace virtuálního počítače s [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), pak vytvořte virtuální počítač s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat na další krok: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Vytvoření virtuálního počítače sloužit jako server pro správu:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Virtuální počítač trvá několik minut pro vytvoření. Nemusíte pokračovat dalším krokem, dokud Azure dokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Filtry přenosů testu

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVmMgmt* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVmMgmt* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.

Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), potom vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení. 
   
Je připojení úspěšné, protože port 3389 je povolena příchozí z Internetu, aby *myAsgMgmtServers* skupina zabezpečení aplikací, která síťové rozhraní připojené k *myVmMgmt* virtuální počítač.

Použijte následující příkaz k vytvoření připojení ke vzdálené ploše *myVmWeb* virtuální počítač, z *myVmMgmt* virtuální počítač, pomocí následujícího příkazu z prostředí PowerShell:

``` 
mstsc /v:myvmWeb
```

Je připojení úspěšné, protože výchozí pravidlo zabezpečení v rámci jednotlivých skupin zabezpečení sítě umožňuje provoz přes všechny porty mezi všechny IP adresy v rámci virtuální sítě. Nelze vytvořit připojení ke vzdálené ploše na *myVmWeb* virtuálních počítačů z Internetu protože pravidla zabezpečení pro *myAsgWebServers* neumožňuje portu 3389 příchozí z Internetu.

Použijte následující příkaz k instalaci Microsoft IIS na *myVmWeb* virtuálních počítačů z prostředí PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po dokončení instalace služby IIS odpojit od *myVmWeb* virtuálních počítačů, což zanechá v *myVmMgmt* připojení ke vzdálené ploše virtuálního počítače. Můžete zobrazit na úvodní obrazovce služby IIS, otevřete internetový prohlížeč a přejděte do http://myVmWeb.

Odpojení od *myVmMgmt* virtuálních počítačů.

V počítači, zadejte následující příkaz z prostředí PowerShell k načtení veřejnou IP adresu *myVmWeb* serveru:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Potvrďte, že máte přístup *myVmWeb* webový server z mimo Azure, otevřete internetový prohlížeč na váš počítač a přejděte do `http://<public-ip-address-from-previous-step>`. Je připojení úspěšné, protože port 80 je povolena příchozí z Internetu, aby *myAsgWebServers* skupina zabezpečení aplikací, která síťové rozhraní připojené k *myVmWeb* virtuální počítač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružené k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupiny zabezpečení sítě](security-overview.md) a [spravovat skupinu zabezpečení sítě](virtual-network-manage-nsg-arm-ps.md).

Provoz Azure směrování mezi podsítěmi ve výchozím nastavení. Místo toho můžete směrovat provoz mezi podsítěmi pomocí virtuálních počítačů, slouží jako bránu firewall, například. Informace o tom, chcete-li vytvořit směrovací tabulku, přechodu na další článek.

> [!div class="nextstepaction"]
> [Vytvořit směrovací tabulku](./tutorial-create-route-table-portal.md)