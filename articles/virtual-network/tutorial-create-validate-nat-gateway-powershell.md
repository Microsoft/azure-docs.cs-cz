---
title: Vytvoření a otestování brány NAT – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: V tomto kurzu se dozvíte, jak vytvořit bránu NAT pomocí Azure PowerShell a testovat službu NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: d9f3fa67a0d3eee303ed307f1d64d30955348869
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222475"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí Azure PowerShell a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která poskytuje odchozí připojení k virtuálním počítačům v Azure. Pokud chcete otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete tak, že vytvoříte odchozí připojení k veřejné IP adrese. Tato připojení budou pocházet ze zdroje do cílového virtuálního počítače. Tento kurz nasadí zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků pro zjednodušení.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo místně spustit příslušné příkazy.  Pokud jste Azure Cloud Shell nikdy nepoužili, měli byste se [Přihlásit nyní](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsource** v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPsource** pro pozdější použití.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

 Pomocí [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix?view=latest) vytvořte prostředek předpony veřejné IP adresy s názvem **myPublicIPprefixsource** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPPrefixsource** pro pozdější použití.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

Vytvořte globální bránu Azure NAT pomocí [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Výsledkem tohoto příkazu je vytvoření prostředku brány s názvem **myNATgateway** , který používá veřejnou IP adresu **myPublicIPsource** a předponu veřejné IP adresy **myPublicIPprefixsource**. Časový limit nečinnosti je nastavený na 10 minut.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$natGateway** pro pozdější použití.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

V tomto okamžiku je brána NAT funkční a všechny, které chybí, je konfigurace, které podsítě virtuální sítě by měly používat.

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje na odchozí provoz

Provedeme vás instalací kompletního testovacího prostředí. Pomocí Open source nástrojů nastavíte test a ověříte bránu NAT. Začneme se zdrojem, který bude používat bránu NAT, kterou jsme vytvořili dříve.

### <a name="configure-virtual-network-for-source"></a>Konfigurace virtuální sítě pro zdroj

Vytvořte virtuální síť a přidružte podsíť k bráně.

Vytvořte virtuální síť s názvem **myVnetsource** s podsítí s názvem **mySubnetsource** pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **myResourceGroupNAT** pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$subnetsource** a **$vnetsource** pro pozdější použití.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

Veškerý odchozí provoz do internetových cílů teď používá službu překladu adres (NAT).  Není nutné konfigurovat UDR.

Než budeme moct otestovat bránu NAT, musíme vytvořit zdrojový virtuální počítač.  Pro přístup k tomuto virtuálnímu počítači z vnějšku přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance. Tato adresa se používá jenom pro přístup k tomuto testu.  Ukážeme, jak má služba NAT přednost před jinými odchozími možnostmi.

Tento virtuální počítač můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální počítač pro použití jako JumpBox bez veřejné IP adresy jako cvičení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu počítači.  Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpsourceVM** pro pozdější použití.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Vytvoření NSG a zpřístupnění koncového bodu SSH pro virtuální počítač

Vzhledem k tomu, že standardní veřejné IP adresy jsou ve výchozím nastavení zabezpečené, vytvoříme NSG, který umožní příchozí přístup pro SSH. Služba NAT má na vědomí směr toku. Tato NSG se nebude používat pro odchozí připojení, když je ve stejné podsíti nakonfigurovaná brána NAT. Pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek NSG s názvem **myNSGsource**. Pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo NSG pro přístup SSH s názvem **SSH** v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nsgsource** pro pozdější použití.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>Vytvořit síťovou kartu pro zdrojový virtuální počítač

Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNicsource**. Tento příkaz přiřadí veřejnou IP adresu a skupinu zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nicsource** pro pozdější použití.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Vytvoření zdrojového virtuálního počítače

#### <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

K vytvoření páru klíčů SSH použijte ssh-keygen.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Podrobnější informace o vytváření párů klíčů SSH, včetně použití PuTTY, najdete v tématu [Jak používat klíče SSH s Windows](../virtual-machines/linux/ssh-from-windows.md).

Pokud vytvoříte pár klíčů SSH pomocí Cloud Shell, dvojice klíčů je uložena v imagi kontejneru. Tento [účet úložiště se automaticky vytvoří](../cloud-shell/persisting-shell-storage.md). Neodstraňujte účet úložiště nebo sdílenou složku v rámci, dokud nezískáte klíče.

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Pokud chcete vytvořit virtuální počítač v PowerShellu, vytvoříte konfiguraci obsahující nastavení, jako jsou image, která se má použít, velikost a možnosti ověřování. Tato konfigurace se pak použije k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložený v ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Zkombinujte definice konfigurace a vytvořte virtuální počítač s názvem **myVMsource** s [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

I když se příkaz vrátí hned, může trvat několik minut, než se virtuální počítač nasadí.

## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí přenosy přeložené službou překladu adres (NAT), abyste je mohli otestovat.

### <a name="configure-virtual-network-for-destination"></a>Konfigurace virtuální sítě pro cíl

Musíme vytvořit virtuální síť, ve které bude cílový virtuální počítač.  Tyto příkazy jsou stejný postup jako u zdrojového virtuálního počítače. Byly přidány malé změny pro zpřístupnění cílového koncového bodu.

Vytvořte virtuální síť s názvem **myVnetdestination** s podsítí s názvem **mySubnetdestination** pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **myResourceGroupNAT** pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$subnetdestination** a **$vnetdestination** pro pozdější použití.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Vytvoření veřejné IP adresy pro cílový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup k cílovému virtuálnímu počítači.  Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPdestinationVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpdestinationVM** pro pozdější použití.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Vytvoření NSG a zpřístupnění koncového bodu protokolu SSH a HTTP pro virtuální počítač

Standardní veřejné IP adresy jsou ve výchozím nastavení zabezpečené, vytvoříme NSG, který umožní příchozí přístup pro SSH. Pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek NSG s názvem **myNSGdestination**. Pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo NSG pro přístup SSH s názvem **SSH**.  Pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo NSG pro přístup HTTP s názvem **http**. Obě pravidla budou vytvořena v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nsgdestination** pro pozdější použití.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>Vytvořit síťovou kartu pro cílový virtuální počítač

Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNicdestination**. Tento příkaz se přidruží k veřejné IP adrese a skupině zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nicdestination** pro pozdější použití.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Vytvoření cílového virtuálního počítače

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Pokud chcete vytvořit virtuální počítač v PowerShellu, vytvoříte konfiguraci obsahující nastavení, jako jsou image, která se má použít, velikost a možnosti ověřování. Tato konfigurace se pak použije k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložený v ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Zkombinujte definice konfigurace a vytvořte virtuální počítač s názvem **myVMdestination** s [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

I když se příkaz vrátí hned, může trvat několik minut, než se virtuální počítač nasadí.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a datové části testu na cílovém virtuálním počítači

Nejdřív musíme zjistit IP adresu cílového virtuálního počítače.  K získání veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu počítači

Přihlašovací údaje SSH by měly být uložené v Cloud Shell z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Po přihlášení zkopírujte a vložte následující příkazy.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Tyto příkazy aktualizují váš virtuální počítač, nainstaluje Nginx a vytvoří soubor 100-kilobajtů. Tento soubor se načte ze zdrojového virtuálního počítače pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním počítačem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejdřív musíme zjistit IP adresu zdrojového virtuálního počítače.  K získání veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o zdrojový virtuální počítač.

### <a name="log-into-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu počítači

Přihlašovací údaje SSH se znovu ukládají v Cloud Shell. Otevře novou kartu pro [Azure Cloud Shell](https://shell.azure.com) v prohlížeči.  Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Zkopírujte a vložte následující příkazy, které se připraví na testování služby překladu adres (NAT).

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Tyto příkazy aktualizují váš virtuální počítač, nainstaluje se, nainstaluje [Hey](https://github.com/rakyll/hey) z GitHubu a aktualizují prostředí prostředí.

Teď jste připraveni otestovat službu NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Během přihlášení ke zdrojovému virtuálnímu počítači můžete k vygenerování požadavků na cílovou IP adresu použít **oblé** a **Hey** .

Pomocí oblé načtěte soubor 100-kilobajtů.  **\<ip-address-destination>** V následujícím příkladu nahraďte cílovou IP adresou, kterou jste zkopírovali dříve.  Parametr **--Output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

Můžete také vygenerovat sérii požadavků pomocí **Hey**. Znovu nahraďte **\<ip-address-destination>** cílovou IP adresou, kterou jste zkopírovali dříve.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Tento příkaz vygeneruje 100 požadavků, 10 současně s časovým limitem 30 sekund. Připojení TCP se nebude znovu používat.  Každý požadavek získá 100 kB.  Na konci běhu ohlásí funkce **Hey** informace o tom, jak dobře služba NAT provedla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a potom jste otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků na portech SNAT se dá snadno adresovat přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.

- Informace o [Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]