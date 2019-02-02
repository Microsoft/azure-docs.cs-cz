---
title: Ukázkový – Key Vault auditu trezory Recovery Services pro koncové body žádné virtuální sítě
description: Tato ukázková zásada Audituje trezory služby Key Vault ke zjišťování instancí, které mají žádné koncové body služby virtuální sítě.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 8269e06d3e2fd92e0d20fbc3dc84d0a1b6761cda
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658432"
---
# <a name="key-vault-vaults-with-no-virtual-network-endpoints"></a>Trezory služby Key Vault s koncovými body žádné virtuální sítě

Tato zásada Audituje pro trezory služby Key Vault, které mají žádné koncové body virtuální sítě. Slouží k vynucení požadavků na zabezpečení. Další informace najdete v tématu [koncové body služeb virtuální sítě ve službě Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

K nasazení této ukázkové zásady můžete použít:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Ukázková zásada

### <a name="policy-definition"></a>Definice zásady

Kompletní složená definice zásady JSON, která se používá v rozhraní REST API, pro tlačítka Nasadit do Azure a při ručním vytváření na portálu.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Pokud zásadu vytváříte ručně na portálu, použijte části **properties.parameters** a **properties.policyRule** uvedené výše. Obě části zabalte pomocí složených závorek `{}`, aby byl kód JSON platný.

### <a name="policy-rules"></a>Pravidla zásady

Kód JSON definující pravidla zásady, který se používá v Azure CLI a Azure PowerShellu.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zásad

Tato ukázková definice zásady nemá definované žádné parametry.

## <a name="azure-portal"></a>portál Azure

[![Nasadit do Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Nasadit do Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Odebrání pomocí Azure PowerShellu

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Vysvětlení Azure PowerShellu

Ve skriptech nasazení a odebrání se používají následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Vytvoří novou definici Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Získá jednu skupinu prostředků. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Odebere stávající přiřazení Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Odebere stávající definici Azure Policy. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Odebrání pomocí Azure CLI

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Vysvětlení Azure CLI

| Příkaz | Poznámky |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Vytvoří novou definici Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Získá jednu skupinu prostředků. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Odebere stávající přiřazení Azure Policy. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Odebere stávající definici Azure Policy. |

## <a name="rest-api"></a>REST API

Při práci s rozhraním REST API Resource Manageru můžete použít několik nástrojů, jako je [ARMClient](https://github.com/projectkudu/ARMClient) nebo PowerShell.

### <a name="deploy-with-rest-api"></a>Nasazení pomocí rozhraní REST API

- Vytvořte definici zásady (v oboru předplatného). Jako text požadavku použijte JSON s [definicí zásady](#policy-definition).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Vytvořte přiřazení zásady (v oboru skupiny prostředků).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Jako text požadavku použijte následující příklad JSON:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Odebrání pomocí rozhraní REST API

- Odebrání přiřazení zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Odebrání definice zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Vysvětlení rozhraní REST API

| Služba | Skupina | Operace | Poznámky |
|---|---|---|---|
| Správa prostředků | Definice zásad | [Vytvoření](/rest/api/resources/policydefinitions/createorupdate) | Vytvoří v předplatném novou definici Azure Policy. Ve zkratce: [Vytvořit ve skupině pro správu](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Správa prostředků | Přiřazení zásad | [Vytvoření](/rest/api/resources/policyassignments/create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| Správa prostředků | Přiřazení zásad | [Odstranění](/rest/api/resources/policyassignments/delete) | Odebere stávající přiřazení Azure Policy. |
| Správa prostředků | Definice zásad | [Odstranění](/rest/api/resources/policydefinitions/delete) | Odebere stávající definici Azure Policy. Ve zkratce: [Odstranit ve skupině pro správu](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Další postup

- Prohlédněte si další [ukázky pro Azure Policy](index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).