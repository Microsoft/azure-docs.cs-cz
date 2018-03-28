---
title: "Vytvoření škálovací sady virtuálních počítačů pomocí Azure PowerShellu | Dokumentace Microsoftu"
description: "Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů pomocí Azure PowerShellu."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: c5ca90ae303d62c4ad453971f84da78866b0c599
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-virtual-machine-scale-set-with-azure-powershell"></a>Vytvoření škálovací sady virtuálních počítačů pomocí Azure PowerShellu
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto článku Rychlý start vytvoříte škálovací sadu virtuálních počítačů pomocí Azure PowerShellu. Škálovací sadu můžete vytvořit také pomocí [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) nebo webu [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 4.4.1 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```


## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Instance virtuálních počítačů ve škálovací sadě jsou připojené k nástroji pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení Azure je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP), který poskytuje vysokou dostupnost díky distribuci příchozího provozu mezi virtuální počítače v dobrém stavu. Sonda stavu nástroje pro vyrovnávání zatížení na všech virtuálních počítačích monitoruje daný port a distribuuje provoz pouze do virtuálních počítačů, které jsou v provozu.

Vytvořte virtuální síť a nástroj pro vyrovnávání zatížení s veřejnou IP adresou, který distribuuje webový provoz na portu 80. Tyto prostředky vytvoříte zkopírováním a vložením následujících rutin PowerShellu:

```azurepowershell-interactive
# Create a virtual network subnet
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet

# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -Name "myPublicIP"

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"

# Create a Network Address Translation (NAT) pool
$inboundNATPool = New-AzureRmLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroup" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id
```


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá image platformy *Windows Server 2016 Datacenter*. Objekt *vmssConfig* vytvoří 2 instance virtuálních počítačů v oblasti Východní USA s přihlašovacími údaji zadanými v proměnných *adminUsername* a *securePassword*. Následujícím způsobem zadejte vlastní přihlašovací údaje a vytvořte škálovací sadu:

```azurepowershell-interactive
# Provide your own secure password for use with the VM instances
$securePassword = "P@ssword!"
$adminUsername = "azureuser"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location "EastUS" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher "MicrosoftWindowsServer" `
  -ImageReferenceOffer "WindowsServer" `
  -ImageReferenceSku "2016-Datacenter" `
  -ImageReferenceVersion "latest"

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername $adminUsername `
  -AdminPassword $securePassword `
  -ComputerNamePrefix "myVM"

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmssConfig
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="install-iis-webserver"></a>Instalace webového serveru služby IIS
Pokud chcete svou škálovací sadu otestovat, pomocí rozšíření vlastních skriptů stáhněte a spusťte skript, který na instance virtuálních počítačů nainstaluje službu IIS. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/windows/extensions-customscript.md).

Následujícím způsobem použijte rozšíření vlastních skriptů, které nainstaluje službu IIS:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-web-server"></a>Otestování webového serveru
Pokud chcete vidět svůj webový server v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá IP adresu vytvořenou ve skupině prostředků *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Spuštění webu služby IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), jak je znázorněno níže:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Další kroky
V tomto článku Rychlý start jste vytvořili základní škálovací sadu a pomocí rozšíření vlastních skriptů jste na instance virtuálních počítačů nainstalovali základní webový server služby IIS. Pokud chcete zlepšit škálovatelnost a automatizaci, rozšiřte svou škálovací sadu pomocí některého z těchto článků s postupy:

- [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md)
- Automatické škálování pomocí [Azure PowerShellu](virtual-machine-scale-sets-autoscale-powershell.md), [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md) nebo webu [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Použití automatických upgradů operačního systému pro instance virtuálních počítačů ve škálovací sadě](virtual-machine-scale-sets-automatic-upgrade.md)
