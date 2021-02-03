---
title: Funkce šablon v oboru nasazení
description: Popisuje způsob řešení funkcí šablon v oboru nasazení. Oborem může být tenant, skupiny pro správu, předplatná a skupiny prostředků.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492089"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Funkce šablon ARM v oborech nasazení

Pomocí Azure Resource Manager šablon (šablon ARM) můžete nasadit do skupin prostředků, předplatných, skupin pro správu nebo klientů. Obecně platí, že [funkce šablon ARM](template-functions.md) fungují stejně pro všechny obory. Tento článek popisuje rozdíly, které existují u některých funkcí v závislosti na rozsahu.

## <a name="supported-functions"></a>Podporované funkce

Při nasazování do různých oborů existují některé důležité předpoklady:

* Funkce [Resource ()](template-functions-resource.md#resourcegroup) je **podporovaná** pro nasazení skupin prostředků.
* Funkce [Subscription ()](template-functions-resource.md#subscription) je **podporovaná** pro nasazení skupiny prostředků a předplatných.
* Funkce [Reference ()](template-functions-resource.md#reference) a [list ()](template-functions-resource.md#list) jsou **podporovány** pro všechny obory.
* Identifikátor [ResourceID ()](template-functions-resource.md#resourceid) použijte k získání ID prostředku nasazeného ve skupině prostředků.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* K získání ID prostředku nasazeného v rámci předplatného použijte funkci [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) .

  Pokud například chcete získat ID prostředku pro definici zásady, která je nasazena v rámci předplatného, použijte:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Pro prostředky, které jsou implementovány jako rozšíření skupiny pro správu, použijte funkci [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Vlastní definice zásad, které jsou nasazené ve skupině pro správu, jsou rozšířeními skupiny pro správu.

  Pokud chcete získat ID prostředku pro definici vlastní zásady na úrovni skupiny pro správu, použijte:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* K získání ID prostředku nasazeného v tenantovi použijte funkci [tenantResourceId ()](template-functions-resource.md#tenantresourceid) . Předdefinované definice zásad jsou prostředky na úrovni tenanta. Při přiřazování předdefinované zásady na úrovni skupiny pro správu použijte funkci tenantResourceId.

  Pokud chcete získat ID prostředku pro předdefinovanou definici zásady, použijte:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Řešení funkcí v oborech

Když nasadíte do více než jednoho oboru, funkce [Resource ()](template-functions-resource.md#resourcegroup) a [Subscription ()](template-functions-resource.md#subscription) se vyřeší odlišně podle toho, jak šablonu zadáte. Když propojíte externí šablonu, funkce se vždy vyhodnotí do oboru pro tuto šablonu. Při vnořování šablony v rámci nadřazené šablony použijte `expressionEvaluationOptions` vlastnost k určení, zda funkce překládá na skupinu prostředků a odběr nadřazené šablony nebo vnořené šablony. Nastavte vlastnost na hodnotu `inner` , aby se přeložila na obor vnořené šablony. Nastavte vlastnost na hodnotu `outer` , aby se přeložila na rozsah nadřazené šablony.

Následující tabulka ukazuje, jestli se funkce překládají na nadřazenou nebo integrovanou skupinu prostředků a předplatné.

| Typ šablony | Obor | Řešení |
| ------------- | ----- | ---------- |
| vnořen        | vnější (výchozí) | Nadřazená skupina prostředků |
| vnořen        | vnořen | Dílčí skupina prostředků |
| Spojeného        | –   | Dílčí skupina prostředků |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ukazuje:

* vnořená šablona s výchozím (vnějším) rozsahem
* vnořená šablona s vnitřním rozsahem
* odkazovaná šablona

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Další kroky

* Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony ARM pomocí tokenu SAS](secure-template-with-sas-token.md).
