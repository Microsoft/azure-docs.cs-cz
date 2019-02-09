---
title: Nasazení aplikace do škálovací sady virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikace pro Linux a Windows instancí virtuálních počítačů ve škálovací sadě
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
ms.openlocfilehash: 09145612821cb669e26e3ccb8d15611112eca700
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980070"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Nasazení aplikace do škálovací sady virtuálních počítačů

Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. Tento článek představuje způsobů, jak vytvářet vlastní image virtuálního počítače pro instancí ve škálovací, nastavení nebo automatické spouštění skriptů instalace v existujících instancích virtuálních počítačů. Také se dozvíte, jak spravovat aplikace nebo aktualizace operačního systému na škálovací sadu.


## <a name="build-a-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače
Pokud používáte některou k imagí platformy Azure k vytvoření instancí ve škálovací sadě, žádný další software je nainstalovaný nebo nakonfigurován. Můžete automatizovat instalaci těchto součástí, ale, který přidá do čas potřebný ke zřízení instance škálovací sady virtuálních počítačů. Pokud použijete mnoho změn konfigurace na instance virtuálních počítačů, je Správa režie pomocí těchto konfigurační skripty a úlohy.

Pokud chcete snížit správu konfigurací a čas pro zřízení virtuálního počítače, můžete vytvořit vlastní image virtuálního počítače, který je připraven ke spuštění aplikace co nejdříve po zřízení instance ve škálovací sadě. Další informace o tom, jak vytvořit a používat vlastní image virtuálního počítače se škálovací sady najdete v následujících kurzech:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalace aplikace s využitím rozšíření vlastních skriptů
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Další informace o tom, jak vytvořit a používat vlastní image virtuálního počítače se škálovací sady najdete v následujících kurzech:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Šablona Azure Resource Manageru](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalace aplikace pro Windows virtuální počítač s prostředím PowerShell DSC
[Prostředí PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) je platforma pro správu k definování konfigurace cílových počítačích. Konfigurace DSC definovat, co se má nainstalovat na počítač a jak nakonfigurovat hostitele. Modul místního Configuration Manageru (LCM) běží na každý cílový uzel, který zpracovává nabízená konfigurace požadované akce.

Rozšíření PowerShell DSC umožňuje přizpůsobit instancí virtuálních počítačů ve škálovací sadě pomocí Powershellu. V následujícím příkladu:

- Dává pokyn stáhnout DSC balíček z webu GitHub - instance virtuálních počítačů *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Nastaví rozšíření pro spuštění skriptu install- `configure-http.ps1`
- Získá informace o škálovací sadu s [Get AzVmss](/powershell/module/az.compute/get-azvmss)
- Rozšíření se vztahuje na instance virtuálních počítačů s [AzVmss aktualizace](/powershell/module/az.compute/update-azvmss)

K použití tohoto rozšíření DSC *myScaleSet* instancí virtuálních počítačů ve skupině prostředků s názvem *myResourceGroup*. Zadejte vlastní názvy následujícím způsobem:

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

Pokud zásady upgradu ve škálovací sadě je *ruční*, aktualizovat vaše instance virtuálních počítačů s [aktualizace AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Tato rutina konfigurace aktualizace škálovací sady se vztahuje na instance virtuálních počítačů a nainstaluje vaši aplikaci.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalace aplikace na virtuální počítač s Linuxem pomocí cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Další informace, včetně příklad *cloud-init.txt* souborů naleznete v tématu [použití cloud-init přizpůsobí virtuální počítače Azure](../virtual-machines/linux/using-cloud-init.md).

Pokud chcete vytvořit škálovací sadu a používat soubor cloud-init, přidejte `--custom-data` parametr [az vmss vytvořit](/cli/azure/vmss) příkaz a zadejte název souboru cloud-init. Následující příklad vytvoří škálovací sadu s názvem *myScaleSet* v *myResourceGroup* a nakonfiguruje instancí virtuálních počítačů pomocí souboru s názvem *cloud-init.txt*. Zadejte vlastní názvy následujícím způsobem:

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
Když novou verzí operačního systému jsou k dispozici, můžete použít nebo vytvořit nové vlastní image a [nasazování upgradů operačního systému](virtual-machine-scale-sets-upgrade-scale-set.md) škálovací nastavte. Všechny instance virtuálních počítačů je upgradovat na nejnovější image, který zadáte. Vlastní image předem nainstalovaná aplikace rozšíření vlastních skriptů nebo prostředí PowerShell DSC můžete mít vaše aplikace automaticky k dispozici při provádění upgradu. Budete muset plánování údržby aplikace při provádění tohoto procesu, abyste zajistili, že zde nejsou žádné verze problémy s kompatibilitou.

Pokud používáte vlastní image virtuálního počítače s předem nainstalovaná aplikace, může aktualizace aplikace integrovat nasazení kanálu pro sestavování nových imagí a nasazování upgradů operačního systému ve škálovací sadě. Tento přístup umožňuje kanálu tak, aby se získaly nejnovější sestavení aplikace, vytvořit a ověřit image virtuálního počítače a potom upgradovat instance virtuálních počítačů ve škálovací sadě. Pokud chcete spustit kanál nasazení, který sestaví a nasadí aktualizace aplikace ve vlastních imagí virtuálních počítačů, může [image Packeru vytvořit a nasadit pomocí služeb Azure DevOps](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), nebo použít jinou platformu, jako [Spinnaker](https://www.spinnaker.io/) nebo [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Další postup
Při vývoji a nasazení aplikací do škálovací sady, můžete zkontrolovat [přehled návrhu nastavte škálování](virtual-machine-scale-sets-design-overview.md). Další informace o tom, jak Správa škálovací sady, naleznete v tématu [použití Powershellu ke správě škálovací sady](virtual-machine-scale-sets-windows-manage.md).
