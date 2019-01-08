---
title: Filtrování síťového provozu – Azure PowerShell | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak filtrovat síťový provoz do podsítě se skupinou zabezpečení sítě pomocí Powershellu.
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
ms.custom: mvc
ms.openlocfilehash: 92c1e706d3cbcff8bb0d66ddf4f2e73362a96af6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064521"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrování síťového provozu se skupinou zabezpečení sítě pomocí Powershellu

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto článku získáte informace o těchto tématech:

* Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
* Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
* Nasazení virtuálních počítačů do podsítě
* Testování filtrů provozu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte modul Azure PowerShell verze 6.2.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení určují zdroj a cíl. Zdroji a cíli můžou být skupiny zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky vytvořené v tomto článku se [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků v umístění *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte skupinu zabezpečení aplikací pomocí rutiny [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými požadavky na filtrování portů. Následující příklad vytvoří dvě skupiny zabezpečení aplikací.

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

### <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

Vytvořte pravidlo zabezpečení pomocí rutiny [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Následující příklad vytvoří pravidlo povolující příchozí provoz z internetu do skupiny zabezpečení aplikací *myWebServers* na portech 80 a 443:

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

V tomto článku protokolu RDP (portu 3389) je přístupný z Internetu pro *myAsgMgmtServers* virtuálního počítače. V produkčních prostředích se místo zveřejňování portu 3389 na internetu doporučuje připojovat k prostředkům Azure, které chcete spravovat, pomocí sítě [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátního](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťového připojení.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí rutiny [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Pomocí rutiny [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) vytvořte konfiguraci podsítě a pak tuto konfiguraci podsítě zapište do virtuální sítě pomocí rutiny [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). Následující příklad do virtuální sítě přidá podsíť *mySubnet* a přidruží k ní skupinu zabezpečení sítě *myNsg*:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Před vytvořením virtuálních počítačů načtěte objekt virtuální sítě s podsítí pomocí rutiny [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Vytvořte pro jednotlivé virtuální počítače veřejné IP adresy pomocí rutiny [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Pomocí rutiny [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) vytvořte dvě síťová rozhraní a přiřaďte k nim veřejné IP adresy. Následující příklad vytvoří síťové rozhraní, přidruží k němu veřejnou IP adresu *myVmWeb* a udělá z něj člena skupiny zabezpečení aplikací *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Následující příklad vytvoří síťové rozhraní, přidruží k němu veřejnou IP adresu *myVmMgmt* a udělá z něj člena skupiny zabezpečení aplikací *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Vytvořte ve virtuální síti dva virtuální počítače, abyste v pozdějším kroku mohli ověřit filtrování provozu. 

Pomocí rutiny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) vytvořte konfiguraci virtuálního počítače a pak vytvořte virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku: 

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

Vytvořte virtuální počítač, který bude sloužit jako server pro správu:

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

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte dalším krokem, dokud Azure nedokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

Pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) získejte veřejnou IP adresu virtuálního počítače. Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Pomocí následujícího příkazu vytvořte ze svého místního počítače relaci vzdálené plochy s virtuálním počítačem *myVmMgmt*. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Otevřete stažený soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.

Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování. 
   
Připojení bude úspěšné, protože do skupiny zabezpečení aplikací *myAsgMgmtServers*, která obsahuje síťové rozhraní připojené k virtuálnímu počítači *myVmMgmt*, je povolený příchozí provoz z internetu na portu 3389.

Pomocí následujícího příkazu v PowerShellu vytvořte připojení ke vzdálené ploše virtuálního počítače *myVmWeb* z virtuálního počítače *myVmMgmt*:

``` 
mstsc /v:myvmWeb
```

Připojení bude úspěšné, protože výchozí pravidlo zabezpečení v obou skupinách zabezpečení sítě povoluje provoz na všech portech mezi všemi IP adresami v rámci virtuální sítě. K virtuálnímu počítači *myVmWeb* nemůžete vytvořit připojení ke vzdálené ploše z internetu, protože pravidlo zabezpečení pro skupinu zabezpečení aplikací *myAsgWebServers* nepovoluje příchozí provoz na portu 3389 z internetu.

Pomocí následujícího příkazu v PowerShellu nainstalujte na virtuální počítač *myVmWeb* službu Microsoft IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po dokončení instalace služby IIS se odpojte od virtuálního počítače *myVmWeb*. Stále zůstanete připojeni ke vzdálené ploše virtuálního počítače *myVmMgmt*. Pokud chcete zobrazit úvodní obrazovku služby IIS, otevřete internetový prohlížeč a přejděte na adresu http://myVmWeb.

Odpojte se od virtuálního počítače *myVmMgmt*.

Zadáním následujícího příkazu v PowerShellu na svém počítači načtěte veřejnou IP adresu serveru *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Pokud chcete ověřit, že máte přístup k webovému serveru *myVmWeb* i mimo Azure, otevřete na svém počítači internetový prohlížeč a přejděte na adresu `http://<public-ip-address-from-previous-step>`. Připojení bude úspěšné, protože do skupiny zabezpečení aplikací *myAsgWebServers*, která obsahuje síťové rozhraní připojené k virtuálnímu počítači *myVmWeb*, je povolený příchozí provoz z internetu na portu 80.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružené k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](security-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Další informace o postupu [vytvořit směrovací tabulku](tutorial-create-route-table-powershell.md).
