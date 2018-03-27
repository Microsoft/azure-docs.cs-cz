---
title: "Vytvoření škálovací sady virtuálních počítačů s Linuxem pomocí šablony Azure | Dokumentace Microsoftu"
description: "Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů s Linuxem pomocí šablony Azure Resource Manageru, která nasadí ukázkovou aplikaci a nakonfiguruje pravidla automatického škálování."
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
ms.openlocfilehash: 16e9c0b30710d711ef2789f7781b17e72889d4da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Vytvoření škálovací sady virtuálních počítačů s Linuxem pomocí šablony Azure
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto článku Rychlý start vytvoříte škálovací sadu virtuálních počítačů s Linuxem pomocí šablony Azure Resource Manageru. Škálovací sadu můžete vytvořit také pomocí [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShellu](virtual-machine-scale-sets-create-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI ve verzi 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="define-a-scale-set-in-a-template"></a>Definice škálovací sady v šabloně
Šablony Azure Resource Manageru umožňují nasazení skupin souvisejících prostředků. Šablony se píší ve formátu JavaScript Object Notation (JSON) a definují celé prostředí infrastruktury Azure pro vaši aplikaci. V jediné šabloně můžete vytvořit škálovací sadu virtuálních počítačů, nainstalovat aplikace a nakonfigurovat pravidla automatického škálování. Díky použití proměnných a parametrů se může tato šablona použít opakovaně k aktualizaci stávajících nebo vytvoření dalších škálovacích sad. Šablony můžete nasadit prostřednictvím webu Azure Portal, Azure CLI 2.0 nebo Azure PowerShell nebo z kanálů průběžné integrace nebo průběžného doručování (CI/CD).

Další informace o šablonách najdete v tématu [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Pokud chcete vytvořit škálovací sadu pomocí šablony, nadefinujete odpovídající prostředky. Hlavní části typu prostředku škálovací sady virtuálních počítačů jsou následující:

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

 Pro zkrácení ukázky není zobrazená konfigurace virtuální síťové karty. Další komponenty, například nástroj pro vyrovnávání zatížení, se také nezobrazují. Kompletní šablonu škálovací sady najdete [na konci tohoto článku](#deploy-the-template).


## <a name="install-an-application"></a>Instalace aplikace
Po nasazení škálovací sady můžou rozšíření virtuálního počítače zajistit konfiguraci po nasazení a úlohy automatizace, jako je instalace aplikace. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal. Pokud chcete pro svou škálovací sadu použít rozšíření, do předchozího příkladu prostředku přidáte část *extensionProfile*. Profil rozšíření obvykle definuje následující vlastnosti:

- Typ rozšíření
- Vydavatel rozšíření
- Verze rozšíření
- Umístění konfiguračních nebo instalačních skriptů
- Příkazy, které se mají spustit na instancích virtuálních počítačů

Šablona [HTTP server s Pythonem v Linuxu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) používá rozšíření vlastních skriptů k instalaci [Bottle](http://bottlepy.org/docs/dev/), webového rozhraní založeného na jazyce Python, a jednoduchého HTTP serveru. 

Tyto dva skripty jsou definované ve vlastnosti **fileUris** - *installserver.sh* a *workserver.py*. Tyto soubory se stáhnou z GitHubu a pak se spustí příkaz *commandToExecute* ve vlastnosti `bash installserver.sh`, který nainstaluje a nakonfiguruje aplikaci:

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


## <a name="deploy-the-template"></a>Nasazení šablony
Šablonu [HTTP server s Pythonem v Linuxu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) můžete nasadit pomocí následujícího tlačítka **Nasadit do Azure**. Toto tlačítko otevře Azure Portal, načte kompletní šablonu a zobrazí výzvu k zadání několika parametrů, jako jsou název škálovací sady, počet instancí a přihlašovací údaje správce.

[![Nasazení šablony do Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

K instalaci HTTP serveru s Pythonem v Linuxu můžete použít také Azure CLI 2.0 s příkazem [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create), jak je znázorněno níže:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Do zobrazených výzev zadejte název škálovací sady, počet instancí a přihlašovací údaje správce pro instance virtuálních počítačů. Vytvoření škálovací sady a podpůrných prostředků trvá několik minut.


## <a name="test-your-sample-application"></a>Otestování ukázkové aplikace
Pokud chcete vidět svou aplikaci v akci, získejte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show), jak je znázorněno níže:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče ve formátu *http://veřejná_IP_adresa:9000/do_work*. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Výchozí webová stránka na serveru NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete), jak je znázorněno níže:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky
V tomto článku Rychlý start jste pomocí šablony Azure vytvořili škálovací sadu s Linuxem a pomocí rozšíření vlastních skriptů jste na instance virtuálních počítačů nainstalovali základní webový server s Pythonem. Pokud chcete zlepšit škálovatelnost a automatizaci, rozšiřte svou škálovací sadu pomocí některého z těchto článků s postupy:

- [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md)
- Automatické škálování pomocí [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShellu](virtual-machine-scale-sets-autoscale-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Použití automatických upgradů operačního systému pro instance virtuálních počítačů ve škálovací sadě](virtual-machine-scale-sets-automatic-upgrade.md)
