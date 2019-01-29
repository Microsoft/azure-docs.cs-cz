---
title: Ukázkový – imagí schválených virtuálních počítačů
description: Tato ukázková zásada vyžaduje, aby se ve vašem prostředí nasazovaly jenom schválené vlastní image.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 19e6fa5c7286eea26397f0bd69aec1ac9cf03140
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103875"
---
# <a name="approved-vm-images"></a>Schválené image virtuálních počítačů

Tato zásada vyžaduje, aby se ve vašem prostředí nasazovaly jenom schválené vlastní image. Zadejte pole ID schválených imagí.

K nasazení této ukázkové zásady můžete použít:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Ukázková zásada

### <a name="policy-definition"></a>Definice zásady

Kompletní složená definice zásady JSON, která se používá v rozhraní REST API, pro tlačítka Nasadit do Azure a při ručním vytváření na portálu.

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Pokud zásadu vytváříte ručně na portálu, použijte části **properties.parameters** a **properties.policyRule** uvedené výše. Obě části zabalte pomocí složených závorek `{}`, aby byl kód JSON platný.

### <a name="policy-rules"></a>Pravidla zásady

Kód JSON definující pravidla zásady, který se používá v Azure CLI a Azure PowerShellu.

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zásad

Kód JSON definující parametry zásady, který se používá Azure CLI a Azure PowerShellu.

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametry

|Name |Type |Pole |Popis |
|---|---|---|---|
|imageIds |Pole |Microsoft.Compute/imageIds |Seznam schválených imagí virtuálních počítačů|

Při vytváření přiřazení přes PowerShell nebo Azure CLI je možné předat hodnoty parametrů ve formátu JSON buď v řetězci, nebo prostřednictvím souboru pomocí parametru `-PolicyParameter` (PowerShell) nebo `--params` (Azure CLI).
PowerShell podporuje také parametr `-PolicyParameterObject`, který vyžaduje, aby se rutině předala zatřiďovací tabulka názvů a hodnot, kde **název** je název parametru a **hodnota** je jedna hodnota nebo pole hodnot, které se předávají během přiřazení.

V tomto ukázkovém parametru se povolí pouze image _ContosoStdImage_ ve skupině prostředků _YourResourceGroup_ nebo verze image Windows Serveru 2016 Datacenter z května 2018 v umístění USA – střed.

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>portál Azure

[![Nasadit do Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![Nasadit do Azure Government](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Při práci s rozhraním REST API Resource Manageru můžete použít několik nástrojů, jako je [ARMClient](https://github.com/projectkudu/ARMClient) nebo PowerShell. Příklad volání rozhraní REST API z PowerShellu najdete v části **Aliasy** v článku věnovaném [struktuře definic zásad](../concepts/definition-structure.md#aliases).

### <a name="deploy-with-rest-api"></a>Nasazení pomocí rozhraní REST API

- Vytvořte definici zásady (v oboru předplatného). Jako text požadavku použijte JSON s [definicí zásady](#policy-definition).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Vytvořte přiřazení zásady (v oboru skupiny prostředků).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  Jako text požadavku použijte následující příklad JSON:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Odebrání pomocí rozhraní REST API

- Odebrání přiřazení zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Odebrání definice zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
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
- Další příklady pro Azure Policy pro virtuální počítače najdete v článku o [použití zásad pro virtuální počítače s Windows](../../../virtual-machines/windows/policy.md).