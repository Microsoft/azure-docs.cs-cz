---
title: Moduly bicep
description: Popisuje, jak definovat a využívat modul a jak používat obory modulů.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 2edeb5c96f771867f964963b2d27768291ae2d4a
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889701"
---
# <a name="use-bicep-modules"></a>Použití modulů bicep

Bicep umožňuje rozdělit komplexní řešení do modulů. Modul bicep je sada jednoho nebo více prostředků, které se mají nasadit společně. Moduly s abstraktními komplexními podrobnostmi o deklaraci nezpracovaného prostředku, které mohou zvýšit čitelnost. Tyto moduly můžete znovu použít a sdílet je s dalšími lidmi. V kombinaci s [specifikacemi šablon](./template-specs.md)vytvoří způsob pro modularitu a opakované použití kódu. Kurz najdete v tématu [kurz: Přidání bicep modulů](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definovat moduly

Každý soubor bicep lze spotřebovat jako modul. Modul zpřístupňuje jenom parametry a výstupy jako kontrakt do jiných souborů bicep. Parametry i výstupy jsou volitelné.

Následující soubor bicep se dá nasadit přímo k vytvoření účtu úložiště nebo ho použít jako modul.  V další části se dozvíte, jak využívat moduly:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Výstup se používá k předání hodnot do nadřazených bicep souborů.

## <a name="consume-modules"></a>Využívat moduly

Použijte klíčové slovo _Module_ pro využití modulu. Následující soubor bicep nasadí prostředek definovaný v souboru modulu, na který se odkazuje:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **Module**: klíčové slovo
- **symbolický název** (stgModule): identifikátor pro modul.
- **soubor modulu**: cesta k modulu v tomto příkladu je určena pomocí relativní cesty (./storageAccount.bicep). Všechny cesty v bicep musí být zadané pomocí oddělovače adresáře dopředného lomítka (/), aby se zajistila konzistentní kompilace více platforem. Znak zpětného lomítka () systému Windows \\ není podporován.
- Při zpracování modulu se vyžaduje vlastnost **_Name_** (storageDeploy). Když bicep vygeneruje šablonu IL, toto pole se použije jako název vnořeného prostředku nasazení, který se generuje pro modul:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Chcete-li získat výstupní hodnotu z modulu, načtěte hodnotu vlastnosti pomocí syntaxe jako: `stgModule.outputs.storageEndpoint` kde `stgModule` je identifikátor modulu.

## <a name="configure-module-scopes"></a>Konfigurace oborů modulů

Při deklaraci modulu můžete zadat vlastnost _oboru_ pro nastavení rozsahu, do kterého chcete nasadit modul:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Vlastnost _Scope_ může být vynechána, pokud cílový obor modulu a cílový obor nadřazeného objektu jsou stejné. Pokud není zadána vlastnost oboru, modul je nasazen do cílového oboru nadřazeného objektu.

Následující soubor bicep ukazuje, jak vytvořit skupinu prostředků a nasadit modul do skupiny prostředků:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete projít kurz, přečtěte si [kurz: Přidání bicep modulů](./bicep-tutorial-add-modules.md).
