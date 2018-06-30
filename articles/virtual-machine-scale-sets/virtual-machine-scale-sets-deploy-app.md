---
title: Nasazení aplikace na sadu škálování virtuálního počítače Azure | Microsoft Docs
description: Zjistěte, jak nasadit aplikace pro Linux a Windows instancí virtuálního počítače ve škálovací sadě
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: ed43dc21c8b7c585abc0a2734a541f760ab3c487
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111559"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Nasazení aplikace na sady škálování virtuálního počítače
Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. Tento článek představuje způsoby, jak vytvořit vlastní image virtuálního počítače pro instance v škálování nastavit nebo automatické spouštění skriptů instalace v existující instance virtuálních počítačů. Také zjistíte, jak spravovat aplikace nebo aktualizace operačního systému v rámci sady škálování.


## <a name="build-a-custom-vm-image"></a>Sestavení vlastní image virtuálního počítače
Když použijete jednu z imagí platformy Azure k vytvoření instance ve škálovací sadě, žádný další software je nainstalovaná nebo nakonfigurovaná. Můžete automatizovat instalace těchto součástí, ale která přidává do čas potřebný ke zřízení instance, které chcete škálovací sady virtuálních počítačů. Pokud použijete mnoho změn konfigurace pro instance virtuálních počítačů, je Správa spojené s těmito konfigurační skripty a úlohy.

Pokud chcete zkrátit configuration management a čas pro zřízení virtuálního počítače, můžete vytvořit vlastní image virtuálního počítače, který je připraven ke spuštění aplikace v sadě škálování při zřízení instance. Další informace o tom, jak vytvořit a použít vlastní image virtuálního počítače s měřítkem nastavení najdete v následujících kurzech:

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalace aplikace s rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Další informace o tom, jak vytvořit a použít vlastní image virtuálního počítače s měřítkem nastavení najdete v následujících kurzech:

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Šablona Azure Resource Manageru](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalace aplikace pro Windows virtuálního počítače pomocí prostředí PowerShell DSC
[Prostředí PowerShell požadovaného stavu konfigurace (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) je platforma pro správu k definování konfigurace cílových počítačů. Konfigurace DSC definovat, co je potřeba nainstalovat na počítači a postup konfigurace hostitele. Modul Místní Configuration Manager (LCM) běží na každý cílový uzel, který zpracovává požadovaná akce na základě stisknutí konfigurací.

Rozšíření DSC prostředí PowerShell umožňuje přizpůsobit instance virtuálních počítačů v škálování, nastavte pomocí prostředí PowerShell. V následujícím příkladu:

- Dá pokyn instance virtuálních počítačů DSC balíček stáhnout z webu GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Nastaví rozšíření pro spuštění skriptu install- `configure-http.ps1`
- Získá informace o škálování s [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Platí pro instance virtuálních počítačů s příponou [AzureRmVmss aktualizace](/powershell/module/azurerm.compute/update-azurermvmss)

Rozšíření DSC se použijí na *myScaleSet* instance virtuálních počítačů ve skupině prostředků s názvem *myResourceGroup*. Zadejte vlastní názvy následujícím způsobem:

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
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Pokud je aktualizace zásad na škálovací sadu *ruční*, aktualizovat instancím virtuálních počítačů s [aktualizace AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Tato rutina platí konfigurace sady aktualizované škálování pro instance virtuálních počítačů a nainstaluje aplikace.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalace aplikace na virtuální počítače Linux s inicializací cloudu
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Další informace, včetně příklad *cloudu init.txt* souborů najdete v tématu [použít k přizpůsobení virtuálních počítačích Azure cloud init](../virtual-machines/linux/using-cloud-init.md).

Pokud chcete vytvořit sadu škálování a použít soubor init cloudu, přidejte `--custom-data` parametru [vytvořit az vmss](/cli/azure/vmss#az_vmss_create) příkaz a zadejte název souboru init cloudu. Následující příklad vytvoří škálování nastavení s názvem *myScaleSet* v *myResourceGroup* a nakonfiguruje instance virtuálních počítačů se soubor s názvem *cloudu init.txt*. Zadejte vlastní názvy následujícím způsobem:

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


### <a name="install-applications-with-os-updates"></a>Instalovat aplikace s aktualizacemi operačního systému
Když jsou k dispozici nové verze operačního systému, můžete použít nebo vytvořit novou vlastní bitovou kopii a [nasazení upgradu OS](virtual-machine-scale-sets-upgrade-scale-set.md) na měřítko nastavit. Každá instance virtuálního počítače je upgradován na nejnovější bitovou kopii, která zadáte. Vlastní image s předinstalovaným, aplikací rozšíření vlastních skriptů nebo DSC prostředí PowerShell můžete použít tak, aby měl vaší aplikace, které jsou automaticky dostupné, jak provést upgrade. Potřebujete plánu údržby aplikace, jak provést tento postup zajistěte, aby žádná verze problémy s kompatibilitou.

Pokud používáte vlastní image virtuálního počítače s předinstalovaným aplikací, může integrovat kanál nasazení a vytvářet nové bitové kopie nasazení operačního systému upgrady v rámci škálovací sadu aktualizací aplikace. Tento přístup umožňuje kanálu, který má být vyzvednutí nejnovější sestavení aplikace, vytvořit a ověřit image virtuálního počítače a pak upgradovat instance virtuálních počítačů v sadě škálování. Pokud chcete spustit nasazení kanálu, který vytvoří a nasadí aplikaci aktualizace napříč vlastní Image virtuálních počítačů, může [bitovou kopii balírna vytvářet a nasazovat s Visual Studio Team Services](/vsts/pipelines/apps/cd/azure/deploy-azure-scaleset), nebo pomocí jiné platformy, jako například [Spinnaker ](https://www.spinnaker.io/) nebo [volaných](https://jenkins.io/).


## <a name="next-steps"></a>Další postup
Jak vytvořit a nasadit aplikace do vaší sady škálování, můžete zkontrolovat [přehled návrhu nastavení škálování](virtual-machine-scale-sets-design-overview.md). Další informace o tom, jak spravovat škálovací sadu najdete v tématu [použití Powershellu ke správě škálovací sadu](virtual-machine-scale-sets-windows-manage.md).
