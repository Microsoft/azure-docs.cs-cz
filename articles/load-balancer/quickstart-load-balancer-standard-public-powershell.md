---
title: 'Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit nástroj pro vyrovnávání zatížení pomocí Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 9db530c1bdff6521c945ae0c2373bb9d32b8a476
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047773"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure PowerShell

Začněte s Azure Load Balancer pomocí Azure PowerShell k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* S názvem **myResourceGroupLB**.
* V umístění **eastus** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

## <a name="create-a-public-ip-address-in-the-standard-sku"></a>Vytvoření veřejné IP adresy v SKU Standard

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) k:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Pokud chcete vytvořit veřejnou IP adresu oblasti v zóně 1, použijte následující příkaz:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-frontend-ip"></a>Vytvoření front-endové IP adresy

Vytvořte front-end IP adresu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* S názvem **myFrontEnd**.
* Připojeno k veřejné IP **myPublicIP**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurovat fond back-endu adres

Vytvořte fond back-end adres pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* S názvem **myBackEndPool**.
* Virtuální počítače se připojují k tomuto fondu back-end ve zbývajících krocích.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [doplňku add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe**sondy stavu.
* Protokol **TCP**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```

### <a name="create-load-balancer-resource"></a>Vytvořit prostředek nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Pojmenovaný **myLoadBalancer**
* V **eastus**.
* Ve skupině prostředků **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-standard-sku"></a>Konfigurace virtuální sítě v SKU Standard

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* S názvem **myVNet**.
* Ve skupině prostředků **myResourceGroupLB**.
* Podsíť s názvem **myBackendSubnet**.
* Virtuální síť **10.0.0.0/16**.
* Podsíť **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80
Vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* S názvem **myNSGRuleHTTP**.
* Popis **Povolení protokolu HTTP**.
* Přístup k **Povolení**.
* Protokol **(*)**.
* Směr **příchozí**.
* Priorita **2000**.
* Zdroj **Internetu**.
* Rozsah zdrojových portů **(*)**.
* Předpona cílové adresy **(*)**.
* Cílový **Port 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* S názvem **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* S pravidly zabezpečení vytvořenými v předchozích krocích uložených v proměnné.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>Vytvoření síťových rozhraní

Vytvořte tři síťová rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VIRTUÁLNÍ POČÍTAČ 1

* S názvem **myNicVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VIRTUÁLNÍ POČÍTAČ 2

* S názvem **myNicVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VIRTUÁLNÍ POČÍTAČ 3

* S názvem **myNicVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell
$cred = Get-Credential
```

Vytvořte virtuální počítače pomocí:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* S názvem **myVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V **zóna 1**.
* V umístění **eastus** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* S názvem **myVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V **zóna 2**.
* V umístění **eastus** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* S názvem **myVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V **zóna 3**.
* V umístění **eastus** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí Zdrojová síťová adresa (SNAT) pro virtuální počítače ve fondu back-endu. 

Další informace o odchozích připojeních najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address"></a>Vytvoření odchozí veřejné IP adresy

Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) k:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIPOutbound**.
* V **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Pokud chcete vytvořit veřejnou IP adresu oblasti v zóně 1, použijte následující příkaz:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>Vytvoření odchozí konfigurace IP adresy front-endu

Vytvořte novou konfiguraci IP adresy front-endu pomocí [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig):

* S názvem **myFrontEndOutbound**.
* Přidruženo k veřejné IP adrese **myPublicIPOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'
$lbn = 'myLoadBalancer'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>Vytvořit odchozí fond

Vytvořte nový odchozí fond pomocí [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig). 

