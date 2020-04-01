---
title: 'Kurz: Vytvoření a testování brány NAT – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Tento kurz ukazuje, jak vytvořit bránu NAT pomocí Azure PowerShellu a otestovat službu NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202218"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí Azure PowerShellu a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítače v Azure. Chcete-li otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete vytvořením odchozích připojení k veřejné IP adrese. Tato připojení budou pocházet ze zdroje do cílového virtuálního počítače. Tento kurz nasazuje zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků pro jednoduchost.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo spustit příslušné příkazy místně.  Pokud jste službu Azure Cloud Shell nikdy nepoužívali, měli byste [se přihlásit.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2:**


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k Internetu potřebujete jednu nebo více veřejných IP adres pro bránu NAT. Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsource** v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPsource** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

 Pomocí [funkce New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) vytvořte veřejný prostředek předpony IP s názvem **myPublicIPprefixsource** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPPrefixsource** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

V této části je podrobně uvedeno, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a veřejná předpona IP pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minut na 10 minut.

Vytvořte globální bránu Azure NAT s [novou AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Výsledek tohoto příkazu vytvoří prostředek brány s názvem **myNATgateway,** který používá veřejnou IP adresu **myPublicIPsource** a veřejnou IP předponu **myPublicIPprefixsource**. Časový limit nečinnosti je nastaven na 10 minut.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$natGateway** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

V tomto okamžiku je brána NAT funkční a jediné, co chybí, je konfigurace podsítí virtuální sítě, které by ji měly používat.

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje pro odchozí provoz

Provedeme vás nastavením úplného testovacího prostředí. K ověření brány NAT nastavíte test pomocí nástrojů s otevřeným zdrojovým kódem. Začneme se zdrojem, který bude používat bránu NAT, kterou jsme vytvořili dříve.

### <a name="configure-virtual-network-for-source"></a>Konfigurace virtuální sítě pro zdroj

Vytvořte virtuální síť a přidružte podsíť k bráně.

Vytvořte virtuální síť s názvem **myVnetsource** s podsítí s názvem **mySubnetsource** pomocí [nástroje New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **souboru myResourceGroupNAT** pomocí [programu New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$subnetsource** a **$vnetsource** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Veškerý odchozí provoz do internetových cílů nyní používá službu NAT.  Není nutné konfigurovat UDR.

Než budeme moci otestovat bránu NAT, musíme vytvořit zdrojový virtuální počítač.  Přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance pro přístup k tomuto virtuálnímu virtuálnímu jevu zvenčí. Tato adresa se používá pouze pro přístup k ní pro test.  Ukážeme, jak má služba NAT přednost před ostatními odchozími možnostmi.

Tento virtuální virtuální ms můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální virtuální virtuální< systém, který se použije jako jumpbox bez veřejné IP adresy jako cvičení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu virtuálnímu mněmu.  Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpsourceVM** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Vytvoření nsg a vystavit koncový bod SSH pro virtuální ms

Vzhledem k tomu, že standardní veřejné IP adresy jsou ve výchozím nastavení zabezpečené, vytvoříme soubor NSG, který povolí příchozí přístup pro ssh. Služba NAT je informována o směru toku. Tento soubor nsg nebude použit pro odchozí po nakonfigurovánou bránu NAT ve stejné podsíti. Pomocí [skupiny New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek skupiny zabezpečení sítě s názvem **myNSGsource**. Pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo skupiny zabezpečení sítě pro přístup SSH s názvem **ssh** v **aplikaci myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nsgsource** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Vytvoření nic pro zdrojový virtuální virtuální ms

Vytvořte síťové rozhraní s [new-aznetworkinterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNicsource**. Tento příkaz přidružíte veřejnou IP adresu a skupinu zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nicsource** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Vytvoření zdrojového virtuálního virtuálního montova

#### <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

Pomocí ssh-keygen vytvořte dvojici klíčů SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Podrobnější informace o vytváření párů klíčů SSH, včetně použití PuTTY, najdete v tématu [Jak používat klíče SSH s Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Pokud vytvoříte dvojici klíčů SSH pomocí prostředí Cloud Shell, pár klíčů je uložen v image kontejneru. Tento [účet úložiště se vytvoří automaticky](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Neodstraňujte účet úložiště ani sdílenou složku uvnitř, dokud nenačtete klíče.

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Pokud chcete vytvořit virtuální počítač v PowerShellu, vytvoříte konfiguraci obsahující nastavení, jako jsou image, která se má použít, velikost a možnosti ověřování. Tato konfigurace se pak použije k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložen v ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
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
Zkombinujte definice konfigurace a vytvořte virtuální hospo- s názvem **myVMsource** s [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Zatímco příkaz se vrátí okamžitě, může trvat několik minut pro virtuální ho nasazení.

## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí provoz přeložený službou NAT, abyste jej mohli otestovat.

### <a name="configure-virtual-network-for-destination"></a>Konfigurace virtuální sítě pro cíl

Musíme vytvořit virtuální síť, kde bude cílový virtuální počítač.  Tyto příkazy jsou stejné kroky jako pro zdrojový virtuální hod. Byly přidány malé změny, které zveřejňují cílový koncový bod.

Vytvořte virtuální síť s názvem **myVnetdestination** s podsítí s názvem **mySubnetdestination** pomocí [nástroje New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **souboru myResourceGroupNAT** pomocí [programu New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$subnetdestination** a **$vnetdestination** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Vytvoření veřejné IP adresy pro cílový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup k cílovému virtuálnímu virtuálnímu počítačům.  Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPdestinationVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpdestinationVM** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Vytvoření nsg a vystavit Koncový bod SSH a HTTP pro virtuální ms

Standardní veřejné IP adresy jsou 'bezpečné ve výchozím nastavení', vytvoříme NSG, aby příchozí přístup pro ssh. Pomocí [skupiny New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek skupiny zabezpečení sítě s názvem **myNSGdestination**. Pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo skupiny zabezpečení sítě pro přístup SSH s názvem **ssh**.  Pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo skupiny zabezpečení sítě pro přístup http s názvem **http**. Obě pravidla budou vytvořena v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nsgdestination** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
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
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Vytvořit nic pro cílový virtuální ms

Vytvořte síťové rozhraní s [rozhraním New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNicdestination**. Tento příkaz bude přidružit k veřejné IP adrese a skupině zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nicdestination** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Vytvoření cílového virtuálního virtuálního montova

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Pokud chcete vytvořit virtuální počítač v PowerShellu, vytvoříte konfiguraci obsahující nastavení, jako jsou image, která se má použít, velikost a možnosti ověřování. Tato konfigurace se pak použije k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložen v ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
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
Zkombinujte definice konfigurace a vytvořte virtuální hospo s názvem **myVMdestination** s [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Zatímco příkaz se vrátí okamžitě, může trvat několik minut pro virtuální ho nasazení.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a testování datové části na cílovém virtuálním počítači

Nejprve musíme zjistit IP adresu cílového virtuálního počítačů.  Chcete-li získat veřejnou IP adresu virtuálního počítačů, použijte [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu virtuálnímu mísu

Pověření SSH by měla být uložena ve vašem prostředí Cloud z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače. 

```bash
ssh azureuser@<ip-address-destination>
```

Po přihlášení zkopírujte a vložte následující příkazy.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Tyto příkazy aktualizují váš virtuální počítač, nainstalují nginx a vytvoří soubor 100 KBytes. Tento soubor bude načten ze zdrojového virtuálního virtuálního aplikace pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním virtuálním mem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejprve musíme zjistit IP adresu zdrojového virtuálního počítačů.  Chcete-li získat veřejnou IP adresu virtuálního počítačů, použijte [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o zdrojový virtuální počítač.

### <a name="log-into-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu virtuálnímu montovně

Pověření SSH jsou opět uloženy v prostředí Cloud Shell. Otevřete novou kartu pro [Azure Cloud Shell](https://shell.azure.com) ve vašem prohlížeči.  Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače. 

```bash
ssh azureuser@<ip-address-source>
```

Zkopírujte a vložte následující příkazy a připravte se na testování služby NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Tyto příkazy aktualizují váš virtuální počítač, nainstalují go, [nainstalují hej](https://github.com/rakyll/hey) z GitHubu a aktualizují prostředí prostředí prostředí.

Nyní jste připraveni k testování služby NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Při přihlášení ke zdrojovému virtuálnímu virtuálnímu počítačůmu můžete ke generování požadavků na cílovou IP adresu použít **curl** a **hey.**

Pomocí zvlnění načtěte soubor 100 KBytes.  Nahraďte ** \<cílovou>ip-adres** v níže uvedeném příkladu cílovou adresou IP, kterou jste dříve zkopírovali.  Parametr **--output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také generovat řadu požadavků pomocí **hey**. Opět nahraďte ** \<cílovou>ip-address>** cílovou ADRESOu IP, kterou jste dříve zkopírovali.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tento příkaz vygeneruje 100 požadavků, 10 současně, s časovým časem 30 sekund. Připojení TCP nebude znovu použito.  Každý požadavek načte 100 kbajtů.  Na konci běhu, **bude hej** zprávu některé statistiky o tom, jak dobře na vychýlivá služba NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete použít [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) k odebrání skupiny prostředků a všech prostředků obsažených v rámci.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a pak otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT lze snadno vyřešit přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.

- Další informace o [překladu virtuálních sítí](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

