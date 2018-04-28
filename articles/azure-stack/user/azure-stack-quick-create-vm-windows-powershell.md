---
title: Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell v zásobníku Azure | Microsoft Docs
description: Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 7CA6C0AC-23B7-4007-BA32-7A950FD1F3B8
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/20/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 9f5752a969ff6a191ec60e175494316aea4abcaf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Rychlý úvod: vytvoření virtuálního počítače Windows serveru pomocí prostředí PowerShell v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítač systému Windows Server 2016 můžete vytvořit pomocí Azure PowerShell zásobníku. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače. Tento článek také poskytuje postup:

* Připojte k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalovat webový server IIS a zobrazit výchozí domovskou stránku.
* Vyčistěte vašich prostředků.

>[!NOTE]
 Můžete spustit kroků popsaných v tomto článku z Development Kit zásobník Azure nebo z externí klienta se systémem Windows, pokud se připojíte přes síť VPN.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že vaše operátor zásobník Azure přidal bitovou kopii "Windows Server 2016" do zásobníku Azure marketplace.

* Azure zásobníku vyžaduje konkrétní verzi prostředí Azure PowerShell k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro zásobník Azure PowerShell, postupujte podle kroků pro [nainstalovat](azure-stack-powershell-install.md) a [konfigurace](azure-stack-powershell-configure-user.md) prostředí PowerShell.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, do které zásobník Azure jsou nasadit a spravovat prostředky. Z vaší development kit nebo zásobník Azure integrovaný systém spusťte následující blok kódu a vytvořte skupinu prostředků. Hodnoty se přiřazují pro všechny proměnné v tomto dokumentu, můžete použít tyto hodnoty, nebo přiřadit nové hodnoty.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Vytvořit prostředky úložiště

Vytvořte účet úložiště a kontejner úložiště pro uložení bitové kopie systému Windows Server 2016.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Vytvoření síťových prostředků

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky se používají k zajištění síťové připojení k virtuálnímu počítači.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla skupiny zabezpečení sítě

Skupina zabezpečení sítě zabezpečí virtuálního počítače pomocí příchozí a odchozí pravidla. Umožňuje vytvořit pravidlo pro příchozí pro port 3389, umožňuje příchozí připojení ke vzdálené ploše a příchozí pravidlo pro port 80 až povolit příchozí webový provoz.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Vytvoření síťové karty pro virtuální počítač

Síťová karta připojuje virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení používané při nasazení virtuálního počítače. Příklad: přihlašovací údaje, velikost a image virtuálního počítače.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Na vzdálený do virtuálního počítače, který jste vytvořili v předchozím kroku musíte její veřejnou IP adresu. Spusťte následující příkaz, který získat veřejnou IP adresu virtuálního počítače:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Použijte následující příkaz pro vytvoření relace vzdálené plochy s virtuálním počítačem. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste použili při vytvoření virtuálního počítače.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalace služby IIS pomocí PowerShellu

Když jste přihlášeni k virtuálnímu počítači Azure, můžete k instalaci služby IIS a k aktivaci pravidla místní brány firewall pro povolení webového provozu použít jeden řádek PowerShellu. Otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

S nainstalovanou službou IIS a s portem 80 otevřete na vašem virtuálním počítači můžete zobrazit výchozí úvodní stránka IIS webový prohlížeč podle svého výběru. Použití *publicIpAddress* popsané v předchozí části přejděte výchozí stránky.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Odstranění virtuálního počítače

Pokud již nepotřebujete, chcete-li odebrat skupinu prostředků, která obsahuje virtuální počítač a jeho souvisejících prostředků použijte následující příkaz:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili jednoduchého virtuálního počítače Windows. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