Použijte fond a IP adresu front-endu na nástroj pro vyrovnávání zatížení s [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* S názvem **myBackEndPoolOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>Vytvořit odchozí pravidlo a použít na nástroj pro vyrovnávání zatížení

Vytvořte nové odchozí pravidlo pro odchozí back-end fond pomocí [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig). 

Použijte pravidlo pro nástroj pro vyrovnávání zatížení s [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* S názvem **myOutboundRule**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* Přidruženo k front-endu **myFrontEndOutbound**.
* Protokol **vše**.
* Časový limit nečinnosti **15**.
* porty **10000** odchozí.
* Přidruženo k **myBackEndPoolOutbound**fondu back-endu.
* Ve skupině prostředků **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>Přidat virtuální počítače do odchozího fondu

Přidejte síťová rozhraní virtuálního počítače do odchozího fondu nástroje pro vyrovnávání zatížení pomocí [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig):


#### <a name="vm1"></a>VM1
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM1** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM2** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM3** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

## <a name="create-a-public-ip-address-in-the-basic-sku"></a>Vytvoření veřejné IP adresy v základní skladové jednotce

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) k:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-frontend-ip"></a>Vytvoření front-endové IP adresy

Vytvořte front-end IP adresu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* S názvem **myFrontEnd**.
* Připojeno k veřejné IP **myPublicIP**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurovat fond back-endu adres

Vytvořte fond back-end adres pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* S názvem **myBackEndPool**.
* Virtuální počítače se připojují k tomuto fondu back-end ve zbývajících krocích.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [doplňku add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe**sondy stavu.
* Protokol **TCP**.
* Povolte překlad odchozích adres zdrojové sítě (SNAT) pomocí IP adresy front-endu.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>Vytvořit prostředek nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Pojmenovaný **myLoadBalancer**
* V **eastus**.
* Ve skupině prostředků **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-basic-sku"></a>Konfigurace virtuální sítě v základní skladové jednotce

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* S názvem **myVNet**.
* Ve skupině prostředků **myResourceGroupLB**.
* Podsíť s názvem **myBackendSubnet**.
* Virtuální síť **10.0.0.0/16**.
* Podsíť **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80
Vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* S názvem **myNSGRuleHTTP**.
* Popis **Povolení protokolu HTTP**.
* Přístup k **Povolení**.
* Protokol **(*)**.
* Směr **příchozí**.
* Priorita **2000**.
* Zdroj **Internetu**.
* Rozsah zdrojových portů **(*)**.
* Předpona cílové adresy **(*)**.
* Cílový **Port 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* S názvem **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* S pravidly zabezpečení vytvořenými v předchozích krocích uložených v proměnné.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Vytvoření síťových rozhraní

Vytvořte tři síťová rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VIRTUÁLNÍ POČÍTAČ 1

* S názvem **myNicVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VIRTUÁLNÍ POČÍTAČ 2

* S názvem **myNicVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VIRTUÁLNÍ POČÍTAČ 3

* S názvem **myNicVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Vytvořit skupinu dostupnosti pro virtuální počítače

Pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) vytvořte skupinu dostupnosti pro virtuální počítače:

* S názvem **myAvSet**.
* Ve skupině prostředků **myResourceGroupLB**.
* V umístění **eastus** .

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell
$cred = Get-Credential
```

Vytvořte virtuální počítače pomocí:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* S názvem **myVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V umístění **eastus** .
* Ve skupině dostupnosti **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* S názvem **myVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V umístění **eastus** .
* Ve skupině dostupnosti **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* S názvem **myVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* Připojeno k **myLoadBalancer**nástroje pro vyrovnávání zatížení.
* V umístění **eastus** .
* Ve skupině dostupnosti **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Vytvoření a konfigurace tří virtuálních počítačů trvá několik minut.

---

## <a name="install-iis"></a>Instalace služby IIS

K instalaci rozšíření vlastních skriptů použijte [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest) . 

Rozšíření spustí PowerShell Add-WindowsFeature Web-Server pro instalaci webserveru IIS a pak aktualizuje stránku Default.htm, aby zobrazovala název hostitele virtuálního počítače:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm3"></a>VM3

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM3'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

K získání veřejné IP adresy nástroje pro vyrovnávání zatížení použijte [příkaz Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) :

```azurepowershell-interactive
  ## Variables for command. ##
  $rg = 'myResourceGroupLB'
  $ipn = 'myPublicIP'
    
  Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a zbývajících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu

* Vytvořili jste veřejný Nástroj pro vyrovnávání zatížení Standard nebo Basic.
* Připojené virtuální počítače. 
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu.
* Otestování nástroje pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte na..
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)


