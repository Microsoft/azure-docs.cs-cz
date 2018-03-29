---
title: Vytvoření škálovací sady virtuálních počítačů pomocí šablony Azure | Dokumentace Microsoftu
description: Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů pomocí šablony Azure Resource Manageru.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: fc5c4166464ec43833bd49ac68fa52e8da8892e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Vytvoření škálovací sady virtuálních počítačů pomocí Azure CLI 2.0
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto úvodním článku vytvoříte škálovací sadu virtuálních počítačů pomocí šablony Azure Resource Manageru. Škálovací sadu můžete vytvořit také pomocí [Azure CLI 2.0](quick-create-cli.md), [Azure PowerShellu](quick-create-powershell.md) nebo webu [Azure Portal](quick-create-portal.md).


## <a name="overview-of-templates"></a>Přehled šablon
Šablony Azure Resource Manageru umožňují nasazení skupin souvisejících prostředků. Šablony se píší ve formátu JavaScript Object Notation (JSON) a definují celé prostředí infrastruktury Azure pro vaši aplikaci. V jediné šabloně můžete vytvořit škálovací sadu virtuálních počítačů, nainstalovat aplikace a nakonfigurovat pravidla automatického škálování. Díky použití proměnných a parametrů se může tato šablona použít opakovaně k aktualizaci stávajících nebo vytvoření dalších škálovacích sad. Šablony můžete nasadit prostřednictvím webu Azure Portal, Azure CLI 2.0 nebo Azure PowerShellu a můžete je také volat z kanálů průběžné integrace nebo průběžného doručování (CI/CD).

Další informace o šablonách najdete v tématu [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).


## <a name="define-a-scale-set"></a>Definice škálovací sady
Šablona definuje konfiguraci pro každý typ prostředků. Typ prostředku škálovací sady virtuálních počítačů je podobný samostatnému virtuálnímu počítači. Hlavní části typu prostředku škálovací sady virtuálních počítačů jsou následující:

| Vlastnost                     | Popis vlastnosti                                  | Příklad hodnoty v šabloně                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Typ prostředku Azure, který se má vytvořit                            | Microsoft.Compute/virtualMachineScaleSets |
| jméno                         | Název škálovací sady                                       | myScaleSet                                |
| location                     | Umístění, ve kterém se škálovací sada vytvoří                     | Východ USA                                   |
| sku.name                     | Velikost virtuálního počítače pro všechny instance škálovací sady                  | Standard_A1                               |
| sku.capacity                 | Počet instancí virtuálních počítačů, které se mají zpočátku vytvořit           | 2                                         |
| upgradePolicy.mode           | Režim upgradů instance virtuálního počítače, kdy dochází ke změnám              | Automaticky                                 |
| imageReference               | Image platformy nebo vlastní image, která se použije pro instance virtuálních počítačů | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Předpona názvu všech instancí virtuálních počítačů                     | myvmss                                    |
| osProfile.adminUsername      | Uživatelské jméno pro všechny instance virtuálních počítačů                        | azureuser                                 |
| osProfile.adminPassword      | Heslo pro všechny instance virtuálních počítačů                        | P@ssw0rd!                                 |

 Následující fragment kódu ukazuje jádro definice prostředku škálovací sady v šabloně. Pro zkrácení ukázky není zobrazená konfigurace virtuální síťové karty. Pokud chcete šablonu škálovací sady upravit, můžete změnit velikost virtuálního počítače nebo počáteční kapacitu nebo použít jinou platformu nebo vlastní image.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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


## <a name="install-an-application"></a>Instalace aplikace
Po nasazení škálovací sady můžou rozšíření virtuálního počítače zajistit konfiguraci po nasazení a úlohy automatizace, jako je instalace aplikace. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Pokud chcete pro svou škálovací sadu použít rozšíření, do předchozího příkladu prostředku přidáte část *extensionProfile*. Profil rozšíření obvykle definuje následující vlastnosti:

- Typ rozšíření
- Vydavatel rozšíření
- Verze rozšíření
- Umístění konfiguračních nebo instalačních skriptů
- Příkazy, které se mají spustit na instancích virtuálních počítačů

Podívejme se na dva způsoby, jak nainstalovat aplikaci s využitím rozšíření – pomocí rozšíření vlastních skriptů můžete nainstalovat aplikaci Pythonu v Linuxu nebo pomocí rozšíření PowerShell DSC můžete nainstalovat aplikaci ASP.NET ve Windows.

### <a name="python-http-server-on-linux"></a>HTTP server s Pythonem v Linuxu
[HTTP server s Pythonem v Linuxu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) používá rozšíření vlastních skriptů k instalaci [Bottle](http://bottlepy.org/docs/dev/), webového rozhraní založeného na jazyce Python, a jednoduchého HTTP serveru. 

Tyto dva skripty jsou definované ve vlastnosti *fileUris* - *installserver.sh* a *workserver.py*. Tyto soubory se stáhnou z GitHubu a pak se spustí příkaz `bash installserver.sh` ve vlastnosti *commandToExecute*, který nainstaluje a nakonfiguruje aplikaci:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>Aplikace ASP.NET ve Windows
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
Nejjednodušší způsob, jak nasadit šablonu [HTTP server s Pythonem v Linuxu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) nebo [Aplikace ASP.NET MVC ve Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale), je pomocí tlačítka **Nasazení do Azure**, které najdete na GitHubu v souborech Readme.  Ukázkové šablony můžete nasadit také pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Azure CLI 2.0 můžete použít k instalaci HTTP serveru s Pythonem v Linuxu následujícím způsobem:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Pokud chcete vidět svou aplikaci v akci, získejte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show), jak je znázorněno níže:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče ve formátu *http://<publicIpAddress>:9000/do_work*. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Výchozí webová stránka na serveru NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell můžete použít k instalaci aplikace ASP.NET ve Windows následujícím způsobem:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Pokud chcete vidět svou aplikaci v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), jak je znázorněno níže:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče ve formátu *http://<publicIpAddress>/MyApp*. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Spuštění webu služby IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete), jak je znázorněno níže:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky
