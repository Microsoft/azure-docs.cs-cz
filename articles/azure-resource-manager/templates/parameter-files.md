---
title: Vytvoření souboru parametrů
description: Vytvoří soubor parametrů pro předávání hodnot během nasazování šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531222"
---
# <a name="create-resource-manager-parameter-file"></a>Vytvořit soubor Správce prostředků parametrů

Místo předání parametrů jako vložených hodnot do skriptu můžete použít soubor JSON, který obsahuje hodnoty parametrů. Tento článek ukazuje, jak vytvořit soubor parametrů, který použijete se šablonou JSON nebo souborem bicep.

## <a name="parameter-file"></a>Soubor parametrů

Soubor parametrů používá následující formát:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Všimněte si, že soubor parametrů ukládá hodnoty parametrů jako prostý text. Tento přístup funguje pro hodnoty, které nejsou citlivé, jako je třeba SKU prostředku. Prostý text nefunguje u citlivých hodnot, jako jsou hesla. Pokud potřebujete předat parametr, který obsahuje citlivou hodnotu, uložte hodnotu do trezoru klíčů. Pak na Trezor klíčů vytvořte odkaz v souboru parametrů. Citlivá hodnota je bezpečně načtena během nasazení.

Následující soubor parametrů obsahuje hodnotu prostého textu a citlivou hodnotu, která je uložena v trezoru klíčů.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Další informace o použití hodnot z trezoru klíčů najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definovat hodnoty parametrů

Chcete-li určit, jak definovat názvy parametrů a hodnoty, otevřete šablonu JSON nebo bicep. Podívejte se do části Parameters (parametry) v šabloně. Následující příklady znázorňují parametry ze šablon JSON a bicep.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

V souboru parametrů je první podrobná Poznámka název každého parametru. Názvy parametrů v souboru parametrů se musí shodovat s názvy parametrů ve vaší šabloně.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Všimněte si typu parametru. Typy parametrů v souboru parametrů musí používat stejné typy jako vaše šablona. V tomto příkladu oba typy parametrů jsou řetězce.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

V šabloně vyhledejte parametry s výchozí hodnotou. Pokud má parametr výchozí hodnotu, můžete zadat hodnotu v souboru parametrů, ale není to vyžadováno. Hodnota souboru parametrů přepíše výchozí hodnotu šablony.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Ověřte povolené hodnoty šablony a jakákoli omezení, jako je maximální délka. Tyto hodnoty určují rozsah hodnot, které můžete zadat pro parametr. V tomto příkladu `storagePrefix` může mít maximálně 11 znaků a `storageAccountType` musí určovat povolenou hodnotu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

> [!NOTE]
> Soubor parametrů může obsahovat pouze hodnoty parametrů, které jsou definovány v šabloně. Pokud soubor parametrů obsahuje další parametry, které neodpovídají parametrům šablony, zobrazí se chyba.

## <a name="parameter-type-formats"></a>Formáty typů parametrů

Následující příklad ukazuje formáty různých typů parametrů: String, Integer, Boolean, Array a Object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

## <a name="deploy-template-with-parameter-file"></a>Nasadit šablonu se souborem parametrů

Z Azure CLI předáte soubor s místními parametry pomocí `@` a název souboru parametru. Například, `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure CLI](./deploy-cli.md#parameters). K nasazení souborů _. bicep_ potřebujete Azure CLI verze 2,20 nebo vyšší.

Z Azure PowerShell předáte soubor s místním parametrem pomocí `TemplateParameterFile` parametru.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](./deploy-powershell.md#pass-parameter-values). K nasazení souborů _. bicep_ potřebujete Azure PowerShell verze 5.6.0 nebo vyšší.

> [!NOTE]
> Nemůžete použít soubor parametrů v okně vlastní šablony na portálu.

> [!TIP]
> Pokud používáte [projekt skupiny prostředků Azure v sadě Visual Studio](create-visual-studio-deployment-project.md), ujistěte se, že soubor parametrů má svou **akci sestavení** nastavenou na **obsah**.

## <a name="file-name"></a>Název souboru

Obecná konvence pojmenování pro soubor parametrů je zahrnutí _parametrů_ do názvu šablony. Například pokud je vaše šablona pojmenována _azuredeploy.jsna_, váš soubor s parametrem má název _azuredeploy.parameters.js_. Tato konvence pojmenování vám pomůže zobrazit propojení mezi šablonou a parametry.

Pro nasazení do různých prostředí můžete vytvořit více než jeden soubor parametrů. Při pojmenování souborů parametrů Identifikujte jejich použití, jako je například vývoj a produkce. Můžete například použít _azuredeploy.parameters-dev.jsv_ a _azuredeploy.parameters-prod.jsna_ k nasazení prostředků.

## <a name="parameter-precedence"></a>Priorita parametru

Můžete použít vložené parametry a místní soubor parametrů ve stejné operaci nasazení. Můžete například zadat některé hodnoty v souboru s místním parametrem a přidat další hodnoty vložené během nasazování. Zadáte-li hodnoty pro parametr v místním souboru parametrů i v poli inline, má hodnota inline přednost.

Je možné použít externí soubor parametrů poskytnutím identifikátoru URI souboru. Při použití externího souboru parametrů nelze předat jiné hodnoty buď vložené, nebo z místního souboru. Všechny vložené parametry jsou ignorovány. Zadejte všechny hodnoty parametrů v externím souboru.

## <a name="parameter-name-conflicts"></a>Konflikty názvů parametrů

Pokud vaše šablona obsahuje parametr se stejným názvem jako jeden z parametrů v příkazu PowerShellu, PowerShell prezentuje parametr z vaší šablony s příponou `FromTemplate` . Například parametr s názvem `ResourceGroupName` v šabloně koliduje s `ResourceGroupName` parametrem v rutině [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Budete vyzváni k zadání hodnoty pro `ResourceGroupNameFromTemplate` . Chcete-li se vyhnout této nejasnostem, použijte názvy parametrů, které se nepoužívají pro příkazy nasazení.

## <a name="next-steps"></a>Další kroky

- Další informace o definování parametrů v šabloně najdete v tématu [parametry v šablonách ARM](template-parameters.md).
- Další informace o použití hodnot z trezoru klíčů najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](key-vault-parameter.md).
