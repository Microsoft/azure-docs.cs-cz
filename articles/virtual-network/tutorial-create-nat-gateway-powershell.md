---
title: 'Kurz: Vytvoření brány NAT – PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Začněte vytvářet bránu NAT pomocí Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620225"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Kurz: Vytvoření brány NAT pomocí Azure PowerShell

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť.
> * Vytvoří virtuální počítač.
> * Vytvořte bránu NAT a přidružte ji k virtuální síti.
> * Připojte se k virtuálnímu počítači a ověřte IP adresu NAT.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

V této části vytvoříme bránu NAT a podpůrné prostředky.

* Pro přístup k Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) vytvořte prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**. 

* Vytvořte globální bránu Azure NAT pomocí [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Výsledkem tohoto příkazu je vytvoření prostředku brány s názvem **myNATgateway** , který používá veřejnou IP adresu **myPublicIP**. Časový limit nečinnosti je nastavený na 10 minut.  

* Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) v **myResourceGroup** pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Adresní prostor IP adres pro virtuální síť je **10.1.0.0/16**. Podsíť ve virtuální síti je **10.1.0.0/24**.

* Pro přístup k virtuálnímu počítači vytvořte hostitele Azure bastionu s názvem **myBastionHost** . Pomocí [New-AzBastion](/powershell/module/az.network/new-azbastion) Vytvořte hostitele bastionu. Vytvořte veřejnou IP adresu pro hostitele bastionu pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Virtuální počítač

V této části vytvoříte virtuální počítač pro otestování brány NAT a ověříte veřejnou IP adresu odchozího připojení.

* Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)nastavte uživatelské jméno a heslo správce pro virtuální počítač.

* Vytvořit virtuální počítač pomocí:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Než přejdete k další části, počkejte, než se dokončí vytváření virtuálního počítače.

## <a name="test-nat-gateway"></a>Test brány NAT

V této části otestujeme bránu NAT. Nejdříve zjistíme veřejnou IP adresu brány NAT. Pak se připojíme k testovacímu virtuálnímu počítači a ověříme odchozí připojení prostřednictvím brány NAT.
    
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu pro bránu NAT. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP**.

2. Poznamenejte si veřejnou IP adresu:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Zjistit veřejnou IP adresu brány NAT" border="true":::

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředky vyberte **myVM** , která je umístěná ve skupině prostředků **myResourceGroupNAT** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. **https://whatsmyip.com** Do adresního řádku zadejte.

9. Ověřte, že zobrazená IP adresa odpovídá adrese brány NAT, kterou jste si poznamenali v předchozím kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer zobrazující externí odchozí IP adresy" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a bránu NAT pomocí následujících kroků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Virtual Network NAT najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Přehled služby Virtual Network NAT](nat-overview.md)
