---
title: Ukázka – vysazení značky a hodnoty ve skupinách prostředků
description: Tato definice ukázkové zásady vyžaduje značku a hodnotu ve skupině prostředků.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/31/2019
ms.author: dacoulte
ms.openlocfilehash: 5f4af5ee88ad491e7864e82afc337801e47f2204
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255770"
---
# <a name="sample---enforce-tag-and-its-value-on-resource-groups"></a>Ukázka – vynutilo značku a její hodnotu ve skupinách prostředků

Tato zásada vyžaduje pro skupinu prostředků značku a hodnotu. Zadejte požadovaný název a hodnotu značky.

Tuto ukázkovou zásadu můžete nasadit pomocí:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Rozhraní příkazového řádku Azure](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Ukázkové zásady

### <a name="policy-definition"></a>Definice zásad

Kompletní definice zásady složeného JSON, kterou používají REST API, nasadit do Azure a ručně na portálu.

[!code-json[main](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]

> [!NOTE]
> Pokud zásadu vytváříte ručně na portálu, použijte výše uvedené části **Properties. Parameters** a **Properties. policyRule** . Zabalte dvě části dohromady se složenými závorkami `{}`, aby se zajistil platný formát JSON.

### <a name="policy-rules"></a>Pravidla zásad

JSON definující pravidla zásad, která používá Azure CLI a Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zásad

JSON definující parametry zásad, které používá Azure CLI a Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Jméno |Type |Pole |Description |
|---|---|---|---|
|Značky |Řetězec |značky |Název značky, například costCenter|
|tagValue |Řetězec |značky |Hodnota značky, například sídel|

Při vytváření přiřazení prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure můžete hodnoty parametrů předat jako JSON buď v řetězci, nebo prostřednictvím souboru pomocí `-PolicyParameter` (PowerShell) nebo `--params` (Azure CLI).
PowerShell také podporuje `-PolicyParameterObject`, která vyžaduje předání rutiny s názvem a hodnotou hash, kde **název** je název parametru a **hodnota** je jediná hodnota nebo pole hodnot předávaných během přiřazení.

V tomto příkladu parametru je definována _TagName_ z **costCenter** a _tagValue_ of **sídel** .

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Portál Azure Portal

[@no__t – 1Deploy vzor zásady do azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)
[![Deploy vzor zásady do Azure gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'enforce-resourceGroup-tags' -DisplayName 'Enforce tag and its value on resource groups' -description 'Enforces a required tag and its value on resource groups.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyParam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'enforce-resourceGroup-tags-assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyParam
```

### <a name="remove-with-azure-powershell"></a>Odebrat s Azure PowerShell

Spuštěním následujících příkazů odeberte předchozí přiřazení a definici:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Vysvětlení Azure PowerShell

Skripty nasazení a odebrání používají následující příkazy. Každý příkaz v následující tabulce odkazuje na dokumentaci specifickou pro příkaz:

| Příkaz | Poznámky |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Vytvoří novou definici Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Načte jednu skupinu prostředků. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu poskytujeme definici, ale může také vzít v úvahu podnět. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Odebere existující přiřazení Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Odebere existující definici Azure Policy. |

## <a name="azure-cli"></a>Rozhraní příkazového řádku Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Nasazení pomocí Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyParam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(
az policy assignment create --name 'enforce-resourceGroup-tags-assignment' --display-name 'Enforce tag and its value on resource groups'  --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Odebrat pomocí Azure CLI

Spuštěním následujících příkazů odeberte předchozí přiřazení a definici:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Vysvětlení Azure CLI

| Příkaz | Poznámky |
|---|---|
| [AZ Policy definition Create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Vytvoří novou definici Azure Policy. |
| [AZ Group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Načte jednu skupinu prostředků. |
| [AZ Policy Assignment Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu poskytujeme definici, ale může také vzít v úvahu podnět. |
| [AZ Policy Assignment DELETE](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Odebere existující přiřazení Azure Policy. |
| [AZ Policy definition DELETE](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Odebere existující definici Azure Policy. |

Existuje několik nástrojů, které lze použít k interakci s Správce prostředků REST API, jako je například [ARMClient](https://github.com/projectkudu/ARMClient) nebo PowerShell. Příklad volání REST API z PowerShellu najdete v části **aliasy** v části [Struktura definice zásad](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>REST API

### <a name="deploy-with-rest-api"></a>Nasazení pomocí REST API

- Vytvořte definici zásady (obor předplatného). Pro tělo žádosti použijte JSON [definice zásad](#policy-definition) .

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

- Vytvoření přiřazení zásady (rozsah skupiny prostředků)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

  Pro tělo žádosti použijte následující příklad JSON:

```json
  {
      "properties": {
          "displayName": "Enforce tag and its value Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Odebrat s REST API

- Odebrat přiřazení zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

- Odebrat definici zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Vysvětlení REST API

| Služba | Skupina | Operace | Poznámky |
|---|---|---|---|
| Správa prostředků | Definice zásad | [Vytvořit](/rest/api/resources/policydefinitions/createorupdate) | Vytvoří novou definici Azure Policy v rámci předplatného. Alternativa: [vytvořit ve skupině pro správu](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Správa prostředků | Přiřazení zásad | [Vytvořit](/rest/api/resources/policyassignments/create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu poskytujeme definici, ale může také vzít v úvahu podnět. |
| Správa prostředků | Přiřazení zásad | [Odstranit](/rest/api/resources/policyassignments/delete) | Odebere existující přiřazení Azure Policy. |
| Správa prostředků | Definice zásad | [Odstranit](/rest/api/resources/policydefinitions/delete) | Odebere existující definici Azure Policy. Alternativní: [odstranění ve skupině pro správu](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Další kroky

- Kontrola dalších [ukázek Azure Policy](index.md)
- Kontrola [Azure Policy struktury definice](../concepts/definition-structure.md)