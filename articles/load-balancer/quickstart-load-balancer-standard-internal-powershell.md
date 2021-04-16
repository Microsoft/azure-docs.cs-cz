---
title: 'Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms.openlocfilehash: 618ca8722cef1959fddc5dcd24e8e8bdbb1f620a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530042"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure PowerShell

Začněte s Azure Load Balancer pomocí Azure PowerShell k vytvoření interního nástroje pro vyrovnávání zatížení a dvou virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateIntLBQS-rg' -Location 'eastus'
```
---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. 

Následující diagram znázorňuje prostředky vytvořené v rámci tohoto rychlého startu:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Prostředky služby Load Balancer úrovně Standard vytvořené pro rychlý Start." border="false":::

## <a name="configure-virtual-network---standard"></a>Konfigurace virtuální sítě – standardní

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

Vytvořte virtuální síť pro virtuální počítače back-endu.

Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k virtuální síti.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Vytvoření virtuální sítě, skupiny zabezpečení sítě a hostitele bastionu

* Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Vytvořte hostitele Azure bastionu pomocí [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

* Vytvořte front-end IP adresu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) pro fond IP adres front-endu. Tato IP adresa přijímá příchozí provoz v nástroji pro vyrovnávání zatížení.

* Vytvořte fond back-endové adresy s [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) pro provoz odeslaný z front-endu nástroje pro vyrovnávání zatížení. Tento fond je místo, kde se nasazují vaše virtuální počítače back-end.

* Vytvořte sondu stavu pomocí [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) , který určuje stav instancí virtuálních počítačů back-endu.

* Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [doplňku add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) , který definuje způsob distribuce provozu do virtuálních počítačů.

* Vytvořte veřejný Nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---standard"></a>Vytváření virtuálních počítačů – Standard

V této části vytvoříte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.

* Vytvořte tři síťová rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)nastavte uživatelské jméno a heslo správce pro virtuální počítače.

* Vytvořte virtuální počítače pomocí:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

Nasazení virtuálních počítačů a hostitele bastionu se odesílají jako úlohy PowerShellu. Chcete-li zobrazit stav úloh, použijte [příkaz Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. 

Následující diagram znázorňuje prostředky vytvořené v rámci tohoto rychlého startu:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Základní prostředky nástroje pro vyrovnávání zatížení vytvořené v rychlém startu." border="false":::

## <a name="configure-virtual-network---basic"></a>Konfigurace virtuální sítě – Basic

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

Vytvořte virtuální síť pro virtuální počítače back-endu.

Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k virtuální síti.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Vytvoření virtuální sítě, skupiny zabezpečení sítě a hostitele bastionu

* Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Vytvořte hostitele Azure bastionu pomocí [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-basic-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

* Vytvořte front-end IP adresu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) pro fond IP adres front-endu. Tato IP adresa přijímá příchozí provoz v nástroji pro vyrovnávání zatížení.

* Vytvořte fond back-endové adresy s [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) pro provoz odeslaný z front-endu nástroje pro vyrovnávání zatížení. Tento fond je místo, kde se nasazují vaše virtuální počítače back-end.

* Vytvořte sondu stavu pomocí [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) , který určuje stav instancí virtuálních počítačů back-endu.

* Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [doplňku add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) , který definuje způsob distribuce provozu do virtuálních počítačů.

* Vytvořte veřejný Nástroj pro vyrovnávání zatížení pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---basic"></a>Vytvoření virtuálních počítačů – základní

V této části vytvoříte virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.

* Vytvořte tři síťová rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)nastavte uživatelské jméno a heslo správce pro virtuální počítače.

* Pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) vytvořte skupinu dostupnosti pro virtuální počítače.

* Vytvořte virtuální počítače pomocí:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvailabilitySet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

Nasazení virtuálních počítačů a hostitele bastionu se odesílají jako úlohy PowerShellu. Chcete-li zobrazit stav úloh, použijte [příkaz Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

---

## <a name="install-iis"></a>Instalace služby IIS

K instalaci rozšíření vlastních skriptů použijte [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . 

Rozšíření spustí `PowerShell Add-WindowsFeature Web-Server` za účelem instalace webového serveru služby IIS a potom aktualizuje stránku Default.htm, aby zobrazovala název hostitele virtuálního počítače:

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že se nasazování virtuálních počítačů dokončila z předchozích kroků.  Slouží `Get-Job` ke kontrole stavu úloh nasazení virtuálních počítačů.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreateIntLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

Rozšíření se nasazují jako úlohy PowerShellu. Chcete-li zobrazit stav úloh instalace, použijte [příkaz Get-Job](/powershell/module/microsoft.powershell.core/get-job):


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```


## <a name="test-the-load-balancer"></a>Testování Load Balanceru

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořit virtuální počítač pomocí:

* [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)
* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VM. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Command to create network interface for VM ##
$nic = @{
    Name = "myNicTestVM"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myTestVM"
    VMSize = 'Standard_DS1_v2' 
}
$vmos = @{
    ComputerName = "myTestVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm

```

### <a name="test"></a>Test

1. [Přihlaste se](https://portal.azure.com) na web Azure Portal.

1. Na obrazovce **Přehled** vyhledejte privátní IP adresu pro nástroj pro vyrovnávání zatížení. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myLoadBalancer**.

2. V **přehledu** **myLoadBalancer** si poznamenejte nebo zkopírujte adresu u pole **privátní IP adresa** .

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myTestVM** , která je umístěná ve skupině prostředků **CreateIntLBQS-RG** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. Zadejte IP adresu z předchozího kroku do panelu Adresa v prohlížeči. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Vytvoření standardního interního nástroje pro vyrovnávání zatížení" border="true":::
   
Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a zbývajících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateIntLBQS-rg'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořili jste interní nástroj pro vyrovnávání zatížení Standard nebo Basic.
* Připojené virtuální počítače. 
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu.
* Otestování nástroje pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte tady:
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)