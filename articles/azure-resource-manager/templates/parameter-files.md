---
title: Vytvoření souboru parametrů
description: Vytvoření souboru parametrů pro předávání hodnot během nasazení šablony Správce prostředků Azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bb52d9c5ebcb0820362e5de3d6b24b0b18d742e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155617"
---
# <a name="create-resource-manager-parameter-file"></a>Vytvořit soubor parametrů Správce prostředků

Spíše než předávání parametrů jako vložkové hodnoty ve skriptu, může být jednodušší použít soubor JSON, který obsahuje hodnoty parametrů. Tento článek ukazuje, jak vytvořit soubor parametrů.

## <a name="parameter-file"></a>Soubor parametrů

Soubor parametrů má následující formát:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Všimněte si, že hodnoty parametrů jsou uloženy jako prostý text v souboru parametrů. Tento přístup funguje pro hodnoty, které nejsou citlivé, jako je například určení skladové položky pro prostředek. Nefunguje pro citlivé hodnoty, jako jsou hesla. Pokud potřebujete předat citlivou hodnotu jako parametr, uložte hodnotu do trezoru klíčů a odkazujte na trezor klíčů v souboru parametrů. Citlivá hodnota je bezpečně načtena během nasazení.

Následující soubor parametrů obsahuje hodnotu prostého textu a hodnotu, která je uložena v trezoru klíčů.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Další informace o použití hodnot z trezoru klíčů najdete v [tématu Použití trezoru klíčů Azure k předání hodnoty zabezpečeného parametru během nasazení](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definování hodnot parametrů

Chcete-li zjistit, jak definovat hodnoty parametrů, otevřete šablonu, kterou nasazujete. Podívejte se na část parametrů šablony. Následující příklad ukazuje parametry ze šablony.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

První detail, který si všimnete, je název každého parametru. Hodnoty v souboru parametrů se musí shodovat s názvy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Všimněte si typu parametru. Hodnoty v souboru parametrů musí mít stejné typy. Pro tuto šablonu můžete zadat oba parametry jako řetězce.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Dále vyhledejte výchozí hodnotu. Pokud má parametr výchozí hodnotu, můžete zadat hodnotu, ale není to možné.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Nakonec se podívejte na povolené hodnoty a všechna omezení, jako je maximální délka. Řeknou vám rozsah hodnot, které můžete zadat pro parametr.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Formáty typů parametrů

Následující příklad ukazuje formáty různých typů parametrů.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Název souboru

Obecnou konvencí pro pojmenování souboru parametrů je přidání **parametrů .parameters** k názvu šablony. Pokud je například vaše šablona pojmenována **azuredeploy.json**, bude soubor parametrů pojmenován **azuredeploy.parameters.json**. Tato konvence pojmenování vám pomůže zobrazit spojení mezi šablonou a parametry.

Chcete-li nasadit do různých prostředí, vytvořte více než jeden soubor parametrů. Při pojmenování souboru parametrů přidejte způsob, jak identifikovat jeho použití. Použijte například **azuredeploy.parameters-dev.json** a **azuredeploy.parameters-prod.json.**


## <a name="parameter-precedence"></a>Priorita parametru

Můžete použít vložky parametry a soubor místníparametr ve stejné operaci nasazení. Můžete například zadat některé hodnoty v místním souboru parametrů a přidat další hodnoty vsazené během nasazení. Pokud zadáte hodnoty parametru v souboru místního parametru i v řadě, má přednost váděná hodnota.

Pokud však použijete externí soubor parametrů, nemůžete předat jiné hodnoty ani vložku, ani z místního souboru. Všechny vsazené parametry jsou ignorovány. Zadejte všechny hodnoty parametrů v externím souboru.

## <a name="parameter-name-conflicts"></a>Konflikty názvů parametrů

Pokud vaše šablona obsahuje parametr se stejným názvem jako jeden z parametrů v příkazu PowerShell, PowerShell zobrazí parametr z vaší šablony s příponou **FromTemplate**. Například parametr s názvem **ResourceGroupName** v šabloně je v konfliktu s parametrem **ResourceGroupName** v rutině [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Zobrazí se výzva k zadání hodnoty pro **položku ResourceGroupNameFromTemplate**. Tomuto zmatku se můžete vyhnout pomocí názvů parametrů, které se nepoužívají pro příkazy nasazení.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak definovat parametry v šabloně, najdete [v tématu Parametry v šablonách Azure Resource Manageru](template-parameters.md).
- Další informace o použití hodnot z trezoru klíčů najdete v [tématu Použití trezoru klíčů Azure k předání hodnoty zabezpečeného parametru během nasazení](key-vault-parameter.md).
- Další informace o parametrech najdete [v tématu Parametry v šablonách Azure Resource Manageru](template-parameters.md).
