---
title: Filtrovat síťový provoz – Azure PowerShell | Microsoft Docs
description: V tomto článku se dozvíte, jak filtrovat síťový provoz do podsítě a pomocí skupiny zabezpečení sítě pomocí PowerShellu.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9dca3d95f9fe810c90c27a32250d1b8ffcf89ac8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065340"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí PowerShellu

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto článku získáte informace o těchto tématech:

* Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
* Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
* Nasazení virtuálních počítačů do podsítě
* Testování filtrů provozu

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení určují zdroj a cíl. Zdroji a cíli můžou být skupiny zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky vytvořené v tomto článku pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků v umístění *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte skupinu zabezpečení aplikace pomocí [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými požadavky na filtrování portů. Následující příklad vytvoří dvě skupiny zabezpečení aplikací.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

Vytvořte pravidlo zabezpečení pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující příklad vytvoří pravidlo povolující příchozí provoz z internetu do skupiny zabezpečení aplikací *myWebServers* na portech 80 a 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
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

$mgmtRule = New-AzNetworkSecurityRuleConfig `
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

V tomto článku se k Internetu pro virtuální počítač *myAsgMgmtServers* zveřejňuje protokol RDP (port 3389). V produkčních prostředích se místo zveřejňování portu 3389 na internetu doporučuje připojovat k prostředkům Azure, které chcete spravovat, pomocí sítě [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátního](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťového připojení.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)a pak zapište konfiguraci podsítě do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). Následující příklad do virtuální sítě přidá podsíť *mySubnet* a přidruží k ní skupinu zabezpečení sítě *myNsg*:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Před vytvořením virtuálních počítačů načtěte objekt virtuální sítě s podsítí pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Vytvořte veřejnou IP adresu pro každý virtuální počítač pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)vytvořte dvě síťová rozhraní a k síťovému rozhraní přiřaďte veřejnou IP adresu. Následující příklad vytvoří síťové rozhraní, přidruží k němu veřejnou IP adresu *myVmWeb* a udělá z něj člena skupiny zabezpečení aplikací *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Následující příklad vytvoří síťové rozhraní, přidruží k němu veřejnou IP adresu *myVmMgmt* a udělá z něj člena skupiny zabezpečení aplikací *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Vytvořte ve virtuální síti dva virtuální počítače, abyste v pozdějším kroku mohli ověřit filtrování provozu.

Vytvořte konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)a pak vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
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
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte dalším krokem, dokud Azure nedokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

K vrácení veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVmMgmt*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
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

Po dokončení instalace služby IIS se odpojte od virtuálního počítače *myVmWeb*. Stále zůstanete připojeni ke vzdálené ploše virtuálního počítače *myVmMgmt*. Chcete-li zobrazit uvítací obrazovku služby IIS, otevřete internetový prohlížeč a přejděte na adresu http: \/ /myVmWeb.

Odpojte se od virtuálního počítače *myVmMgmt*.

Zadáním následujícího příkazu v PowerShellu na svém počítači načtěte veřejnou IP adresu serveru *myVmWeb*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Pokud chcete ověřit, že máte přístup k webovému serveru *myVmWeb* i mimo Azure, otevřete na svém počítači internetový prohlížeč a přejděte na adresu `http://<public-ip-address-from-previous-step>`. Připojení bude úspěšné, protože do skupiny zabezpečení aplikací *myAsgWebServers*, která obsahuje síťové rozhraní připojené k virtuálnímu počítači *myVmWeb*, je povolený příchozí provoz z internetu na portu 80.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružíte ji k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Další informace o postupu najdete v tématu [Vytvoření směrovací tabulky](tutorial-create-route-table-powershell.md).