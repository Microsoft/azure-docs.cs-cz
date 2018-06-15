---
title: Vytvoření virtuální sítě Azure s několika podsítěmi – prostředí PowerShell | Microsoft Docs
description: Naučte se vytvořit virtuální síť s více podsítěmi pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880560"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Vytvoření virtuální sítě s několika podsítěmi pomocí prostředí PowerShell

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Vytvoření více podsítí ve virtuální síti umožňuje segmentovat vaší síti, takže můžete filtrovat nebo řízení toku provozu mezi podsítěmi. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření podsítě
> * Testování síťovou komunikaci mezi virtuální počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje prostředí Azure PowerShell verze modulu 3,6 nebo novější. Spustit ` Get-Module -ListAvailable AzureRM` najít nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *EastUS* umístění.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

**AddressPrefix** je zadat v notaci CIDR. Předpona zadaná adresa patří 10.0.0.0-10.0.255.254 adresy IP.

## <a name="create-a-subnet"></a>Vytvoření podsítě

Nejdřív vytvoříte konfiguraci podsítě, a aktualizujte virtuální síť s konfigurací podsítě se vytvoří podsíť. Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří dvě konfigurací podsítě pro *veřejné* a *privátní* podsítě:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

**AddressPrefix** zadaná pro podsíť musí být v rámci předpona adresy definované pro virtuální síť. Azure DHCP přiřadí z adres předpony podsítě IP adres v podsíti nejsou nasazené prostředky. Azure přiřadí prostředky nasazené v rámci pouze adresy 10.0.0.4-10.0.0.254 **veřejné** podsíť, protože Azure si vyhrazuje první čtyři adres (10.0.0.0-10.0.0.3 podsítě, v tomto příkladu) a poslední adres ( 10.0.0.255 podsítě, v tomto příkladu) v každé podsíti.

