---
title: Nasazení aplikace do sady škálování virtuálních počítačů Azure | Microsoft Docs
description: Naučte se nasazovat aplikace do instancí virtuálních počítačů se systémy Linux a Windows v sadě škálování.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 0dc1c52e65090acd5f63d1b23d8da6f37e3cf567
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960724"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Nasazení aplikace ve službě Virtual Machine Scale Sets

Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. V tomto článku se seznámíte s způsoby vytvoření vlastní image virtuálního počítače pro instance v sadě škálování nebo automatické spouštění instalačních skriptů na existujících instancích virtuálních počítačů. Naučíte se také, jak spravovat aktualizace aplikace nebo operačního systému v rámci sady škálování.


## <a name="build-a-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače
Když použijete některou z imagí platformy Azure k vytvoření instancí v sadě škálování, není nainstalovaný ani nakonfigurovaný žádný další software. Instalaci těchto součástí můžete automatizovat, ale přidáváme vám čas potřebný ke zřízení instancí virtuálních počítačů pro vaše sady škálování. Pokud v instancích virtuálních počítačů použijete mnoho změn konfigurace, bude se jednat o režii správy s těmito konfiguračními skripty a úlohami.

Pokud chcete omezit správu a dobu konfigurace virtuálního počítače, můžete vytvořit vlastní image virtuálního počítače, která je připravená ke spuštění vaší aplikace hned po zřízení instance v sadě škálování. Další informace o tom, jak vytvořit a použít vlastní image virtuálního počítače se sadou škálování, najdete v následujících kurzech:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalace aplikace pomocí rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Další informace o tom, jak nainstalovat aplikaci pomocí rozšíření vlastních skriptů, najdete v následujících kurzech:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Šablona Azure Resource Manageru](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalace aplikace do virtuálního počítače s Windows pomocí prostředí PowerShell DSC
[Prostředí PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) je platforma pro správu k definování konfigurace cílových počítačích. Konfigurace DSC definovat, co se má nainstalovat na počítač a jak nakonfigurovat hostitele. Modul místního Configuration Manageru (LCM) běží na každý cílový uzel, který zpracovává nabízená konfigurace požadované akce.

Rozšíření PowerShell DSC umožňuje přizpůsobit instance virtuálních počítačů v sadě škálování pomocí PowerShellu. Následující příklad:

- Vydá pokyn instancím virtuálních počítačů ke stažení balíčku DSC z GitHubu – *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Nastaví rozšíření pro spuštění instalačního skriptu – `configure-http.ps1`
- Načte informace o škálované sadě pomocí rutiny [Get-AzVmss](/powershell/module/az.compute/get-azvmss) .
- Aplikuje rozšíření na instance virtuálních počítačů pomocí [Update-AzVmss](/powershell/module/az.compute/update-azvmss) .

Rozšíření DSC se aplikuje na instance virtuálních počítačů *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*. Zadejte vlastní názvy následujícím způsobem:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Pokud jsou zásady upgradu v sadě škálování *Ruční*, aktualizujte instance virtuálních počítačů pomocí [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Tato rutina aplikuje aktualizovanou konfiguraci sady škálování na instance virtuálních počítačů a nainstaluje vaši aplikaci.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalace aplikace do virtuálního počítače se systémem Linux pomocí Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Další informace, včetně ukázkového souboru *Cloud-init. txt* , najdete v tématu věnovaném [použití Cloud-init k přizpůsobení virtuálních počítačů Azure](../virtual-machines/linux/using-cloud-init.md).

Pokud chcete vytvořit sadu škálování a použít soubor Cloud-init, přidejte do příkazu [AZ VMSS Create](/cli/azure/vmss) parametr `--custom-data` a zadejte název souboru Cloud-init. Následující příklad vytvoří sadu škálování s názvem *myScaleSet* v *myResourceGroup* a nakonfiguruje instance virtuálních počítačů pomocí souboru s názvem *Cloud-init. txt*. Zadejte vlastní názvy následujícím způsobem:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Instalace aplikací s aktualizacemi operačního systému
Když jsou k dispozici nové verze operačního systému, můžete použít nebo sestavit novou vlastní image a [nasadit upgrady operačního systému](virtual-machine-scale-sets-upgrade-scale-set.md) do sady škálování. Každá instance virtuálního počítače je upgradována na nejnovější obrázek, který zadáte. Vlastní image s předinstalovanou aplikací, rozšířením vlastního skriptu nebo prostředím PowerShell DSC můžete použít k automatickému zpřístupnění při provedení upgradu. Při provádění tohoto procesu může být nutné naplánovat údržbu aplikace, aby nedocházelo k žádným problémům s kompatibilitou verzí.

Pokud používáte vlastní image virtuálního počítače s předinstalovanou aplikací, mohli byste integrovat aktualizace aplikace pomocí kanálu nasazení a sestavit nové image a nasadit upgrady operačního systému napříč sadou škálování. Tento přístup umožňuje kanálu vybrat nejnovější sestavení aplikace, vytvořit a ověřit image virtuálního počítače a pak upgradovat instance virtuálních počítačů v sadě škálování. Pokud chcete spustit kanál nasazení, který sestaví a nasadí aktualizace aplikací napříč vlastními imagemi virtuálních počítačů, můžete [vytvořit image balíčku a nasadit ji pomocí Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)nebo použít jinou platformu, jako je například [Spinnaker](https://www.spinnaker.io/) nebo [Jenkinse](https://jenkins.io/).


## <a name="next-steps"></a>Další kroky
Při sestavování a nasazování aplikací do sady škálování můžete zkontrolovat [Přehled návrhu sady škálování](virtual-machine-scale-sets-design-overview.md). Další informace o tom, jak spravovat sadu škálování, najdete v tématu [použití PowerShellu ke správě sady škálování](virtual-machine-scale-sets-windows-manage.md).
