---
title: Rychlý start – Vytvoření virtuálního počítače s Linuxem pomocí Azure PowerShellu | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí Azure PowerShellu vytvořit virtuální počítač s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: df6f99bfe9f1ae7b79f0f382fdee4fe4f1578bad
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407533"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Rychlý start: Vytvoření virtuálního počítače s Linuxem v Azure pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí modulu Azure PowerShell nasadit do Azure virtuální počítač s Linuxem. V tomto rychlém startu se používá image Ubuntu 16.04 LTS z marketplace od společnosti Canonical. Také se k virtuálnímu počítači připojíte přes SSH a nainstalujete na něj webový server NGINX, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud chcete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

Otevřete prostředí Bash a pomocí nástroje [ssh-keygen](https://www.ssh.com/ssh/keygen/) vytvořte pár klíčů SSH. Pokud na místním počítači nemáte prostředí Bash, můžete použít [Azure Cloud Shell](https://shell.azure.com/bash).  

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Podrobnější informace o vytváření párů klíčů SSH, včetně použití PuTTY, najdete v tématu [Jak používat klíče SSH s Windows](ssh-from-windows.md).

Pokud pár klíčů SSH vytvoříte pomocí služby Cloud Shell, uloží se do image kontejneru v [účtu úložiště, který automaticky vytvoří Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Tento účet úložiště ani sdílenou složku v něm neodstraňujte, dokud nenačtete své klíče, jinak ztratíte přístup k virtuálnímu počítači. 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Vytvoření prostředků virtuální sítě

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky zajišťují síťového připojení k virtuálnímu počítači a jeho připojení k internetu:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Vytvořte pravidlo skupiny zabezpečení sítě Azure a provozu. Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V následujícím příkladu se vytvoří příchozí pravidlo pro port TCP 22, který umožňuje připojení SSH. Za účelem povolení příchozí webových přenosů se také vytvoří příchozí pravidlo pro TCP port 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Vytvořte virtuální síťovou kartu (NIC) pomocí příkazu [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Virtuální síťová karta připojí virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač v PowerShellu, vytvoříte konfiguraci obsahující nastavení, jako jsou image, která se má použít, velikost a možnosti ověřování. Tato konfigurace se pak použije k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložený v souboru `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest" | `
Add-AzureRmVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzureRmVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Dále použijte předchozí definice konfigurací k vytvoření virtuálního počítače pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Nasazení virtuálního počítače bude několik minut trvat. Po dokončení nasazení se přesuňte k další části.

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Vytvořte k virtuálnímu počítači připojení SSH s použitím jeho veřejné IP adresy. Veřejnou IP adresu virtuálního počítače zobrazíte pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Do stejného prostředí Bash, které jste použili k vytvoření páru klíčů SSH (například [Azure Cloud Shell](https://shell.azure.com/bash) nebo místní prostředí Bash), vložte příkaz pro připojení přes SSH a vytvořte relaci SSH.

```bash
ssh azureuser@10.111.12.123
```

Po zobrazení výzvy zadejte uživatelské jméno *azureuser*. Pokud u klíčů SSH používáte heslo, musíte ho zadat po zobrazení výzvy.


## <a name="install-nginx"></a>Instalace serveru NGINX

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server NGINX. V relaci SSH aktualizujte zdroje balíčku a pak nainstalujte nejnovější balíček NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Až budete hotovi, zadáním příkazu `exit` ukončete relaci SSH.


## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

V libovolném webovém prohlížeči zobrazte výchozí úvodní stránku serveru NGINX. Jako webovou adresu zadejte veřejnou IP adresu virtuálního počítače. Veřejnou IP adresu najdete na stránce přehledu virtuálního počítače nebo v připojovacím řetězci SSH, který jste použili dříve.

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
