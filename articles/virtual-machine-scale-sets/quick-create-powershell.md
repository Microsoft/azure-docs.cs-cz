---
title: Rychlý start – Vytvoření škálovací sady virtuálních počítačů pomocí Azure PowerShellu | Microsoft Docs
description: Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů pomocí Azure PowerShellu.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: iainfou
ms.openlocfilehash: b50c72756f96a8f7d121c72f9f24065f810852ff
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362767"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Rychlý start: Vytvoření škálovací sady virtuálních počítačů pomocí Azure PowerShellu
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Počet virtuálních počítačů ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. Nástroj pro vyrovnávání zatížení Azure pak bude distribuovat provoz do instancí virtuálních počítačů ve škálovací sadě. V tomto rychlém startu vytvoříte škálovací sadu virtuálních počítačů a nasadíte ukázkovou aplikaci pomocí Azure PowerShellu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá image platformy *Windows Server 2016 Datacenter*. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
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
Pokud chcete svou škálovací sadu otestovat, nainstalujte základní webovou aplikaci. Ke stažení a spuštění skriptu, který na instance virtuálních počítačů nainstaluje službu IIS, se používá rozšíření vlastních skriptů Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/windows/extensions-customscript.md).

Pomocí rozšíření vlastních skriptů nainstalujte základní webový server služby IIS. Následujícím způsobem použijte rozšíření vlastních skriptů, které nainstaluje službu IIS:

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


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete vidět svou škálovací sadu v akci, přejděte ve webovém prohlížeči na ukázkovou webovou aplikaci. Získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá IP adresu vytvořenou ve skupině prostředků *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Spuštění webu služby IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), jak je znázorněno níže. Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili základní škálovací sadu a pomocí rozšíření vlastních skriptů jste na instance virtuálních počítačů nainstalovali základní webový server služby IIS. Další informace najdete v kurzu věnovaném vytváření a správě škálovacích sad virtuálních počítačů Azure.

> [!div class="nextstepaction"]
> [Vytváření a správa škálovacích sad virtuálních počítačů Azure](tutorial-create-and-manage-powershell.md)
