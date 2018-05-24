---
title: Rychlý start – Vytvoření virtuálního počítače s Linuxem pomocí Azure PowerShellu | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí Azure PowerShellu vytvořit virtuální počítač s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření virtuálního počítače s Linuxem v Azure pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí modulu Azure PowerShell nasadit do Azure virtuální počítač s Linuxem Ubuntu. Pak se k virtuálnímu počítači připojíte přes SSH a nainstalujete na něj webový server NGINX, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

Veřejný klíč SSH s názvem *id_rsa.pub* musí být nakonec uložen v adresáři *.ssh* vašeho profilu uživatele Windows. Podrobné informace o vytváření a používání klíčů SSH najdete v tématu popisujícím [vytvoření klíčů SSH pro Azure](ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Vytvoření prostředků virtuální sítě

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky zajišťují síťového připojení k virtuálnímu počítači a jeho připojení k internetu:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Vytvořte pravidlo skupiny zabezpečení sítě Azure a provozu. Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V následujícím příkladu se vytvoří příchozí pravidlo pro port TCP 22, který umožňuje připojení SSH. Za účelem povolení příchozí webových přenosů se také vytvoří příchozí pravidlo pro TCP port 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Vytvořte virtuální síťovou kartu (NIC) pomocí příkazu [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Virtuální síťová karta připojí virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Konfigurace virtuálního počítače zahrnuje nastavení, která se používají při jeho nasazení, jako je image virtuálního počítače, velikost a možnosti ověřování. Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače následujícím způsobem:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Dále použijte předchozí definice konfigurací k vytvoření virtuálního počítače pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Po dokončení nasazení se přihlaste k virtuálnímu počítači s využitím SSH. K zajištění fungování virtuálního počítače se nainstaluje webový server NGINX.

Veřejnou IP adresu virtuálního počítače zobrazíte pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Pomocí klienta SSH se připojte k virtuálnímu počítači. Můžete použít Azure Cloud Shell z webového prohlížeče. Pokud používáte Windows, můžete použít [Putty](ssh-from-windows.md) nebo [Windows Subsystem pro Linux](/windows/wsl/install-win10). Zadejte veřejnou IP adresu virtuálního počítače:

```bash
ssh azureuser@IpAddress
```

Po zobrazení výzvy zadejte uživatelské jméno *azureuser*. Pokud u klíčů SSH používáte heslo, musíte ho zadat po zobrazení výzvy.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server NGINX. Spuštěním následujících příkazů z vaší relace SSH aktualizujte zdroje balíčku a nainstalujte nejnovější balíček NGINX:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Potom ukončete relaci SSH pomocí `exit`.

## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

Po instalaci serveru NGINX a otevření portu 80 na virtuálním počítači pro přístup z internetu přejděte pomocí libovolného webového prohlížeče na výchozí úvodní stránku serveru NGINX. Použijte veřejnou IP adresu virtuálního počítače, kterou jste získali v předchozím kroku. Následující příklad ukazuje výchozí web NGINX:

![Výchozí web NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, vytvořili jste skupinu zabezpečení sítě a pravidlo a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)
