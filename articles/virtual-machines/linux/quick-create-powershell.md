---
title: Rychlý start Azure – Vytvoření virtuálního počítače pomocí PowerShellu | Dokumentace Microsoftu
description: Rychle se naučíte, jak vytvářet virtuální počítače s Linuxem pomocí PowerShellu.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1d477bfe42cebbc35db4fd40aa5bc3de4829c49b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-linux-virtual-machine-with-powershell"></a>Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tento Rychlý start podrobně popisuje nasazení virtuálního počítače se serverem Ubuntu pomocí modulu Azure PowerShell. Po nasazení serveru se vytvoří připojení SSH a nainstaluje se webový server NGINX.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento Rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Veřejný klíč SSH s názvem *id_rsa.pub* musí být nakonec uložen v adresáři *.ssh* vašeho profilu uživatele Windows. Podrobné informace o vytváření a používání klíčů SSH najdete v tématu popisujícím [vytvoření klíčů SSH pro Azure](ssh-from-windows.md).


## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Login-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
```

## <a name="create-networking-resources"></a>Vytvoření síťových prostředků

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky slouží k zajištění síťového připojení virtuálnímu počítači a k jeho připojení k internetu.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Vytvořte skupinu zabezpečení sítě a pravidlo skupiny zabezpečení sítě. Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V tomto případě se vytvoří příchozí pravidlo pro port 22, které umožní příchozí připojení SSH. Chceme také vytvořit příchozí pravidlo pro port 80, které umožní příchozí webový provoz.

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
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Vytvořte pro virtuální počítač síťovou kartu pomocí příkazu [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Síťová karta připojuje virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení, která se používají při nasazení virtuálního počítače, jako je image virtuálního počítače, jeho velikost a konfigurace ověřování.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 16.04-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení vytvořte připojení SSH k virtuálnímu počítači.

Použijte příkaz [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), který vrátí veřejnou IP adresu virtuálního počítače.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Ze systému s nainstalovaným SSH se pomocí následujícího příkazu připojte k virtuálnímu počítači. Pokud pracujete v systému Windows, můžete k vytvoření připojení použít [PuTTy](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty). 

```bash 
ssh <Public IP Address>
```

Po zobrazení výzvy zadejte uživatelské jméno *azureuser*. Pokud jste při vytváření klíčů SSH zadali heslo, musíte zadat i to.


## <a name="install-nginx"></a>Instalace serveru NGINX

Pomocí následujících příkazů provedete aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

S nainstalovaným serverem NGINX na virtuálním počítači a nyní otevřeným portem 80 z internetu můžete použít libovolný webový prohlížeč a zobrazit výchozí úvodní stránku serveru NGINX. Nezapomeňte pro návštěvu výchozí stránky použít veřejnou IP adresu popsanou výše. 

![Výchozí web NGINX](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto Rychlém startu jste nasadili jednoduchý virtuální počítač a pravidlo skupiny zabezpečení sítě a nainstalovali jste webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)