Zápis konfigurací podsítě pro virtuální síť s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsítí ve virtuální síti:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Před nasazením virtuálních sítí Azure a podsítě pro použití v provozním prostředí, se doporučuje, že jste důkladně Seznamte se s adresním prostorem [aspekty](manage-virtual-network.md#create-a-virtual-network) a [limity virtuální síťové](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Jakmile se prostředky nasadí do podsítí, může vyžadovat některé virtuální síť a podsíť změny, jako je například změna rozsahy adres, opakované nasazení existující prostředky Azure, které jsou nasazeny v rámci podsítě.

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste je mohli otestovat síťovou komunikaci mezi nimi a Internetu v pozdější fázi. 

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVmWeb* v *veřejné* podsíť *myVirtualNetwork* virtuální sítě. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se chcete přihlásit k virtuálnímu počítači s.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

10.0.0.4 Azure přiřadí automaticky jako privátní IP adresu virtuálního počítače, protože 10.0.0.4 je první dostupná IP adresa v *veřejné* podsítě. 

Vytvoření virtuálního počítače v *privátní* podsítě.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

Virtuální počítač trvá několik minut pro vytvoření. I když není ve vrácené výstupu Azure přiřazen 10.0.1.4 jako privátní IP adresu virtuálního počítače, protože 10.0.1.4 je první dostupná IP adresa v *privátní* podsíť *myVirtualNetwork*. 

Zbývající kroky nepokračujte, dokud je virtuální počítač vytvořený a vrátí výstup prostředí PowerShell.

Virtuální počítače vytvořené v tomto článku mít jeden [síťové rozhraní](virtual-network-network-interface.md) s jednu IP adresu, která se dynamicky přiřadit síťové rozhraní. Poté, co nasadíte virtuální počítač, můžete [přidat víc veřejných a privátních IP adres, nebo změnit způsob přiřazení IP adresy statické](virtual-network-network-interface-addresses.md#add-ip-addresses). Můžete [přidat síťových rozhraní](virtual-network-network-interface-vm.md#vm-add-nic), až do limitu nepodporuje [velikost virtuálního počítače](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vyberete při vytváření virtuálního počítače. Můžete také [povolit single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) virtuálních počítačů, ale pouze tehdy, když vytvoření virtuálního počítače s velikostí virtuálních počítačů, která podporuje možnost.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikace mezi virtuálními počítači a s Internetem

Virtuální počítač veřejné IP adresy můžete připojit z Internetu. Vytvoření Azure *myVmMgmt* virtuálního počítače, na veřejnou IP adresu s názvem *myVmMgmt* také vytvořil a přiřazené k virtuálnímu počítači. Když virtuální počítač není potřeba mít přiřazené veřejnou IP adresu, Azure přiřadí veřejnou IP adresu pro každý virtuální počítač, který vytvoříte, ve výchozím nastavení. K virtuálnímu počítači komunikovat z Internetu, musí mít veřejnou IP adresu přiřazenou k virtuálnímu počítači. Všechny virtuální počítače mohou komunikovat odchozí přes Internet, zda je k virtuálnímu počítači přiřazena veřejnou IP adresu. Další informace o odchozí připojení k Internetu v Azure najdete v tématu [odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVmMgmt* virtuálního počítače:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVmMgmt* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen, stažena do počítače a otevřít. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače) a potom klikněte na **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení. 

V pozdější fázi, ping slouží ke komunikaci s *myVmMgmt* virtuální počítač z *myVmWeb* virtuálního počítače. Příkaz ping používá protokol ICMP, který byl odepřen přes bránu Windows Firewall ve výchozím nastavení. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z příkazového řádku:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

I když v tomto článku je použít příkaz ping, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

Z bezpečnostních důvodů je běžné omezit počet virtuálních počítačů, které můžete vzdáleně připojit k ve virtuální síti. V tomto kurzu *myVmMgmt* virtuální počítač se používá ke správě *myVmWeb* virtuálního počítače ve virtuální síti. Použijte následující příkaz pro vzdálenou plochu *myVmWeb* virtuální počítač z *myVmMgmt* virtuálního počítače:

``` 
mstsc /v:myVmWeb
```

Pro komunikaci se *myVmMgmt* virtuální počítač z *myVmWeb* virtuální počítač, zadejte následující příkaz z příkazového řádku:

```
ping myvmmgmt
```

Zobrazí se výstup podobný výstupu v následujícím příkladu:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Uvidíte, že adresa *myVmMgmt* 10.0.1.4 je virtuální počítač. 10.0.1.4 se první dostupná IP adresa v rozsahu adres *privátní* podsítě, které jste nasadili *myVmMgmt* virtuálního počítače v předchozím kroku.  Zjistíte, že plně kvalifikovaný název domény virtuálního počítače je *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. I když *dar5p44cif3ulfq00wxznl3i3f* část názvu domény se liší pro virtuální počítač, zbývající části názvu domény jsou stejné. Ve výchozím nastavení používají všechny virtuální počítače Azure výchozí služba Azure DNS. Všechny virtuální počítače v rámci virtuální sítě můžete vyřešit názvy všechny ostatní virtuální počítače ve stejné virtuální síti pomocí služby DNS Azure, je výchozí. Místo použití služba DNS výchozí Azure, můžete použít serveru DNS nebo funkcí privátní domény služby Azure DNS. Podrobnosti najdete v tématu [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) nebo [pomocí Azure DNS pro domény privátní](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Instalace Internetové informační služby (IIS) pro systém Windows Server na *myVmWeb* virtuální počítač, zadejte následující příkaz z relace prostředí PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po dokončení instalace služby IIS odpojit *myVmWeb* relace vzdálené plochy, což zanechá v *myVmMgmt* relace vzdálené plochy. Otevřete webový prohlížeč a přejděte do http://myvmweb. Zobrazí úvodní stránce služby IIS.

Odpojení *myVmMgmt* relace vzdálené plochy.

Získat veřejnou adresu Azure přiřazené *myVmWeb* virtuálního počítače:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Ve vašem počítači, přejděte na veřejnou IP adresu *myVmWeb* virtuálního počítače. Pokus o zobrazení úvodní stránka IIS ze svého počítače selže. Pokus selže, protože když byly nasazené virtuální počítače, Azure vytvoří skupinu zabezpečení sítě pro každý virtuální počítač, ve výchozím nastavení. 

Skupina zabezpečení sítě obsahuje pravidla zabezpečení, která povolují nebo odepírají příchozí a odchozí síťový provoz pomocí portu a IP adresy. Výchozí skupina zabezpečení sítě, který vytvořili Azure umožňuje komunikaci přes všechny porty mezi prostředky ve stejné virtuální síti. Pro virtuální počítače s Windows skupinu zabezpečení sítě výchozí odmítne veškerý příchozí provoz z Internetu přes všechny porty, potvrďte TCP port 3389 (RDP). V důsledku toho ve výchozím nastavení, můžete také RDP přímo na *myVmWeb* virtuálního počítače z Internetu, i když není vhodné portu 3389 otevřete na webový server. Vzhledem k tomu, že procházení webu komunikuje přes port 80, komunikaci z Internetu se nezdaří, protože není pravidlo ve skupině zabezpečení výchozí sítě umožňuje provoz přes port 80.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nasadíte virtuální síť s více podsítěmi. Také jste zjistili, že při vytváření virtuálního počítače s Windows Azure vytvoří rozhraní sítě, připojí k virtuálnímu počítači a vytvoří skupinu zabezpečení sítě, který umožňuje pouze provoz přes port 3389, z Internetu. Přechodu na dalším kurzu se dozvíte, jak filtrovat síťový provoz do podsítí, nikoli pro jednotlivé virtuální počítače.

> [!div class="nextstepaction"]
> [Filtr síťový provoz do podsítí](./virtual-networks-create-nsg-arm-ps.md)
