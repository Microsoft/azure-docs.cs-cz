---
title: 'Kurz: Vytvoření škálovací sady virtuálních strojů pro Windows'
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit a nasadit vysoce dostupnou aplikaci na virtuálních počítačích s Windows pomocí škálovací sady virtuálních strojů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bf53b2777c5d1e4d774a9f5ee9df119a0deac9d9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75464974"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Kurz: Vytvoření škálovací sady virtuálních počítačů a nasazení vysoce dostupné aplikace ve Windows pomocí Azure PowerShell
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Počet virtuálních zařízení můžete škálovat v škálovací sadě ručně. Můžete také definovat pravidla pro automatické škálování na základě využití prostředků, jako je procesor, požadavek na paměť nebo síťový provoz. V tomto kurzu nasadíte škálovací sadu virtuálních strojů v Azure a naučíte se:

> [!div class="checklist"]
> * Použití rozšíření vlastních skriptů k definici webu IIS pro škálování
> * Vytvoření nástroje pro vyrovnávání zatížení pro škálovací sadu
> * Vytvoření škálovací sady virtuálních počítačů
> * Zvýšení nebo snížení počtu instancí ve škálovací sadě
> * Vytvoření pravidel automatického škálování

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="scale-set-overview"></a>Přehled škálovací sady
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Virtuální počítače ve škálovací sadě se distribuují napříč logickými doménami selhání a aktualizačními doménami v jedné nebo více *skupinách umístění*. Skupiny umístění jsou skupiny podobně nakonfigurovaných virtuálních počítače, podobně jako [skupiny dostupnosti](tutorial-availability-sets.md).

Virtuální počítače se ve škálovací sadě vytvářejí podle potřeby. Pro řízení, jak a kdy se virtuální počítače přidávají do škálovací sady nebo se z ní odebírají, definujete pravidla automatického škálování. Tato pravidla se můžou aktivovat na základě metrik, jako jsou zatížení procesoru, využití paměti nebo síťový provoz.

Škálovací sady podporují až 1 000 virtuálních počítačů, pokud použijete image platformy Azure. Pro úlohy se značnými požadavky na instalaci nebo přizpůsobení virtuálních počítačů možná budete chtít [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md). Pokud použijete vlastní image, můžete ve škálovací sadě vytvořit až 300 virtuálních počítačů.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Vytvořte škálovací sadu virtuálních strojů s [novou AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss). Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá image platformy *Windows Server 2016 Datacenter*. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy můžete nastavit vlastní pověření pro správu pro instance virtuálních počítače ve škálovací sadě:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="deploy-sample-application"></a>Nasazení ukázkové aplikace
Pokud chcete svou škálovací sadu otestovat, nainstalujte základní webovou aplikaci. Ke stažení a spuštění skriptu, který na instance virtuálních počítačů nainstaluje službu IIS, se používá rozšíření vlastních skriptů Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](extensions-customscript.md).

Pomocí rozšíření vlastních skriptů nainstalujte základní webový server služby IIS. Následujícím způsobem použijte rozšíření vlastních skriptů, které nainstaluje službu IIS:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Povolení provozu do aplikace

Chcete-li povolit přístup k základní webové aplikaci, vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) a [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup). Další informace najdete [v tématu Sítě pro škálovací sady virtuálních strojů Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Test škálovací sady
Chcete-li zobrazit nastavené měřítko v akci, získejte veřejnou IP adresu vašeho vyvažovače zatížení pomocí [aplikace Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). V následujícím příkladu se zobrazí IP adresa *myPublicIP* vytvořená jako součást škálovací sady:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

Zadejte veřejnou IP adresu do webového prohlížeče. Zobrazí se webová aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz:

![Spuštění webu služby IIS](./media/tutorial-create-vmss/running-iis-site.png)

Pokud chcete vidět škálovací sadu v akci, můžete vynutit aktualizaci webového prohlížeče a zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny virtuální počítače, na kterých je vaše aplikace spuštěná.


## <a name="management-tasks"></a>Úlohy správy
V průběhu životního cyklu škálovací sady možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Azure PowerShell nabízí rychlý způsob, jak tyto úlohy provést. Tady je několik běžných úloh.

### <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Chcete-li zobrazit seznam instancí virtuálních virtuálních společností ve škálovací sadě, použijte [Get-AzVmssVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm) následujícím způsobem:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

Následující příklad výstupu ukazuje dvě instance virtuálních počítačů ve škálovací sadě:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Chcete-li zobrazit další informace o konkrétní `-InstanceId` instanci virtuálního soudu, přidejte parametr [do Get-AzVmssVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm). Následující příklad zobrazí informace o instanci virtuálního počítače *1*:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Navýšení nebo snížení počtu instancí virtuálních počítačů
Chcete-li zobrazit počet instancí, které aktuálně máte v škálovací sadě, použijte [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) a dotaz na *sku.capacity*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

Potom můžete ručně zvýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). Následující příklad nastaví počet virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Aktualizace zadaného počtu instancí ve škálovací sadě trvá několik minut.


### <a name="configure-autoscale-rules"></a>Konfigurace pravidel automatického škálování
Místo ručního škálování počtu instancí ve škálovací sadě definujete pravidla automatického škálování. Tato pravidla monitorují instance ve vaší škálovací sadě a reagují odpovídajícím způsobem na základě metrik a prahových hodnot, které nadefinujete. Následující příklad horizontálně navýší kapacitu zvýšením počtu instancí o jednu v případě, že je průměrné zatížení procesoru vyšší než 60 % po dobu 5 minut. Pokud se pak průměrné zatížení procesoru sníží pod 30 % po dobu 5 minut, horizontálně se sníží kapacita snížením počtu instancí o jednu:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Další informace o návrhu v souvislosti s používáním automatického škálování najdete v [osvědčených postupech pro automatické škálování](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili škálovací sadu virtuálních počítačů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Použití rozšíření vlastních skriptů k definici webu IIS pro škálování
> * Vytvoření nástroje pro vyrovnávání zatížení pro škálovací sadu
> * Vytvoření škálovací sady virtuálních počítačů
> * Zvýšení nebo snížení počtu instancí ve škálovací sadě
> * Vytvoření pravidel automatického škálování

Přejděte k dalšímu kurzu, kde najdete další informace o konceptech vyrovnávání zatížení virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vyrovnání zatížení virtuálních počítačů](tutorial-load-balancer.md)
