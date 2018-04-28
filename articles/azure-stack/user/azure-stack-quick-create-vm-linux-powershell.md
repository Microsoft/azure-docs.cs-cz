---
title: Vytvořit virtuální počítač s Linuxem pomocí prostředí PowerShell v zásobníku Azure | Microsoft Docs
description: Vytvořte virtuální počítač s Linuxem pomocí prostředí PowerShell v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 86597defad7c76d41065270030a4c77ee901b014
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Rychlý úvod: Vytvořte virtuální počítač s Linuxem serveru pomocí prostředí PowerShell v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítače s Ubuntu Server 16.04 LTS můžete vytvořit pomocí Azure PowerShell zásobníku. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače.  Tento článek také poskytuje postup:

* Připojte k virtuálnímu počítači pomocí vzdáleného klienta.
* Vyčistěte nepoužité zdroje.

## <a name="prerequisites"></a>Požadavky

* **Bitovou kopii systému Linux v zásobníku Azure marketplace**

   Zásobník Azure marketplace neobsahuje bitovou kopii systému Linux ve výchozím nastavení. Získat operátor zásobník Azure zajistit **Ubuntu Server 16.04 LTS** bitové kopie je nutné. Operátor můžete použít postup popsaný v tématu [stáhnout položky marketplace z Azure do Azure zásobníku](../azure-stack-download-azure-marketplace-item.md) článku.

* Azure zásobníku vyžaduje konkrétní verzi prostředí Azure PowerShell k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro zásobník Azure PowerShell, přihlaste se k [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo externí klienta se systémem Windows Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) a postup [ Nainstalujte](azure-stack-powershell-install.md) a [konfigurace](azure-stack-powershell-configure-user.md) prostředí PowerShell.

* Veřejný klíč SSH s id_rsa.pub název uložené v adresáři .ssh profil uživatele systému Windows. Podrobné informace o vytváření klíčů SSH naleznete v tématu [vytváření SSH klíčů v systému Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, kde můžete nasadit a spravovat prostředky Azure zásobníku. Z vaší development kit nebo zásobník Azure integrovaný systém spusťte následující blok kódu a vytvořte skupinu prostředků. Hodnoty se přiřazují pro všechny proměnné v tomto dokumentu, můžete použít tyto hodnoty, nebo přiřadit nové hodnoty.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Vytvořit prostředky úložiště

Vytvoření účtu úložiště a pak vytvořte kontejner úložiště pro bitovou kopii Ubuntu Server 16.04 LTS.

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

Skupina zabezpečení sítě zabezpečí virtuálního počítače pomocí příchozí a odchozí pravidla. Vytvořte příchozí pravidlo pro port 3389, umožňuje příchozí připojení ke vzdálené ploše a příchozí pravidlo pro port 80 až povolit příchozí webový provoz.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
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

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení používané při nasazení virtuálního počítače. Příklad: přihlašovací údaje uživatele, velikosti a image virtuálního počítače.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
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

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Po nasazení virtuálního počítače, nakonfigurujte připojení SSH pro virtuální počítač. Použijte příkaz [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1), který vrátí veřejnou IP adresu virtuálního počítače.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Z klientského systému pomocí protokolu SSH nainstalována použijte následující příkaz pro připojení k virtuálnímu počítači. Pokud pracujete v systému Windows, můžete použít [Putty](http://www.putty.org/) k vytvoření připojení.

```
ssh <Public IP Address>
```

Po zobrazení výzvy zadejte azureuser jako přihlášení uživatele. Pokud jste použili heslo při vytvoření klíče SSH, budete muset zadat heslo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) příkaz k odebrání těchto prostředků. Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili základní virtuální počítač server Linux. Další informace o virtuálních počítačích Azure zásobníku, přejděte na [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
