---
title: Kurz – instalace aplikací ve škálovací sadě pomocí Azure PowerShellu
description: Zjistěte, jak pomocí Azure PowerShellu instalovat aplikace do škálovacích sad virtuálních počítačů s využitím rozšíření vlastních skriptů.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 789b3ca6cf49bbecf7393c5c70b19cbb43dfd164
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011255"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Kurz: Instalace aplikací ve škálovacích sadách virtuálních počítačů pomocí Azure PowerShellu

Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. V předchozím kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače k nasazení instancí virtuálních počítačů. Tato vlastní image zahrnovala ruční instalaci a konfiguraci aplikací. Můžete automatizovat také instalaci aplikací do škálovací sady po nasazení všech instancí virtuálních počítačů nebo aktualizaci aplikace, která je již ve škálovací sadě spuštěná. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Co je rozšíření vlastních skriptů Azure?
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal.

Rozšíření Vlastní skript se integruje se šablonami Azure Resource Manager. Dá se taky použít s rozhraním API Azure, Azure PowerShell, portál Azure nebo rozhraní REST API. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/windows/extensions-customscript.md).

Pokud se chcete podívat na rozšíření vlastních skriptů v akci, vytvořte škálovací sadu, která nainstaluje webový server služby IIS a vypíše název hostitele instance virtuálního počítače ve škálovací sadě. Definice rozšíření vlastních skriptů stáhne ukázkový skript z GitHubu, nainstaluje požadované balíčky a pak vypíše název hostitele instance virtuálního počítače na základní stránce HTML.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Nyní vytvořte škálovací sadu virtuálních strojů s [novou AzVmss](/powershell/module/az.compute/new-azvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Správce zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80. Umožňuje také provoz vzdálené plochy na portu TCP 3389 a vzdálené vzdálené komunikace prostředí PowerShell na portu TCP 5985. Po zobrazení výzvy můžete nastavit vlastní pověření pro správu pro instance virtuálních počítače ve škálovací sadě:

```azurepowershell-interactive
New-AzVmss `
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


Nyní použijte rozšíření vlastní skript s [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). Do rozšíření se přidá objekt konfigurace definovaný dříve. Aktualizujte a spusťte rozšíření na instancích virtuálních počítačí pomocí [update-AzVmss](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Každá instance virtuálního počítače ve škálovací sadě stáhne a spustí skript z GitHubu. Ve složitějším příkladu se můžou nainstalovat komponenty a soubory několika aplikací. Pokud dojde k vertikálnímu navýšení kapacity škálovací sady, nové instance virtuálních počítačů automaticky použijí stejnou definici rozšíření vlastních skriptů a nainstalují požadovanou aplikaci.


## <a name="allow-traffic-to-application"></a>Povolení provozu do aplikace

Chcete-li povolit přístup k základní webové aplikaci, vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Další informace najdete [v tématu Sítě pro škálovací sady virtuálních strojů Azure](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive

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
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

```



## <a name="test-your-scale-set"></a>Test škálovací sady
Chcete-li zobrazit váš webový server v akci, získejte veřejnou IP adresu vašeho vyrovnávání zatížení pomocí [služby Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). V následujícím příkladu je zobrazena adresa IP vytvořená ve skupině prostředků *myResourceGroup:*

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
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

Aktualizujte konfiguraci rozšíření vlastního skriptu na instance virtuálních počítačů ve škálovací sadě. Definice *customConfigv2* slouží k použití aktualizované verze aplikace:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Na všech instancích virtuálních počítačů ve škálovací sadě se ukázková webová stránka automaticky aktualizuje na nejnovější verzi. Pokud chcete zobrazit aktualizovanou verzi, aktualizujte webovou stránku ve svém prohlížeči:

![Aktualizovaná webová stránka ve službě IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Chcete-li odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí [skupiny Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak automaticky instalovat a aktualizovat aplikace ve škálovací sadě pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

V dalším kurzu se dozvíte, jak škálovací sadu automaticky škálovat.

> [!div class="nextstepaction"]
> [Automatické škálování škálovacích sad](tutorial-autoscale-powershell.md)
