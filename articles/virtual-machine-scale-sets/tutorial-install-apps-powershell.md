---
title: Kurz – Instalace aplikací ve škálovací sadě pomocí Azure PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí Azure PowerShellu instalovat aplikace do škálovacích sad virtuálních počítačů s využitím rozšíření vlastních skriptů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: a806c83f585ff276d9cbfe7cca63ab15011949bb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311394"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Kurz: Instalace aplikací ve škálovacích sadách virtuálních počítačů pomocí Azure PowerShellu
Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. V předchozím kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače k nasazení instancí virtuálních počítačů. Tato vlastní image zahrnovala ruční instalaci a konfiguraci aplikací. Můžete automatizovat také instalaci aplikací do škálovací sady po nasazení všech instancí virtuálních počítačů nebo aktualizaci aplikace, která je již ve škálovací sadě spuštěná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure. 


## <a name="what-is-the-azure-custom-script-extension"></a>Co je rozšíření vlastních skriptů Azure?
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal.

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru. Taky jde použít s Azure CLI, Azure Powershellu, portálu Azure portal nebo rozhraní REST API. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/windows/extensions-customscript.md).

Pokud se chcete podívat na rozšíření vlastních skriptů v akci, vytvořte škálovací sadu, která nainstaluje webový server služby IIS a vypíše název hostitele instance virtuálního počítače ve škálovací sadě. Definice rozšíření vlastních skriptů stáhne ukázkový skript z GitHubu, nainstaluje požadované balíčky a pak vypíše název hostitele instance virtuálního počítače na základní stránce HTML.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80. Umožňuje také provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace Powershellu na portu TCP 5985. Po zobrazení výzvy, můžete nastavit vlastní pověření správce pro instance virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="create-custom-script-extension-definition"></a>Vytvoření definice rozšíření vlastních skriptů
Azure PowerShell k uložení souboru ke stažení a příkazu ke spuštění používá zatřiďovací tabulku. V následujícím příkladu se používá ukázkový skript z GitHubu. Nejprve následujícím způsobem vytvořte tento objekt konfigurace:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

Teď použijte rozšíření vlastních skriptů pomocí rutiny [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension). Do rozšíření se přidá objekt konfigurace definovaný dříve. Aktualizujte a spusťte rozšíření na instancích virtuálních počítačů pomocí rutiny [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Každá instance virtuálního počítače ve škálovací sadě stáhne a spustí skript z GitHubu. Ve složitějším příkladu se můžou nainstalovat komponenty a soubory několika aplikací. Pokud dojde k vertikálnímu navýšení kapacity škálovací sady, nové instance virtuálních počítačů automaticky použijí stejnou definici rozšíření vlastních skriptů a nainstalují požadovanou aplikaci.


## <a name="allow-traffic-to-application"></a>Povolení provozu do aplikace

Povolit přístup k základní webovou aplikaci, vytvořte skupinu zabezpečení sítě s [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Další informace najdete v tématu [síťové služby pro škálovací sady virtuálních počítačů Azure](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
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
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```



## <a name="test-your-scale-set"></a>Test škálovací sady
Chcete-li vidět svůj webový server v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení s [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad zobrazuje IP adresu vytvořenou ve *myResourceGroup* skupina prostředků:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Základní webová stránka ve službě IIS](media/tutorial-install-apps-powershell/running-iis.png)

Nechte webový prohlížeč otevřený, abyste v dalším kroku viděli aktualizovanou verzi.


## <a name="update-app-deployment"></a>Aktualizace nasazení aplikace
V průběhu životního cyklu škálovací sady možná budete potřebovat nasadit aktualizovanou verzi své aplikace. Díky rozšíření vlastních skriptů můžete přidat odkaz na aktualizovaný skript nasazení a pak znovu použít rozšíření na škálovací sadu. Při vytvoření škálovací sady v předchozím kroku se hodnota `-UpgradePolicyMode` nastavila na *Automatic* (Automaticky). Toto nastavení umožňuje instancím virtuálních počítačů ve škálovací sadě automaticky aktualizovat a používat nejnovější verzi vaší aplikace.

Vytvořte novou definici konfigurace *customConfigv2*. Tato definice spustí aktualizovanou verzi *v2* instalačního skriptu aplikace:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Aktualizujte konfiguraci rozšíření vlastních skriptů na instance virtuálních počítačů ve škálovací sadě. Definice *customConfigv2* slouží k použití aktualizované verze aplikace:

```azurepowershell-interactive
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Na všech instancích virtuálních počítačů ve škálovací sadě se ukázková webová stránka automaticky aktualizuje na nejnovější verzi. Pokud chcete zobrazit aktualizovanou verzi, aktualizujte webovou stránku ve svém prohlížeči:

![Aktualizovaná webová stránka ve službě IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí rutiny [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak automaticky instalovat a aktualizovat aplikace ve škálovací sadě pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

V dalším kurzu se dozvíte, jak škálovací sadu automaticky škálovat.

> [!div class="nextstepaction"]
> [Automatické škálování škálovacích sad](tutorial-autoscale-powershell.md)
