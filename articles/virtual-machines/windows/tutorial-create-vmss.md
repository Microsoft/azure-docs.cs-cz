---
title: Kurz vytvoření škálovací sady virtuálních počítačů pro Windows v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShell a škálovací sady virtuálních počítačů vytvořit a nasadit vysoce dostupné aplikace na virtuálním počítači s Windows.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9d9a4a091e1dbeb1c15cd9ec0e90d7006000bea9
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928246"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Kurz: Vytvoření škálovací sady virtuálních počítačů a nasazení vysoce dostupné aplikace ve Windows pomocí Azure PowerShell
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto kurzu nasadíte škálovací sadu virtuálních počítačů v Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití rozšíření vlastních skriptů k definici webu IIS pro škálování
> * Vytvoření nástroje pro vyrovnávání zatížení pro škálovací sadu
> * Vytvoření škálovací sady virtuálních počítačů
> * Zvýšení nebo snížení počtu instancí ve škálovací sadě
> * Vytvoření pravidel automatického škálování

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="scale-set-overview"></a>Přehled škálovací sady
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Virtuální počítače ve škálovací sadě se distribuují napříč logickými doménami selhání a aktualizačními doménami v jedné nebo více *skupinách umístění*. To jsou skupiny podobně nakonfigurovaných virtuálních počítačů, podobně jako [skupiny dostupnosti](tutorial-availability-sets.md).

Virtuální počítače se ve škálovací sadě vytvářejí podle potřeby. Pro řízení, jak a kdy se virtuální počítače přidávají do škálovací sady nebo se z ní odebírají, definujete pravidla automatického škálování. Tato pravidla se můžou aktivovat na základě metrik, jako jsou zatížení procesoru, využití paměti nebo síťový provoz.

Škálovací sady podporují až 1 000 virtuálních počítačů, pokud použijete image platformy Azure. Pro úlohy se značnými požadavky na instalaci nebo přizpůsobení virtuálních počítačů možná budete chtít [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md). Pokud použijete vlastní image, můžete ve škálovací sadě vytvořit až 300 virtuálních počítačů.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá image platformy *Windows Server 2016 Datacenter*. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
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
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
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
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete vidět škálovací sadu v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá IP adresu *myPublicIP* vytvořenou jako součást škálovací sady:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Zadejte veřejnou IP adresu do webového prohlížeče. Zobrazí se webová aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz:

![Spuštění webu služby IIS](./media/tutorial-create-vmss/running-iis-site.png)

Pokud chcete vidět škálovací sadu v akci, můžete vynutit aktualizaci webového prohlížeče a zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny virtuální počítače, na kterých je vaše aplikace spuštěná.


## <a name="management-tasks"></a>Úlohy správy
V průběhu životního cyklu škálovací sady možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Azure PowerShell nabízí rychlý způsob, jak tyto úlohy provést. Tady je několik běžných úloh.

### <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů ve škálovací sadě, použijte rutinu [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) následujícím způsobem:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

Následující příklad výstupu ukazuje dvě instance virtuálních počítačů ve škálovací sadě:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Pokud chcete zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte k rutině [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parametr `-InstanceId`. Následující příklad zobrazí informace o instanci virtuálního počítače *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Navýšení nebo snížení počtu instancí virtuálních počítačů
Pokud chcete zobrazit počet instancí, které aktuálně máte ve škálovací sadě, použijte příkaz [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) s dotazem na *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Pak můžete ručně navýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí příkazu [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Následující příklad nastaví počet virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Aktualizace zadaného počtu instancí ve škálovací sadě trvá několik minut.


### <a name="configure-autoscale-rules"></a>Konfigurace pravidel automatického škálování
Místo ručního škálování počtu instancí ve škálovací sadě definujete pravidla automatického škálování. Tato pravidla monitorují instance ve vaší škálovací sadě a reagují odpovídajícím způsobem na základě metrik a prahových hodnot, které nadefinujete. Následující příklad horizontálně navýší kapacitu zvýšením počtu instancí o jednu v případě, že je průměrné zatížení procesoru vyšší než 60 % po dobu 5 minut. Pokud se pak průměrné zatížení procesoru sníží pod 30 % po dobu 5 minut, horizontálně se sníží kapacita snížením počtu instancí o jednu:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
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
$myRuleScaleDown = New-AzureRmAutoscaleRule `
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
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
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
