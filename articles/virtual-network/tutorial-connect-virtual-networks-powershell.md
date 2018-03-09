---
title: "Připojit virtuální sítě pomocí virtuální sítě partnerský vztah – prostředí PowerShell | Microsoft Docs"
description: "Zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí prostředí PowerShell vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. Tento článek se týká vytvoření vztahu a partnerského vztahu dvě virtuální sítě. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte dvě virtuální sítě
> * Vytvoření partnerského vztahu mezi virtuálními sítěmi
> * Testování partnerského vztahu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 3,6 nebo novější. Spustit ` Get-Module -ListAvailable AzureRM` najít nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítí s předponou adresy 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zapsat konfiguraci podsítě virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Vytvoření virtuální sítě s předponu adresy 10.1.0.0/16 a jednu podsíť:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

Předpona adresy pro *myVirtualNetwork2* virtuální sítě se nepřekrývá s předponou adresy z *myVirtualNetwork1* virtuální sítě. Nejde partnerský uzel virtuálních sítí s překrývajícími se předpony adres.

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

Vytvoření partnerského vztahu s [přidat AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). Následující příklad partnerské uzly *myVirtualNetwork1* k *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **PeeringState** je *získaných*. Partnerského vztahu zůstane v aplikaci *získaných* stavu, dokud nevytvoříte partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **PeeringState** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerského vztahu k *připojeno*. Potvrďte, že stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerský vztah změnit tak, aby *připojeno* s [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Prostředky v jedné virtuální sítě nemůže komunikovat s prostředky v jiné virtuální síti, dokud **PeeringState** pro partnerské vztahy v obě virtuální sítě je *připojeno*. 

Partnerské vztahy jsou mezi dvěma virtuálními sítěmi, ale nejsou přechodné. Ano, například pokud jste chtěli také peer *myVirtualNetwork2* k *myVirtualNetwork3*, musíte vytvořit další vztahy mezi virtuálními sítěmi *myVirtualNetwork2* a *myVirtualNetwork3*. I když *myVirtualNetwork1* je peered s *myVirtualNetwork2*, prostředků v rámci *myVirtualNetwork1* může jenom přístup k prostředkům v  *myVirtualNetwork3* Pokud *myVirtualNetwork1* byl také peered s *myVirtualNetwork3*. 

Před partnerský vztah produkční virtuální sítě, je doporučeno, důkladně Seznamte se s [partnerského vztahu přehled](virtual-network-peering-overview.md), [Správa partnerský vztah](virtual-network-manage-peering.md), a [limity virtuální sítě ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). I když tento článek ukazuje, partnerský vztah mezi dvěma virtuálními sítěmi ve stejném předplatném a umístění, můžete také partnerský uzel virtuálních sítí v [různých oblastech](#register) a [různých předplatných Azure](create-peering-different-subscriptions.md#powershell). Můžete také vytvořit [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah.

## <a name="test-peering"></a>Testování partnerského vztahu

Chcete-li otestovat síťové komunikace mezi virtuálními počítači v různých virtuálních sítích prostřednictvím partnerský vztah, nasazení virtuálního počítače pro každou podsíť a pak komunikaci mezi virtuálními počítači. 

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače v každé virtuální sítě, abyste mohli ověřit komunikace mezi nimi později.

Vytvoření virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se chcete přihlásit k virtuálnímu počítači s.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure automaticky přiřadí 10.0.0.4 jako privátní IP adresu virtuálního počítače, protože se první dostupná IP adresa v 10.0.0.4 *Subnet1* z *myVirtualNetwork1*. 

Vytvoření virtuálního počítače v *myVirtualNetwork2* virtuální sítě.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Virtuální počítač trvá několik minut pro vytvoření. I když není ve vrácené výstupu Azure přiřazen 10.1.0.4 jako privátní IP adresu virtuálního počítače, protože 10.1.0.4 je první dostupná IP adresa v *Subnet1* z *myVirtualNetwork2*. 

Dalších krocích nepokračujte, dokud se vytvoří virtuální počítač Azure a vrátí výstup do prostředí PowerShell.

### <a name="test-virtual-machine-communication"></a>Testovací virtuální počítač komunikace

Virtuální počítač veřejné IP adresy můžete připojit z Internetu. Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVm1* virtuálního počítače:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVm1* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen, stažena do počítače a otevřít. Zadejte uživatelské jméno a heslo (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače) a pak klikněte na tlačítko  **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Z příkazového řádku, povolit ping přes bránu Windows firewall, aby může odeslat příkaz ping tento virtuální počítač z *Můjvp2* později.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

I když příkazu ping je v tomto článku použít pro testování, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

Pro připojení k *Můjvp2* virtuální počítač, zadejte následující příkaz z příkazového řádku *myVm1* virtuálního počítače:

```
mstsc /v:10.1.0.4
```

Vzhledem k tomu, že jste povolili příkazu ping na *myVm1*, vám může nyní odeslat příkaz ping ho podle IP adresy z příkazového řádku na *Můjvp2* virtuálního počítače:

```
ping 10.0.0.4
```

Obdržíte čtyři odpovědi. Pokud jste příkaz ping podle názvu virtuálního počítače (*myVm1*), místo jeho IP adresu, ping nezdaří, protože *myVm1* název Neznámý hostitel. Rozlišení názvů výchozí Azure funguje mezi virtuálními počítači ve stejné virtuální síti, ale není mezi virtuálními počítači v různých virtuálních sítích. Překládat názvy virtuálních sítí, je nutné [nasazení serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) nebo použijte [privátní domén Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vaše relace protokolu RDP na obě odpojit *myVm1* a *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Registrace pro partnerského vztahu preview globální virtuální sítě**

Vytváření partnerských vztahů virtuálních sítí ve stejné oblasti je všeobecně dostupné. Partnerský vztah virtuální sítě v různých oblastech je aktuálně ve verzi preview. V tématu [aktualizace virtuální sítě](https://azure.microsoft.com/updates/?product=virtual-network) pro dostupné oblasti. Rovnocenných počítačů virtuálních sítí v oblastech, nejprve je nutné zaregistrovat verzi Preview, pomocí následujících kroků (v rámci předplatného, které každý virtuální síť, kterou chcete peer se):

1. Registrujte předplatné, které každý virtuální síť, které chcete peer se ve verzi Preview zadáním následujících příkazů:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Pokud se pokusíte rovnocenných počítačů v různých oblastech před virtuální sítě **RegistrationState** výstupu se zobrazí po zadání příkazu předchozí je **registrovaná** oba odběry partnerský vztah selže .

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili připojení dvě sítě pomocí virtuální sítě partnerský vztah. Můžete [svého počítače připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a komunikovat s prostředky ve virtuální síti, nebo peered virtuální sítě.

Nadále ukázky skriptu pro opakovaně použitelné skripty k dokončení mnoho úloh, které jsou popsané v článcích virtuální sítě.

> [!div class="nextstepaction"]
> [Ukázky skriptu virtuální sítě](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
