---
title: Seznamte se se šablonami sady škálování virtuálních počítačů
description: Přečtěte si, jak vytvořit šablonu základní sady škálování pro Azure Virtual Machine Scale Sets prostřednictvím několika jednoduchých kroků.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 55c826b4baf38732684aaa0465aeaab6a45564db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87831494"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Seznamte se se šablonami sady škálování virtuálních počítačů
[Šablony Azure Resource Manageru](../azure-resource-manager/templates/overview.md#template-deployment-process) nabízí skvělou možnost pro nasazení skupin souvisejících prostředků. V této sérii kurzů se dozvíte, jak vytvořit šablonu základní sady škálování a jak tuto šablonu upravit tak, aby vyhovovala různým scénářům. Všechny příklady pocházejí z tohoto [úložiště GitHubu](https://github.com/gatneil/mvss).

Tato šablona je určená pro jednoduché. Další kompletní příklady šablon sady škálování najdete v [úložišti GitHub šablon pro Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates) a hledání složek, které obsahují řetězec `vmss` .

Pokud jste už obeznámení s vytvářením šablon, můžete přejít k části Další kroky a podívat se, jak tuto šablonu upravit.

## <a name="define-schema-and-contentversion"></a>Definování $schema a Contentversion –
Nejprve definujte `$schema` a `contentVersion` v šabloně. `$schema`Element definuje verzi jazyka šablony a používá se pro zvýrazňování syntaxe sady Visual Studio a podobné funkce ověřování. `contentVersion`Element se nepoužívá v Azure. Místo toho vám pomůže udržet si přehled o verzi šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definování parametrů
Dále definujte dva parametry `adminUsername` a `adminPassword` . Parametry jsou hodnoty, které zadáte v době nasazování. `adminUsername`Parametr je prostě `string` typ, ale protože `adminPassword` je tajný klíč, poskytněte mu typ `securestring` . Později tyto parametry jsou předány do konfigurace sady škálování.

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
Šablony Správce prostředků také umožňují definovat proměnné, které se použijí později v šabloně. Příklad nepoužívá žádné proměnné, takže objekt JSON je prázdný.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definování prostředků
Dále je oddíl Resources v šabloně. Tady můžete definovat, co skutečně chcete nasadit. Na rozdíl od `parameters` a `variables` (které jsou objekty JSON), `resources` je seznam JSON objektů JSON.

```json
   "resources": [
```

Všechny prostředky vyžadují `type` `name` vlastnosti,, a `apiVersion` `location` . Prvním prostředkem tohoto příkladu je typ [Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), name `myVnet` a apiVersion `2018-11-01` . (Pokud chcete najít nejnovější verzi rozhraní API pro typ prostředku, přečtěte si [referenční informace o šabloně Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Zadat umístění
Pokud chcete zadat umístění pro virtuální síť, použijte [funkci správce prostředků šablony](../azure-resource-manager/templates/template-functions.md). Tato funkce musí být uzavřena do uvozovek a hranatých závorek takto: `"[<template-function>]"` . V takovém případě použijte `resourceGroup` funkci. Trvá to bez argumentů a vrátí objekt JSON s metadaty týkající se skupiny prostředků, do které se nasazení nasazuje. Skupina prostředků je nastavená uživatelem v době nasazení. Tato hodnota je pak indexována do tohoto objektu JSON s `.location` cílem získat umístění z objektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Zadat vlastnosti virtuální sítě
Každý Správce prostředků prostředek má vlastní `properties` oddíl pro konfigurace specifické pro daný prostředek. V takovém případě určete, že má virtuální síť mít jednu podsíť s použitím rozsahu privátních IP adres `10.0.0.0/16` . Sada škálování je vždy obsažena v jedné podsíti. Nemůže zabírat podsítě.

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
Kromě požadovaných `type` `name` vlastností,, `apiVersion` a `location` mohou mít jednotlivé prostředky volitelný `dependsOn` seznam řetězců. Tento seznam určuje, které další prostředky z tohoto nasazení musí být před nasazením tohoto prostředku dokončeny.

V tomto případě existuje pouze jeden prvek v seznamu, virtuální síť z předchozího příkladu. Tuto závislost zadáte, protože sada škálování vyžaduje, aby síť existovala před vytvořením virtuálních počítačů. Díky tomu může sada škálování dát těmto virtuálním počítačům privátní IP adresy z rozsahu IP adres, který jste dříve zadali ve vlastnostech sítě. Formát každého řetězce v seznamu dependsOn je `<type>/<name>` . Použijte stejné `type` a `name` použité dřív v definici prostředků virtuální sítě.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Zadat vlastnosti sady škálování
Sady škálování mají mnoho vlastností pro přizpůsobení virtuálních počítačů v sadě škálování. Úplný seznam těchto vlastností najdete v [referenčních informacích k šabloně](/azure/templates/microsoft.compute/virtualmachinescalesets). V tomto kurzu se nastaví jenom několik běžně používaných vlastností.
### <a name="supply-vm-size-and-capacity"></a>Zadejte velikost a kapacitu virtuálního počítače.
Sada škálování musí znát, jakou velikost virtuálního počítače se má vytvořit ("název SKU") a kolik takových virtuálních počítačů se má vytvořit ("kapacita SKU"). Pokud chcete zjistit, jaké velikosti virtuálních počítačů jsou k dispozici, přečtěte si [dokumentaci velikosti virtuálních počítačů](../virtual-machines/sizes.md).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Zvolit typ aktualizací
Sada škálování taky potřebuje informace o tom, jak zpracovávat aktualizace v sadě škálování. V současné době existují tři možnosti, `Manual` `Rolling` a `Automatic` . Další informace o rozdílech mezi těmito dvěma postupy najdete v dokumentaci k [upgradu sady škálování](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Výběr operačního systému virtuálního počítače
Sada škálování musí znát operační systém, který se má umístit na virtuální počítače. Tady vytvořte virtuální počítače s plně opravenou imagí Ubuntu 16,04-LTS.

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

### <a name="specify-computernameprefix"></a>Zadat computerNamePrefix
Sada škálování nasadí několik virtuálních počítačů. Místo zadání názvu každého virtuálního počítače zadejte `computerNamePrefix` . Sada škálování připojí index k předponě pro každý virtuální počítač, takže názvy virtuálních počítačů mají tvar `<computerNamePrefix>_<auto-generated-index>` .

V následujícím fragmentu kódu použijte parametry z části před a nastavte uživatelské jméno a heslo správce pro všechny virtuální počítače v sadě škálování. Tento proces používá `parameters` funkci šablony. Tato funkce přebírá řetězec, který určuje, který parametr se má odkazovat na, a výstup hodnoty pro tento parametr.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Zadat konfiguraci sítě virtuálních počítačů
Nakonec zadejte konfiguraci sítě pro virtuální počítače v sadě škálování. V takovém případě stačí zadat jenom ID podsítě, kterou jste vytvořili dříve. Tato operace oznamuje, že sada škálování nastavuje síťová rozhraní v této podsíti.

ID virtuální sítě obsahující podsíť můžete získat pomocí `resourceId` funkce šablony. Tato funkce přebírá typ a název prostředku a vrací plně kvalifikovaný identifikátor tohoto prostředku. Toto ID má podobu: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifikátor virtuální sítě ale není dostatečný. Zadejte konkrétní podsíť, ve které by se měly virtuální počítače sady škálování nacházet. Provedete to zřetězením `/subnets/mySubnet` s identifikátorem virtuální sítě. Výsledkem je plně kvalifikované ID podsítě. Udělejte to zřetězení `concat` funkcí, která přebírá v řadě řetězců a vrací jejich zřetězení.

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