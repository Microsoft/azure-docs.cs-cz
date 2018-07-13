---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu – Azure PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí Powershellu.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696579"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí Powershellu

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manager, který Microsoft doporučuje pro většinu nových nasazení namísto modelu nasazení classic.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Spustit skript
Můžete stáhnout úplná skript prostředí PowerShell použít [tady](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Postupem uvedeným níže změníte skript fungoval ve vašem prostředí.

Změňte hodnoty proměnných níže podle hodnoty, které chcete použít pro vaše nasazení. Následující hodnoty mapování na scénář použitý v tomto článku:

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Vytvořit prostředky potřebné pro váš virtuální počítač
Před vytvořením virtuálního počítače, potřebujete skupinu prostředků, virtuální sítě, veřejná IP adresa a síťová karta virtuální počítač využívá.

1. Vytvořte novou skupinu prostředků.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Vytvoření virtuální sítě a podsítě.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Vytvořte prostředek veřejné IP adresy. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Vytvořte síťové rozhraní (NIC) pro virtuální počítač v podsíti vytvořili výše, s veřejnou IP adresu. Všimněte si, že první rutina načítání virtuální sítě z Azure, to je nezbytné, protože `Set-AzureRmVirtualNetwork` spustil změnit existující virtuální síť.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Vytvoření účtu úložiště pro hostování jednotky operačního systému virtuálního počítače.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Všechny potřebné prostředky jsou na místě, můžete vytvořit nový virtuální počítač.

1. Vytvořte objekt konfigurace virtuálního počítače.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Získání přihlašovacích údajů pro účet místního Správce virtuálních počítačů.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Vytvořte objekt konfigurace virtuálního počítače.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Nastavte image operačního systému virtuálního počítače.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Konfigurace disku s operačním systémem.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Přidání síťového rozhraní k virtuálnímu počítači.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Vytvoření virtuálního počítače.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Uložte soubor skriptu.

## <a name="run-the-script"></a>Spusťte skript

Po provedení požadované změny, předchozí skript spusťte. Po několika minutách se vytvoří virtuální počítač.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit. Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-powershell.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení.

## <a name="next-steps"></a>Další postup

Veškerý provoz sítě může téct do a z virtuálního počítače v rámci tohoto článku vytvořili. Můžete definovat příchozích a odchozích pravidel zabezpečení v rámci skupiny zabezpečení sítě neomezují provoz, který může téct do a z rozhraní sítě a podsítě. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupin zabezpečení sítě](security-overview.md).