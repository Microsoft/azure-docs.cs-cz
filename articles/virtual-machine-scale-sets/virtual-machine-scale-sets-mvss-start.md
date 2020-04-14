---
title: Informace o šablonách škálovacích sad virtuálních strojů
description: Zjistěte, jak vytvořit základní šablonu škálovací sady pro škálovací sady virtuálních strojů Azure pomocí několika jednoduchých kroků.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 9c6a30a5f08b33adfa515973962236516f34fbf3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273388"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Informace o šablonách škálovacích sad virtuálních strojů
[Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) nabízí skvělou možnost pro nasazení skupin souvisejících prostředků. Tato série kurzů ukazuje, jak vytvořit základní šablonu škálovací sady a jak upravit tuto šablonu tak, aby vyhovovala různým scénářům. Všechny příklady pocházejí z tohoto [úložiště GitHub](https://github.com/gatneil/mvss).

Tato šablona je určena k jednoduchému. Podrobnější příklady šablon škálovacísady najdete v [úložišti GitHub v Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates) a vyhledejte složky, které obsahují řetězec `vmss`.

Pokud jste již obeznámeni s vytvářením šablon, můžete přeskočit na část Další kroky a zjistit, jak tuto šablonu upravit.

## <a name="define-schema-and-contentversion"></a>Definování $schema a contentVersion
Nejprve `$schema` definujte a `contentVersion` v šabloně. Prvek `$schema` definuje verzi jazyka šablony a používá se pro zvýraznění syntaxe sady Visual Studio a podobné funkce ověření. Prvek `contentVersion` není používán Azure. Místo toho vám pomůže sledovat verzi šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definování parametrů
Dále definujte dva `adminUsername` parametry a `adminPassword`. Parametry jsou hodnoty, které zadáte v době nasazení. Parametr `adminUsername` je jednoduše `string` typ, `adminPassword` ale protože je tajný, dát mu typ `securestring`. Později jsou tyto parametry předány do konfigurace škálovací sady.

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
Šablony Správce prostředků také umožňují definovat proměnné, které budou použity později v šabloně. Příklad nepoužívá žádné proměnné, takže objekt JSON je prázdný.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definování prostředků
Dále je část zdrojů šablony. Zde můžete definovat, co vlastně chcete nasadit. Na `parameters` `variables` rozdíl od a (které `resources` jsou Objekty JSON), je json seznam JSON objektů.

```json
   "resources": [
```

Všechny prostředky `type` `name`vyžadují `apiVersion`, `location` , a vlastnosti. První prostředek tohoto příkladu má typ [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), name `myVnet`a apiVersion `2018-11-01`. (Pokud chcete najít nejnovější verzi rozhraní API pro typ prostředku, podívejte se na [odkaz na šablonu Správce prostředků Azure](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Určení umístění
Chcete-li určit umístění virtuální sítě, použijte [funkci šablony Správce prostředků](../azure-resource-manager/templates/template-functions.md). Tato funkce musí být uzavřena v uvozovkách a hranatých závorkách takto: `"[<template-function>]"`. V tomto případě `resourceGroup` použijte funkci. Trvá žádné argumenty a vrátí objekt JSON s metadaty o skupině prostředků, do které se toto nasazení nasazuje. Skupina prostředků je nastavena uživatelem v době nasazení. Tato hodnota je pak indexována do `.location` tohoto objektu JSON, aby bylo toto umístění z objektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Určení vlastností virtuální sítě
Každý prostředek Správce prostředků `properties` má vlastní oddíl pro konfigurace specifické pro prostředek. V takovém případě určete, že virtuální síť by měla `10.0.0.0/16`mít jednu podsíť pomocí rozsahu privátních ADRES IP . Škálovací sada je vždy obsažena v jedné podsíti. Nemůže sít podsítě.

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
`type`Kromě požadovaných `name`vlastností `apiVersion`, `location` , a může mít `dependsOn` každý prostředek volitelný seznam řetězců. Tento seznam určuje, které další prostředky z tohoto nasazení musí být dokončeny před nasazením tohoto prostředku.

V tomto případě je v seznamu pouze jeden prvek, virtuální síť z předchozího příkladu. Tuto závislost zadáte, protože škálovací sada potřebuje, aby síť existovala před vytvořením virtuálních stránek. Tímto způsobem škálovací sada můžete poskytnout tyto virtuální počítače soukromé IP adresy z rozsahu IP adres dříve zadaných ve vlastnostech sítě. Formát každého řetězce v seznamu dependsOn je `<type>/<name>`. Použijte stejné `type` `name` a používané dříve v definici prostředku virtuální sítě.

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
## <a name="specify-scale-set-properties"></a>Určení vlastností škálovací sady
Škálovací sady mají mnoho vlastností pro přizpůsobení virtuálních počítače v škálovací sadě. Úplný seznam těchto vlastností naleznete v [odkazu na šablonu](/azure/templates/microsoft.compute/virtualmachinescalesets). V tomto kurzu je nastaveno pouze několik běžně používaných vlastností.
### <a name="supply-vm-size-and-capacity"></a>Velikost a kapacita virtuálního počítače dodávky
Škálovací sada potřebuje vědět, jakou velikost virtuálního počítače vytvořit ("název sku") a kolik takových virtuálních počítače k vytvoření ("kapacita sku"). Informace o tom, které velikosti virtuálních aplikací jsou k dispozici, najdete v [dokumentaci k velikosti virtuálních počítače](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Výběr typu aktualizací
Škálovací sada také potřebuje vědět, jak zpracovat aktualizace na škálovací sadě. V současné době existují `Manual`tři `Rolling` `Automatic`možnosti , a . Další informace o rozdílech mezi těmito dvěma informacemi naleznete v dokumentaci [k upgradu škálovací sady](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Volba operačního systému virtuálního provozu
Škálovací sada potřebuje vědět, jaký operační systém umístit na virtuální chod. Zde vytvořte virtuální tlačítka s plně opraveným obrazem Ubuntu 16.04-LTS.

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

### <a name="specify-computernameprefix"></a>Zadat opravu _computerNamePrefix
Škálovací sada nasazuje více virtuálních počítačích. Místo zadávání jednotlivých názvů virtuálních virtuálních bylv, zadejte `computerNamePrefix`. Škálovací sada připojí index k předponě pro každý virtuální ms, takže názvy virtuálních počítačů mají formulář `<computerNamePrefix>_<auto-generated-index>`.

V následujícím fragmentu použijte parametry z před nastavit uživatelské jméno správce a heslo pro všechny virtuální počítače ve škálovací sadě. Tento proces `parameters` používá funkci šablony. Tato funkce přebírá řetězec, který určuje, na který parametr se má odkazovat, a vynese hodnotu pro tento parametr.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Určení konfigurace sítě virtuálního počítače
Nakonec zadejte konfiguraci sítě pro virtuální počítače ve škálovací sadě. V takovém případě stačí zadat ID dříve vytvořené podsítě. To říká škálovací sadu umístit síťová rozhraní v této podsíti.

ID virtuální sítě obsahující podsíť můžete získat pomocí `resourceId` funkce šablony. Tato funkce přebírá typ a název prostředku a vrátí plně kvalifikovaný identifikátor tohoto prostředku. Toto ID má formulář:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Identifikátor virtuální sítě však nestačí. Zadejte konkrétní podsíť, ve které by měly být virtuální chody škálovací sady. Chcete-li to provést, `/subnets/mySubnet` zřetězit na ID virtuální sítě. Výsledkem je plně kvalifikované ID podsítě. Proveďte toto zřetězení s `concat` funkcí, která přebírá řadu řetězců a vrací jejich zřetězení.

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
