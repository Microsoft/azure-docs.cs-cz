---
title: Trezory ukázek klíčů bez koncových bodů virtuální sítě
description: Tato ukázková definice zásad Audituje Key Vault trezory k detekci instancí, které nemají žádné koncové body služby virtuální sítě.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 7bcbcdf68b3c8f882a1e0fbb9953fea575f96556
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255728"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Ukázky Key Vault trezorů bez koncových bodů virtuální sítě

Tato zásada Audituje Key Vault trezory, které nemají žádné koncové body virtuální sítě. Použijte k vymáhání požadavků zabezpečení. Další informace najdete v tématu [koncové body služby virtuální sítě v Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md) .

Tuto ukázkovou zásadu můžete nasadit pomocí:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Rozhraní příkazového řádku Azure](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Ukázkové zásady

### <a name="policy-definition"></a>Definice zásad

Kompletní definice zásady složeného JSON, kterou používají REST API, nasadit do Azure a ručně na portálu.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Pokud zásadu vytváříte ručně na portálu, použijte výše uvedené části **Properties. Parameters** a **Properties. policyRule** . Zabalte dvě části dohromady se složenými závorkami `{}`, aby se zajistil platný formát JSON.

### <a name="policy-rules"></a>Pravidla zásad

JSON definující pravidla zásad, která používá Azure CLI a Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zásad

V této definici ukázkové zásady nejsou definovány žádné parametry.

## <a name="azure-portal"></a>Portál Azure Portal

[@no__t – 1Deploy vzor zásady do azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Deploy vzor zásady do Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
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
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
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

## <a name="rest-api"></a>REST API

Existuje několik nástrojů, které lze použít k interakci s Správce prostředků REST API, jako je například [ARMClient](https://github.com/projectkudu/ARMClient) nebo PowerShell.

### <a name="deploy-with-rest-api"></a>Nasazení pomocí REST API

- Vytvořte definici zásady (obor předplatného). Pro tělo žádosti použijte JSON [definice zásad](#policy-definition) .

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Vytvoření přiřazení zásady (rozsah skupiny prostředků)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Pro tělo žádosti použijte následující příklad JSON:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Odebrat s REST API

- Odebrat přiřazení zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Odebrat definici zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
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