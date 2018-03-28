---
title: "Vytvoření škálovací sady virtuálních počítačů s Windows pomocí šablony Azure | Dokumentace Microsoftu"
description: "Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů s Windows pomocí šablony Azure Resource Manageru, která nasadí ukázkovou aplikaci a nakonfiguruje pravidla automatického škálování."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Vytvoření škálovací sady virtuálních počítačů s Windows pomocí šablony Azure
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto článku Rychlý start vytvoříte škálovací sadu virtuálních počítačů pomocí šablony Azure Resource Manageru. Škálovací sadu můžete vytvořit také pomocí [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShellu](virtual-machine-scale-sets-create-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 4.4.1 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="define-a-scale-set-in-a-template"></a>Definice škálovací sady v šabloně
Šablony Azure Resource Manageru umožňují nasazení skupin souvisejících prostředků. Šablony se píší ve formátu JavaScript Object Notation (JSON) a definují celé prostředí infrastruktury Azure pro vaši aplikaci. V jediné šabloně můžete vytvořit škálovací sadu virtuálních počítačů, nainstalovat aplikace a nakonfigurovat pravidla automatického škálování. Díky použití proměnných a parametrů se může tato šablona použít opakovaně k aktualizaci stávajících nebo vytvoření dalších škálovacích sad. Šablony můžete nasadit prostřednictvím webu Azure Portal, Azure CLI 2.0 nebo Azure PowerShell nebo z kanálů průběžné integrace nebo průběžného doručování (CI/CD).

Další informace o šablonách najdete v tématu [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Šablona definuje konfiguraci pro každý typ prostředků. Typ prostředku škálovací sady virtuálních počítačů je podobný samostatnému virtuálnímu počítači. Hlavní části typu prostředku škálovací sady virtuálních počítačů jsou následující:

| Vlastnost                     | Popis vlastnosti                                  | Příklad hodnoty v šabloně                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Typ prostředku Azure, který se má vytvořit                            | Microsoft.Compute/virtualMachineScaleSets |
| jméno                         | Název škálovací sady                                       | myScaleSet                                |
| location                     | Umístění, ve kterém se škálovací sada vytvoří                     | Východ USA                                   |
| sku.name                     | Velikost virtuálního počítače pro všechny instance škálovací sady                  | Standard_A1                               |
| sku.capacity                 | Počet instancí virtuálních počítačů, které se mají zpočátku vytvořit           | 2                                         |
| upgradePolicy.mode           | Režim upgradů instance virtuálního počítače, kdy dochází ke změnám              | Automaticky                                 |
| imageReference               | Image platformy nebo vlastní image, která se použije pro instance virtuálních počítačů | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Předpona názvu všech instancí virtuálních počítačů                     | myvmss                                    |
| osProfile.adminUsername      | Uživatelské jméno pro všechny instance virtuálních počítačů                        | azureuser                                 |
| osProfile.adminPassword      | Heslo pro všechny instance virtuálních počítačů                        | P@ssw0rd!                                 |

 Následující příklad ukazuje jádro definice prostředku škálovací sady. Pokud chcete šablonu škálovací sady upravit, můžete změnit velikost virtuálního počítače nebo počáteční kapacitu nebo použít jinou platformu nebo vlastní image.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Pro zkrácení ukázky není zobrazená konfigurace virtuální síťové karty. Další komponenty, například nástroj pro vyrovnávání zatížení, se také nezobrazují. Kompletní šablonu škálovací sady najdete [na konci tohoto článku](#deploy-the-template).


## <a name="install-an-application"></a>Instalace aplikace
Po nasazení škálovací sady můžou rozšíření virtuálního počítače zajistit konfiguraci po nasazení a úlohy automatizace, jako je instalace aplikace. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Pokud chcete pro svou škálovací sadu použít rozšíření, do předchozího příkladu prostředku přidáte část *extensionProfile*. Profil rozšíření obvykle definuje následující vlastnosti:

- Typ rozšíření
- Vydavatel rozšíření
- Verze rozšíření
- Umístění konfiguračních nebo instalačních skriptů
- Příkazy, které se mají spustit na instancích virtuálních počítačů

Ukázková šablona [Aplikace ASP.NET ve Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) používá rozšíření PowerShell DSC k instalaci aplikace ASP.NET MVC, která spouští službu IIS. 

Instalační skript se stáhne z GitHubu, jak je definováno ve vlastnosti *url*. Rozšíření pak spustí funkci *InstallIIS* ze skriptu *IISInstall.ps1*, jak je definováno ve vlastnostech *function* a *script*. Samotná aplikace ASP.NET je zadána jako balíček Nasazení webu, který se také stáhne z GitHubu, jak je definováno ve vlastnosti *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Nasazení šablony
Šablonu [Aplikace ASP.NET MVC ve Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) můžete nasadit pomocí následujícího tlačítka **Nasadit do Azure**. Toto tlačítko otevře Azure Portal, načte kompletní šablonu a zobrazí výzvu k zadání několika parametrů, jako jsou název škálovací sady, počet instancí a přihlašovací údaje správce.

[![Nasazení šablony do Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

K instalaci aplikace ASP.NET ve Windows můžete použít také Azure PowerShell s rutinou [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment), jak je znázorněno níže:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Do zobrazených výzev zadejte název škálovací sady a přihlašovací údaje správce pro instance virtuálních počítačů. Vytvoření škálovací sady a použití rozšíření ke konfiguraci aplikace může trvat 10 až 15 minut.


## <a name="test-your-sample-application"></a>Otestování ukázkové aplikace
Pokud chcete vidět svou aplikaci v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), jak je znázorněno níže:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče ve formátu *http://veřejná_IP_adresa/MyApp*. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Spuštění webu služby IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), jak je znázorněno níže:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>Další kroky
V tomto článku Rychlý start jste pomocí šablony Azure vytvořili škálovací sadu s Windows a pomocí rozšíření PowerShell DSC jste na instance virtuálních počítačů nainstalovali základní aplikaci ASP.NET. Pokud chcete zlepšit škálovatelnost a automatizaci, rozšiřte svou škálovací sadu pomocí některého z těchto článků s postupy:

- [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md)
- Automatické škálování pomocí [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShellu](virtual-machine-scale-sets-autoscale-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Použití automatických upgradů operačního systému pro instance virtuálních počítačů ve škálovací sadě](virtual-machine-scale-sets-automatic-upgrade.md)
