---
title: 'Úvodní příručka: Vytvoření brány NAT – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Tento úvodní příručka ukazuje, jak vytvořit bránu NAT pomocí Azure PowerShellu
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202217"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Úvodní příručka: Vytvoření brány NAT pomocí Azure PowerShellu

Tento rychlý start ukazuje, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítač v Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo spustit příkazy místně.  Pokud jste Azure Cloud Shell nepoužili, [přihlaste se teď](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2:**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Možnosti veřejné IP adresy pro bránu NAT jsou:

* **Veřejné IP adresy**
* **Předpony veřejné IP adresy**

Obojí lze použít s bránou NAT.

Do tohoto scénáře přidáme veřejnou IP adresu a veřejnou předponu IP, abychom to předvedli.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k Internetu potřebujete jednu nebo více veřejných IP adres pro bránu NAT. Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**. Výsledek tohoto příkazu bude uložen v proměnné **$publicIP** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

Pomocí [funkce New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) vytvořte veřejný prostředek předpony IP s názvem **myPublicIPprefix** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIPPrefix** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

V této části je podrobně uvedeno, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a veřejná předpona IP pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minut na 10 minut.

Vytvořte globální bránu Azure NAT s [novou AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Výsledek tohoto příkazu vytvoří prostředek brány s názvem **myNATgateway,** který používá veřejnou IP adresu **myPublicIP** a veřejnou IP předponu **myPublicIPprefix**. Časový limit nečinnosti je nastaven na 10 minut.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$natGateway** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

V tomto okamžiku je brána NAT funkční a jediné, co chybí, je konfigurace podsítí virtuální sítě, které by ji měly používat.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Vytvořte virtuální síť a přidružte podsíť k bráně.

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** pomocí [nástroje New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **myResourceGroup** pomocí [nové azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.  Výsledek příkazů bude uložen v proměnných s názvem **$subnet** a **$vnet** pro pozdější použití.

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

Veškerý odchozí provoz do internetových cílů nyní používá službu NAT.  Není nutné konfigurovat UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Vytvoření virtuálního virtuálního virtuálního provozu pro použití služby NAT

Teď vytvoříme virtuální hod pro použití služby NAT.  Tento virtuální virtuální soud má veřejnou IP adresu, kterou se dá použít jako veřejná IP adresa na úrovni instance, která vám umožní přístup k virtuálnímu virtuálnímu jevu.  Služba NAT je upozorněna na směr toku a nahradí výchozí cíl Internetu v podsíti. Veřejná IP adresa virtuálního počítačů se nepoužije pro odchozí připojení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu virtuálnímu mněmu.  Pomocí [funkce New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$publicIpVM** pro pozdější použití.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Vytvoření nsg a vystavit koncový bod SSH pro virtuální ms

Standardní veřejné IP adresy jsou 'bezpečné ve výchozím nastavení', musíme vytvořit NSG povolit příchozí přístup pro ssh. Pomocí [skupiny New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) vytvořte prostředek skupiny zabezpečení sítě s názvem **myNSG**. Pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) vytvořte pravidlo skupiny zabezpečení sítě pro přístup SSH s názvem **ssh** v **aplikaci myResourceGroupNAT**.  Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nsg** pro pozdější použití.

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

### <a name="create-nic-for-vm"></a>Vytvoření nic onici pro virtuální hod

Vytvořte síťové rozhraní s [rozhraním New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) s názvem **myNic**. Tento příkaz přidruží veřejnou IP adresu a skupinu zabezpečení sítě. Výsledek tohoto příkazu bude uložen v proměnné s názvem **$nic** pro pozdější použití.

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

Pomocí ssh-keygen vytvořte dvojici klíčů SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Podrobnější informace o vytváření párů klíčů SSH, včetně použití PuTTY, najdete v tématu [Jak používat klíče SSH s Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Pokud vytvoříte dvojici klíčů SSH pomocí prostředí Cloud Shell, pár klíčů je uložen v image kontejneru. Tento [účet úložiště se vytvoří automaticky](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Neodstraňujte účet úložiště ani sdílenou složku uvnitř, dokud nenačtete klíče.

#### <a name="create-vm-configuration"></a>Vytvořit konfiguraci virtuálního počítače

Chcete-li vytvořit virtuální ho v Prostředí PowerShell, vytvořte konfiguraci, která má nastavení pro image, která má být používána, velikost a možnosti ověřování. Konfigurace se používá k sestavení virtuálního počítače.

Definujte přihlašovací údaje SSH, informace o operačním systému a velikost virtuálního počítače. V tomto příkladu je klíč SSH uložen v ~/.ssh/id_rsa.pub.

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
Zkombinujte definice konfigurace a vytvořte virtuální hod s názvem **myVM** s [new-azvm](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) v **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Počkejte na virtuální počítač připravit na nasazení pak pokračovat se zbytkem kroků.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního soudu

Nejprve musíme zjistit IP adresu virtuálního počítačů, které jste vytvořili. Chcete-li získat veřejnou IP adresu virtuálního počítačů, použijte [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu okraji.

### <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu virtuálnímu virtuálnímu montovně

Pověření SSH by měla být uložena ve vašem prostředí Cloud z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače.

```bash
ssh azureuser@<ip-address-destination>
```

Nyní jste připraveni používat službu NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete použít [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) k odebrání skupiny prostředků a všech prostředků obsažených v rámci.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač, abyste ji používali. 

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT je řešeno přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.


- Další informace o [virtuální síti Azure NAT](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]


