---
title: Vytvoření brány NAT – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: V tomto rychlém startu se dozvíte, jak vytvořit bránu NAT pomocí Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 516f858c8f95ec3f7cfd8ffc1592358716986519
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223291"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Kurz: Vytvoření brány NAT pomocí Azure PowerShell

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo místně spustit příkazy.  Pokud jste nepoužili Azure Cloud Shell, [Přihlaste se hned](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=latest). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Možnosti veřejné IP adresy pro bránu NAT:

* **veřejné IP adresy**,
* **Předpony veřejných IP adres**

Obojí lze použít s bránou NAT.

Do tohoto scénáře přidáme veřejnou IP adresu a předponu veřejné IP adresy, abychom mohli předvést.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné **$publicIP** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

Pomocí [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix?view=latest) vytvořte prostředek předpony veřejné IP adresy s názvem **myPublicIPprefix** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPPrefix** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

Vytvořte globální bránu Azure NAT pomocí [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Výsledkem tohoto příkazu je vytvoření prostředku brány s názvem **myNATgateway** , který používá veřejnou IP adresu **myPublicIP** a předponu veřejné IP adresy **myPublicIPprefix**. Časový limit nečinnosti je nastavený na 10 minut.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$natGateway** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

V tomto okamžiku je brána NAT funkční a všechny, které chybí, je konfigurace, které podsítě virtuální sítě by měly používat.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Vytvořte virtuální síť a přidružte podsíť k bráně.

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **myResourceGroup** pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$Subnet** a **$VNet** pro pozdější použití.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Veškerý odchozí provoz do internetových cílů teď používá službu překladu adres (NAT).  Není nutné konfigurovat UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Vytvoření virtuálního počítače pro použití služby NAT

Nyní vytvoříme virtuální počítač pro použití služby NAT.  Tento virtuální počítač má veřejnou IP adresu, která se používá jako veřejná IP adresa na úrovni instance, která umožňuje přístup k virtuálnímu počítači.  Služba NAT má na vědomí směr toku a nahradí výchozí internetový cíl ve vaší podsíti. Veřejná IP adresa virtuálního počítače se nebude používat pro odchozí připojení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu počítači.  Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpVM** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Vytvoření NSG a zpřístupnění koncového bodu SSH pro virtuální počítač

Standardní veřejné IP adresy jsou zabezpečené ve výchozím nastavení, musíme vytvořit NSG a povolit tak příchozí přístup pro SSH. Pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek NSG s názvem **myNSG**. Pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo NSG pro přístup SSH s názvem **SSH** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$NSG** pro pozdější použití.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Vytvořit síťovou kartu pro virtuální počítač

Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNic**. Tento příkaz přidruží veřejnou IP adresu a skupinu zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nic** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Vytvoření virtuálního počítače

#### <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

K vytvoření páru klíčů SSH použijte ssh-keygen.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Podrobnější informace o vytváření párů klíčů SSH, včetně použití PuTTY, najdete v tématu [Jak používat klíče SSH s Windows](../virtual-machines/linux/ssh-from-windows.md).

Pokud vytvoříte pár klíčů SSH pomocí Cloud Shell, dvojice klíčů je uložena v imagi kontejneru. Tento [účet úložiště se automaticky vytvoří](../cloud-shell/persisting-shell-storage.md). Neodstraňujte účet úložiště nebo sdílenou složku v rámci, dokud nezískáte klíče.

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Pokud chcete vytvořit virtuální počítač v prostředí PowerShell, vytvořte konfiguraci s nastavením pro použitou image, velikost a možnosti ověřování. Konfigurace se používá k vytvoření virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložený v ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Zkombinujte definice konfigurace a vytvořte virtuální počítač s názvem **myVM** s [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Počkejte, až se virtuální počítač připraví na nasazení, a pak pokračujte zbývajícími kroky.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního počítače

Nejdřív musíme zjistit IP adresu vytvořeného virtuálního počítače. K získání veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

### <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu počítači

Přihlašovací údaje SSH by měly být uložené v Cloud Shell z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku.

```bash
ssh azureuser@<ip-address-destination>
```

Nyní jste připraveni použít službu překladu adres (NAT).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač pro jeho použití. 

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků SNAT se řeší přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.


- Přečtěte si o [službě Azure Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]