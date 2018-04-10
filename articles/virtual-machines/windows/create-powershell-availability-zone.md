---
title: Vytvoření virtuálního počítače s Windows v zóně – Azure PowerShell | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Windows v zóně dostupnosti pomocí Azure PowerShellu
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 4b6ae95d9b8f7cc4924ea89a743cf9878c7dd79a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>Vytvoření virtuálního počítače s Windows v zóně dostupnosti pomocí PowerShellu

Tento článek podrobně popisuje použití Azure PowerShellu k vytvoření virtuálního počítače Azure se systémem Windows Server 2016 v zóně dostupnosti Azure. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete využít zóny dostupnosti, vytvořte virtuální počítač v [podporované oblasti Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

Ujistěte se, že jste nainstalovali nejnovější modul Azure PowerShell. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Login-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Login-AzureRmAccount
```

## <a name="check-vm-sku-availability"></a>Kontrola dostupnosti skladových položek virtuálních počítačů
Dostupnost velikostí virtuálních počítačů (neboli skladových položek) se může lišit podle oblasti a zóny. Jako pomůcku při plánování použití zón dostupnosti můžete zobrazit seznam dostupných SKU virtuálních počítačů podle zóny a oblasti Azure. Díky tomu se zajistí, že vyberete odpovídající velikost virtuálního počítače a získáte požadovanou odolnost napříč zónami. Další informace o různých velikostech a typech virtuálních počítačů najdete v [přehledu velikostí virtuálních počítačů](sizes.md).

Dostupné skladové položky virtuálních počítačů můžete zobrazit pomocí příkazu [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku). Následující příklad zobrazí seznam dostupných skladových položek virtuálních počítačů v oblasti *eastus2*:

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

Výstup se podobá následujícímu zhuštěnému příkladu, který ukazuje zóny dostupnosti, ve kterých jsou dostupné jednotlivé velikosti virtuálních počítačů:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus2*. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Vytvoření síťových prostředků

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Vytvoření virtuální sítě, podsítě a veřejné IP adresy 
Tyto prostředky slouží k zajištění síťového připojení virtuálnímu počítači a k jeho připojení k internetu. Vytvořte IP adresu v zóně dostupnosti, v tomto příkladu *2*. V pozdějším kroku vytvoříte virtuální počítač ve stejné zóně, kterou jste použili k vytvoření IP adresy.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla skupiny zabezpečení sítě 
Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V tomto případě se vytvoří příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše. Chceme také vytvořit příchozí pravidlo pro port 80, které umožní příchozí webový provoz.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Vytvoření síťové karty pro virtuální počítač 
Vytvořte pro virtuální počítač síťovou kartu pomocí příkazu [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Síťová karta připojuje virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení, která se používají při nasazení virtuálního počítače, jako je image virtuálního počítače, jeho velikost a konfigurace ověřování. Velikost *Standard_DS1_v2* v tomto příkladu je podporovaná v zónách dostupnosti. Tato konfigurace taky určuje zónu dostupnosti, kterou jste nastavili při vytváření IP adresy. Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Potvrzení zóny pro spravovaný disk

Vytvořili jste prostředek IP adresy virtuálního počítače ve stejné zóně dostupnosti jako virtuální počítač. Prostředek spravovaného disku pro virtuální počítač se vytvoří ve stejné zóně dostupnosti. To lze ověřit pomocí rutiny [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk):

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

Výstup ukazuje, že spravovaný disk je ve stejné zóně dostupnosti jako virtuální počítač:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit virtuální počítač v zóně dostupnosti. Přečtěte si další informace o [oblastech a dostupnosti](regions-and-availability.md) pro virtuální počítače Azure.