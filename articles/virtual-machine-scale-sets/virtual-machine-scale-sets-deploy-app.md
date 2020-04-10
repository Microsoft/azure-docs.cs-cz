---
title: Nasazení aplikace do škálovací sady virtuálních strojů Azure
description: Zjistěte, jak nasadit aplikace do instancí virtuálních strojů Linuxu a Windows ve škálovací sadě
author: ju-shim
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: e157db79f1835a8ca891dd360a99e3319565d1d4
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011459"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Nasazení aplikace do škálovací sady virtuálních počítačů

Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. Tento článek představuje způsoby, jak vytvořit vlastní image virtuálního počítače pro instance ve škálovací sadě nebo automaticky spustit instalační skripty na existujících instancích virtuálních počítačů. Dozvíte se také, jak spravovat aktualizace aplikací nebo operačního systému v škálovací sadě.


## <a name="build-a-custom-vm-image"></a>Vytvoření vlastní image virtuálního virtuálního virtuálního
Když použijete jednu z ibi platformy Azure k vytvoření instancí ve škálovací sadě, nenainstaluje se ani nenakonfiguruje žádný další software. Můžete automatizovat instalaci těchto součástí, ale to zvyšuje čas potřebný k zřízení instance virtuálních počítačových služeb do škálovacích sad. Pokud na instance virtuálních počítačů použijete mnoho změn konfigurace, bude s těmito konfiguračními skripty a úkoly režie na správu.

Chcete-li snížit správu konfigurace a čas na zřízení virtuálního počítače, můžete vytvořit vlastní image virtuálního počítače, který je připraven ke spuštění aplikace, jakmile je instance zřízena v škálovací sadě. Další informace o tom, jak vytvořit a použít vlastní image virtuálního počítače s škálovací sadou, najdete v následujících kurzech:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instalace aplikace s rozšířením Vlastní skript
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Další informace o instalaci aplikace s rozšířením vlastního skriptu najdete v následujících kurzech:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Šablona Azure Resource Manageru](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalace aplikace do virtuálního virtuálního aplikace windows s PowerShell DSC
[Konfigurace požadovaného stavu prostředí PowerShell (DSC)](/powershell/scripting/dsc/overview/overview) je platforma pro správu, která definuje konfiguraci cílových počítačů. Konfigurace DSC definují, co se má do počítače nainstalovat a jak nakonfigurovat hostitele. Modul místního configuration manageru (LCM) běží na každém cílovém uzlu, který zpracovává požadované akce na základě nabízených konfigurací.

Rozšíření PowerShell DSC umožňuje přizpůsobit instance virtuálních virtuálních tělísek ve škálovací sadě pomocí Prostředí PowerShell. Následující příklad:

- Dává instanvám virtuálních vojínů pokyn ke stažení balíčku DSC z GitHubu –*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Nastaví rozšíření tak, aby spouštěl instalační skript -`configure-http.ps1`
- Získá informace o škálovací sadě s [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Použije rozšíření na instance virtuálních mís s [update-AzVmss](/powershell/module/az.compute/update-azvmss)

Rozšíření DSC se použije na instance virtuálního virtuálního pracovního *prostředí myScaleSet* ve skupině prostředků s názvem *myResourceGroup*. Zadejte svá vlastní jména takto:

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

Pokud je zásada upgradu ve vaší škálovací sadě *ruční*, aktualizujte instance virtuálních zařízení [pomocí služby Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Tato rutina použije aktualizovanou konfiguraci škálovací sady na instance virtuálních počítačů a nainstaluje vaši aplikaci.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalace aplikace do virtuálního počítače s Linuxem s cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Další informace, včetně ukázkového souboru *cloud-init.txt,* najdete [v tématu Použití cloud-initu k přizpůsobení virtuálních počítačů Azure](../virtual-machines/linux/using-cloud-init.md).

Chcete-li vytvořit škálovací sadu a použít soubor `--custom-data` cloud-init, přidejte parametr do [příkazu az vmss create](/cli/azure/vmss) a zadejte název souboru cloud-init. Následující příklad vytvoří škálovací sadu s názvem *myScaleSet* v *myResourceGroup* a konfiguruje instance virtuálních počítače pomocí souboru s názvem *cloud-init.txt*. Zadejte svá vlastní jména takto:

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
Když jsou k dispozici nové verze operačního systému, můžete použít nebo vytvořit novou vlastní bitovou kopii a [nasadit upgrady operačního systému](virtual-machine-scale-sets-upgrade-scale-set.md) na škálovací sadu. Každá instance virtuálního virtuálního serveru se upgraduje na nejnovější image, kterou zadáte. Vlastní bitovou kopii můžete použít s předinstalovanou aplikací, rozšířením custom script extension nebo PowerShell DSC, aby byla aplikace při upgradu automaticky dostupná. Při provádění tohoto procesu může být nutné naplánovat údržbu aplikace, abyste zajistili, že nedochází k žádným problémům s kompatibilitou verzí.

Pokud používáte vlastní image virtuálního počítače s předinstalovanou aplikací, můžete integrovat aktualizace aplikací s kanálem nasazení k sestavení nových bitových kopií a nasazení upgradů operačního systému v celé škálovací sadě. Tento přístup umožňuje kanálu vyzvednout nejnovější sestavení aplikací, vytvořit a ověřit image virtuálního aplikace a potom upgradovat instance virtuálních aplikací ve škálovací sadě. Chcete-li spustit kanál nasazení, který vytváří a nasazuje aktualizace aplikací napříč vlastními image virtuálních počítače, můžete [vytvořit bitovou kopii Packeru a nasadit pomocí služeb Azure DevOps](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)nebo použít jinou platformu, jako je [Spinnaker](https://www.spinnaker.io/) nebo [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Další kroky
Při vytváření a nasazování aplikací do škálovacích sad můžete zkontrolovat [přehled návrhu škálovací sady](virtual-machine-scale-sets-design-overview.md). Další informace o tom, jak spravovat škálovací sadu, najdete [v tématu Správa škálovací sady pomocí PowerShellu](virtual-machine-scale-sets-windows-manage.md).
