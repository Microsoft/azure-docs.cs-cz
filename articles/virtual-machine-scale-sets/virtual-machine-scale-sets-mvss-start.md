---
title: Další informace o šablonách škálovacích sad virtuálních počítačů | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit šablonu minimální přijatelné škálovací sady pro škálovací sady virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: 29c0a1a15db7670d83ff384a1ba0f37499389ef7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741875"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Další informace o šablonách škálovacích sad virtuálních počítačů
[Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) nabízí skvělou možnost pro nasazení skupin souvisejících prostředků. V této sérii kurzů ukazuje, jak vytvořit šablonu minimální přijatelné škálovací sady a jak upravit tuto šablonu tak, aby odpovídala různé scénáře. Všechny příklady pocházejí z tohoto [úložiště GitHub](https://github.com/gatneil/mvss). 

Tato šablona je určena být jednoduché. Kompletní příklady škálovací sady šablon naleznete v tématu [úložišti šablon Azure Quickstart na Githubu](https://github.com/Azure/azure-quickstart-templates) a vyhledejte složky, které obsahují řetězec `vmss`.

Pokud jste již obeznámeni s vytvořením šablony, můžete přeskočit k části "Další kroky" a uvidíte, jak upravit tuto šablonu.

## <a name="review-the-template"></a>Zkontrolujte šablonu

Zkontrolujte šablonu minimální přijatelné škálovací sady, můžete využít GitHub [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

V tomto kurzu, Podívejme se na rozdíly (`git diff master minimum-viable-scale-set`) vytvoření minimální přijatelné škálovací sady šablon jeden po druhém.

## <a name="define-schema-and-contentversion"></a>Definování $schema a contentversion –
Nejprve definujte `$schema` a `contentVersion` v šabloně. `$schema` Element definuje verze jazyka šablony a používá se pro Visual Studio zvýraznění syntaxe a podobné funkce ověřování. `contentVersion` Není použit element v Azure. Místo toho pomáhá udržovat přehled o verzi šablony.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definovat parametry
Dále definujte dva parametry, `adminUsername` a `adminPassword`. Parametry jsou hodnoty, které zadáte v době nasazování. `adminUsername` Parametr se používá jednoduchý `string` typu, ale protože `adminPassword` je tajný, uveďte ji zadejte `securestring`. Později tyto parametry jsou předány do konfigurace škálovací sady.

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
Šablony Resource Manageru vám také umožní definujte proměnné, které se použije později v šabloně. V příkladu nepoužívá žádné proměnné objektu JSON je prázdný.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definování prostředků
Dále je část zdroje šablony. Tady můžete definovat, co vlastně chcete nasadit. Na rozdíl od `parameters` a `variables` (které jsou objekty JSON), `resources` je seznam objektů JSON JSON.

```json
   "resources": [
```

Všechny prostředky vyžadují `type`, `name`, `apiVersion`, a `location` vlastnosti. První prostředek v tomto příkladu má typ `Microsft.Network/virtualNetwork`a názvem `myVnet`a verze rozhraní API `2016-03-30`. (Nejnovější verze rozhraní API pro typ prostředku, najdete v tématu [dokumentace k rozhraní REST API služby Azure](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Zadejte umístění
Chcete-li zadat umístění pro virtuální síť, použijte [funkce šablony Resource Manageru](../azure-resource-manager/resource-group-template-functions.md). Tato funkce musí být uzavřen v uvozovkách a hranaté závorky takto: `"[<template-function>]"`. V takovém případě použijte `resourceGroup` funkce. Přijímá bez argumentů a vrátí objekt JSON s metadata, které toto nasazení se nasazuje do skupiny prostředků. Skupina prostředků je nastavil uživatel v době nasazování. Tato hodnota se pak indexovaných do tohoto objektu JSON s `.location` chcete zjistit polohu z objektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Zadejte vlastnosti virtuální sítě
Jednotlivé prostředky Resource Manageru má svoji vlastní `properties` části konfigurace specifické pro prostředek. V takovém případě zadejte, že virtuální síť by měla mít jednu podsíť s použitím rozsah privátních IP adres `10.0.0.0/16`. Škálovací sada je vždy součástí jedné podsítě. Nelze pracovat nad podsítě.

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
Kromě požadovaných `type`, `name`, `apiVersion`, a `location` vlastnosti, každý prostředek může mít volitelně `dependsOn` seznam řetězců. Tento seznam určuje, které další prostředky z tohoto nasazení musíte dokončit před nasazením tento prostředek.

V seznamu, virtuální sítě z předchozího příkladu je v tomto případě pouze jeden element. Můžete zadat tuto závislost, protože škálovací sada musí existovat před vytvořením virtuálních síti. Tímto způsobem škálovací sadu můžete poskytnout tyto virtuální počítače s privátní IP adresy z rozsahu IP adres dříve zadanou ve vlastnostech síťového. Formát každý řetězec v seznamu dependsOn `<type>/<name>`. Použijte stejný `type` a `name` použili v definici prostředků virtuální sítě.

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
## <a name="specify-scale-set-properties"></a>Zadejte vlastnosti škálovací sady
Škálovací sady mají mnoho vlastností pro přizpůsobení virtuálních počítačů ve škálovací sadě. Úplný seznam těchto vlastností najdete v tématu [škálovací sada dokumentace k rozhraní REST API](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). Pro účely tohoto kurzu jsou nastavené pouze několik běžně používaných vlastností.
### <a name="supply-vm-size-and-capacity"></a>Zadejte velikost virtuálního počítače a kapacita
Škálovací sada je potřeba vědět, jaké velikosti virtuálního počítače na vytvoření ("název sku") a kolik takových virtuálních počítačů k vytvoření ("kapacita skladové položky"). Chcete-li zjistit, jaké velikosti virtuálních počítačů jsou k dispozici, najdete v článku [dokumentaci velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Zvolte typ aktualizace
Škálovací sady také je potřeba vědět, jak se pro zpracování aktualizací ve škálovací sadě. V současné době existují dvě možnosti, `Manual` a `Automatic`. Další informace o rozdílech mezi těmito dvěma, naleznete v dokumentaci na [postup upgradu škálovací sady](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Vyberte operační systém virtuálního počítače
Škálovací sada je potřeba vědět, jaký operační systém umístit na virtuálních počítačích. Tady vytvořte virtuální počítače s plně opravené image Ubuntu 16.04 LTS.

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
Škálovací sada nasadíte několik virtuálních počítačů. Místo určení každý název virtuálního počítače, zadejte `computerNamePrefix`. Škálovací sady přidá indexu předponu pro každý virtuální počítač, tak názvy virtuálních počítačů mít formát _služba ._protokol `<computerNamePrefix>_<auto-generated-index>`.

Následující fragment kódu použijte parametry z před nastavte uživatelské jméno správce a heslo pro všechny virtuální počítače ve škálovací sadě. Tento proces používá `parameters` šablony funkce. Tato funkce přebírá řetězec, který určuje parametr, který neodkazuje na a uloží hodnotu tohoto parametru.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Zadejte konfigurace sítě virtuálních počítačů
Nakonec zadejte příslušnou konfiguraci sítě pro virtuální počítače ve škálovací sadě. V takovém případě stačí zadat ID podsítě vytvořili dříve. To říká škálovací sady do síťových rozhraní v této podsíti.

Můžete získat ID virtuální sítě podsíť obsahující pomocí `resourceId` šablony funkce. Tato funkce přijímá typ a název prostředku a vrátí úplný identifikátor prostředku. Toto ID má formát: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Ale identifikátor virtuální sítě není dostatečná. Zadejte konkrétní podsítě, škálovací sady virtuálních počítačů musí být v. K tomuto účelu zřetězit `/subnets/mySubnet` ID virtuální sítě. Výsledkem je plně kvalifikované ID podsítě. Proveďte toto zřetězení s `concat` funkci, která přebírá řadu řetězce a vrátí jejich zřetězení.

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

## <a name="next-steps"></a>Další postup

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
