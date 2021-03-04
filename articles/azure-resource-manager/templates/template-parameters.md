---
title: Parametry v šablonách
description: Popisuje definování parametrů v šabloně Azure Resource Manager (šablona ARM) a souboru bicep.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b23417766524204e490450568bb80b8c49e2d328
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043928"
---
# <a name="parameters-in-arm-templates"></a>Parametry v šablonách ARM

Tento článek popisuje, jak definovat a používat parametry ve vaší šabloně Azure Resource Manager (šablona ARM) a souboru bicep. Zadáním různých hodnot pro parametry můžete znovu použít šablonu pro různá prostředí.

Správce prostředků řeší hodnoty parametrů před spuštěním operací nasazení. Všude, kde je parametr použit v šabloně, Správce prostředků ho nahradí vyřešenou hodnotou.

Každý parametr musí být nastaven na jeden z [datových typů](template-syntax.md#data-types).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Minimální deklarace

Minimálně každý parametr vyžaduje název a typ. V bicep parametr nemůže mít stejný název jako proměnná, prostředek, výstup nebo jiný parametr ve stejném oboru.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Zabezpečené parametry

Parametry řetězce nebo objektu můžete označit jako zabezpečené. Hodnota zabezpečeného parametru se neuloží do historie nasazení a není zaprotokolovaná.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Povolené hodnoty

Můžete definovat povolené hodnoty pro parametr. V poli zadáte povolené hodnoty. Nasazení se během ověřování nezdařilo, pokud je hodnota předána pro parametr, který není jednou z povolených hodnot.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Výchozí hodnota

Pro parametr můžete zadat výchozí hodnotu. Výchozí hodnota se použije, když během nasazování není zadaná hodnota.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Chcete-li zadat výchozí hodnotu spolu s dalšími vlastnostmi parametru, použijte následující syntaxi.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

Můžete použít výrazy s výchozí hodnotou. V části Parameters (parametry) nemůžete použít [odkazovou](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) . Tyto funkce získávají běhový stav prostředku a nelze jej provést před nasazením, když jsou parametry vyřešeny.

Výrazy nejsou povoleny s jinými vlastnostmi parametrů.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

K vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona vytvoří název plánu hostitele z názvu webu.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Omezení délky

Můžete zadat minimální a maximální délku pro parametry řetězce a pole. Můžete nastavit jedno nebo obě omezení. Pro řetězce označuje Délka počet znaků. Pro pole length určuje počet položek v poli.

Následující příklad deklaruje dva parametry. Jeden parametr je pro název účtu úložiště, který musí mít 3-24 znaků. Druhým parametrem je pole, které musí mít od 1-5 položek.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Celočíselná omezení

Můžete nastavit minimální a maximální hodnoty pro celočíselné parametry. Můžete nastavit jedno nebo obě omezení.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Popis

Můžete přidat popis k parametru, který uživatelům vaší šablony pomůže pochopit hodnotu, která má být k dispozici. Při nasazování šablony prostřednictvím portálu se text, který zadáte v popisu, automaticky použije jako Tip pro tento parametr. Pokud text poskytne více informací, než lze odvodit z názvu parametru, přidejte pouze popis.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Použít parametr

V šabloně JSON odkazujete na hodnotu parametru pomocí funkce [Parameters](template-functions-deployment.md#parameters) . V bicep použijete název parametru. Následující příklad používá hodnotu parametru pro Key Vault název.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objekty jako parametry

Můžete snadněji organizovat související hodnoty jejich předáním v podobě objektu. Tento přístup také snižuje počet parametrů v šabloně.

Následující příklad ukazuje parametr, který je objektem. Výchozí hodnota zobrazuje očekávané vlastnosti objektu. Tyto vlastnosti se používají při definování prostředku, který se má nasadit.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>Příklady šablon

Následující příklady ukazují scénáře použití parametrů.

|Template (Šablona)  |Popis  |
|---------|---------|
|[parametry s funkcemi pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Ukazuje, jak používat funkce šablon při definování výchozích hodnot pro parametry. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |
|[objekt parametru](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech parametrů naleznete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Další informace o předávání hodnot parametrů jako souboru naleznete v tématu [Create správce prostředků Parameter File](parameter-files.md).
* Doporučení k vytváření parametrů najdete v tématu [osvědčené postupy – parametry](template-best-practices.md#parameters).
