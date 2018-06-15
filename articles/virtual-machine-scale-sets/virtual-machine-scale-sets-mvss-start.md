---
title: Další informace o šablonách sady škálování virtuálního počítače | Microsoft Docs
description: Naučte se vytvořit šablonu sady minimální přijatelná škálování pro sady škálování virtuálního počítače
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 5cd495d1332c71d7eae775f933b73d98826f10e4
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781251"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Další informace o šablonách sady škálování virtuálního počítače
[Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) nabízí skvělou možnost pro nasazení skupin souvisejících prostředků. Tato řada kurz ukazuje, jak vytvořit šablonu sady minimální přijatelná škálování a postup úpravy této šablony, aby vyhovovala různé scénáře. Všechny příklady pocházejí z tohoto [úložiště GitHub](https://github.com/gatneil/mvss). 

Tato šablona má být jednoduché. Podrobnější příklady škálování nastavení šablony najdete [úložiště GitHub šablony Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates) a vyhledejte složky, které obsahují řetězec `vmss`.

Pokud jste již obeznámeni s vytváření šablon, můžete přeskočit k části "Další kroky" a zjistit, jak upravit tuto šablonu.

## <a name="review-the-template"></a>Zkontrolujte šablonu

Ke kontrole šablony sady minimální přijatelná škálování, použijte Githubu [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

V tomto kurzu se podíváme diff (`git diff master minimum-viable-scale-set`) k vytvoření minimální přijatelná měřítka nastavit šablonu část podle část.

## <a name="define-schema-and-contentversion"></a>Zadejte $schema a contentVersion
Nejprve definovat `$schema` a `contentVersion` v šabloně. `$schema` Element definuje verze jazyka šablony a používá se pro Visual Studio zvýraznění syntaxe a podobné funkce ověřování. `contentVersion` Element nepoužívá Azure. Místo toho pomáhá udržovat přehled o verzi šablony.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definujte parametry
V dalším kroku definovat dva parametry `adminUsername` a `adminPassword`. Parametry jsou hodnoty, které zadáte v době nasazení. `adminUsername` Parametr je jednoduše `string` typu, ale protože `adminPassword` je tajný, udělte ho zadejte `securestring`. Později tyto parametry se předávají do konfigurace sady škálování.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definování proměnných
Správce prostředků šablony vám také umožní zadat proměnné určené k použít později v šabloně. V příkladu nepoužívá všechny proměnné, takže objekt JSON je prázdný.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definování zdrojů
Dále je oddílu prostředků šablony. Tady můžete definovat, co Opravdu chcete nasadit. Na rozdíl od `parameters` a `variables` (které jsou objekty JSON), `resources` je JSON seznam objektů JSON.

```json
   "resources": [
```

Všechny zdroje vyžadují `type`, `name`, `apiVersion`, a `location` vlastnosti. První prostředek v tomto příkladu má typ `Microsft.Network/virtualNetwork`, název `myVnet`a apiVersion `2016-03-30`. (Nejnovější verzi rozhraní API pro typ prostředku, najdete v tématu [dokumentace k Azure REST API](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Zadejte umístění
Pokud chcete zadat umístění pro virtuální síť, použijte [funkce šablony Resource Manageru](../azure-resource-manager/resource-group-template-functions.md). Tato funkce musí být uzavřena do uvozovek a hranaté závorky takto: `"[<template-function>]"`. V takovém případě použijte `resourceGroup` funkce. Přebírá v bez argumentů a vrátí objekt JSON s metadata o skupině prostředků, které toto nasazení je nasazován na. Skupina prostředků je nastaven uživatelem v době nasazení. Tato hodnota je pak indexovaných do tohoto objektu JSON s `.location` získat umístění z objektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Zadejte vlastnosti virtuální sítě
Každý prostředek, správce prostředků má svou vlastní `properties` v sekci konfigurace, které jsou specifické pro daný prostředek. V takovém případě zadat, že virtuální síť musí mít jednu podsíť pomocí rozsah privátních IP adres `10.0.0.0/16`. Sada škálování je vždy obsažené v jedné podsíti. Ho nemůže span podsítě.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Přidat seznam dependsOn
Kromě požadovaných `type`, `name`, `apiVersion`, a `location` vlastnosti každého prostředku může mít volitelný `dependsOn` seznamu řetězců. Tento seznam určuje, které další prostředky z tohoto nasazení musí dokončit před nasazením tohoto prostředku.

V seznamu, virtuální sítě z předchozího příkladu je v tomto případě pouze jeden element. Zadáte tuto závislost, protože sada měřítka musí síti existovat před vytvořením všechny virtuální počítače. Tímto způsobem, byly sadou škálování může poskytnout tyto virtuální počítače privátních IP adres z rozsahu IP adres dříve zadán ve vlastnostech síťového. Formát každý řetězec v seznamu dependsOn je `<type>/<name>`. Použijte stejný `type` a `name` použít dříve v definici prostředků virtuální sítě.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Zadejte vlastnosti sady škálování
Sady škálování mít mnoho vlastností pro přizpůsobení virtuálních počítačů v sadě škálování. Úplný seznam těchto vlastností najdete v tématu [škálovací sady dokumentace k REST API](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). U tohoto kurzu jen několik vlastností tak často používaná nastavení.
### <a name="supply-vm-size-and-capacity"></a>Zadejte velikost virtuálního počítače a kapacity
Měřítko nastavit musí vědět, jakou velikost virtuálního počítače k vytvoření ("název sku") a kolik tyto virtuální počítače na vytvoření ("kapacita sku"). Informace, které velikosti virtuálních počítačů jsou k dispozici, najdete v tématu [velikosti virtuálních počítačů dokumentaci](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Vyberte typ aktualizace
Také sad škálování je potřeba vědět, jak pro zpracování aktualizací v sadě škálování. V současné době existují dvě možnosti, `Manual` a `Automatic`. Další informace o rozdílech mezi nimi, najdete v dokumentaci na [jak upgradovat škálovací sadu](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Vyberte operační systém virtuálního počítače
Měřítko nastavit musí vědět, jaké operační systém se umístí na virtuálních počítačích. Zde vytvořte virtuální počítače s bitovou kopii plně opravou 16.04 LTS Ubuntu.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Zadejte computerNamePrefix
Sada škálování nasadí víc virtuálních počítačů. Místo zadání každý název virtuálního počítače, zadejte `computerNamePrefix`. Škálovací sadu připojí index k předponu pro každý virtuální počítač, aby názvy virtuálních počítačů formuláře `<computerNamePrefix>_<auto-generated-index>`.

V následující fragment kódu použijte k nastavení správce uživatelské jméno a heslo pro všechny virtuální počítače ve škálovací sadě parametrů z před. Tento proces používá `parameters` funkce šablony. Tato funkce přebírá řetězec, který určuje, které parametr, který bude odkazovat na a výstupy hodnota tohoto parametru.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Zadejte konfigurace sítě virtuálních počítačů
Nakonec zadejte konfigurace sítě pro virtuální počítače v sadě škálování. V takovém případě stačí zadat ID podsítě vytvořili dříve. Tato hodnota informuje měřítka nastavit uvést rozhraní sítě v této podsíti.

Můžete získat ID virtuální sítě obsahující podsíť pomocí `resourceId` funkce šablony. Tato funkce přebírá typu a název prostředku a vrátí plně kvalifikovaný identifikátor prostředku. Toto ID má tvar:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Však identifikátor virtuální sítě není dostatečná. Zadejte konkrétní podsítě, měřítka nastavit virtuální počítače musí být v. K tomuto účelu řetězení `/subnets/mySubnet` ID virtuální sítě. Výsledkem je plně kvalifikovaný ID podsítě. Proveďte toto zřetězení s `concat` funkce, která přebírá v řadě řetězce a vrátí jejich zřetězení.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
