---
title: Historie nasazení
description: Popisuje, jak zobrazit operace nasazení Azure Resource Manager u portálu, PowerShellu, rozhraní API Azure a rozhraní REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460292"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Zobrazení historie nasazení pomocí Správce prostředků Azure

Azure Resource Manager umožňuje zobrazit historii nasazení a zkoumat konkrétní operace v minulých nasazeních. Můžete zobrazit prostředky, které byly nasazeny, a získat informace o všech chybách.

Nápovědu k řešení konkrétních chyb nasazení najdete [v tématu Řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Získání nasazení a ID korelace

Podrobnosti o nasazení můžete zobrazit prostřednictvím portálu Azure, PowerShellu, rozhraní API Azure nebo rozhraní REST API. Každé nasazení má ID korelace, které se používá ke sledování souvisejících událostí. To může být užitečné při práci s technickou podporou k řešení potíží s nasazením.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Vyberte skupinu prostředků, kterou chcete prozkoumat.

1. Vyberte odkaz v části **Nasazení**.

   ![Vybrat historii nasazení](./media/deployment-history/select-deployment-history.png)

1. Vyberte jedno z nasazení z historie nasazení.

   ![Vybrat nasazení](./media/deployment-history/select-details.png)

1. Zobrazí se souhrn nasazení, včetně ID korelace.

    ![Souhrn nasazení](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li vypsat všechna nasazení pro skupinu prostředků, použijte příkaz [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Chcete-li získat konkrétní nasazení ze skupiny prostředků, přidejte parametr **DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Chcete-li získat ID korelace, použijte:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li vypsat nasazení pro skupinu prostředků, použijte [seznam skupin nasazení az](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Chcete-li získat konkrétní nasazení, použijte [az nasazení skupiny show](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Chcete-li získat ID korelace, použijte:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[Protokol HTTP](#tab/http)

Chcete-li vypsat nasazení pro skupinu prostředků, použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v požadavku, najdete v [tématu Nasazení – seznam podle skupiny prostředků](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Chcete-li získat konkrétní nasazení. použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v požadavku, najdete v [tématu Nasazení – získat](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Odpověď obsahuje ID korelace.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Získání operací nasazení a chybové zprávy

Každé nasazení může zahrnovat více operací. Chcete-li zobrazit další podrobnosti o nasazení, podívejte se na operace nasazení. Pokud se nasazení nezdaří, operace nasazení obsahují chybovou zprávu.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. V souhrnu nasazení vyberte **podrobnosti operace**.

    ![Vybrat operace nasazení](./media/deployment-history/get-operation-details.png)

1. Zobrazí se podrobnosti pro tento krok nasazení. Dojde-li k chybě, podrobnosti obsahují chybovou zprávu.

    ![Zobrazit podrobnosti operace](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li zobrazit operace nasazení pro nasazení do skupiny prostředků, použijte příkaz [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Chcete-li zobrazit neúspěšné operace, filtrujte operace se stavem **Selhání.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Chcete-li získat zprávu o stavu neúspěšných operací, použijte následující příkaz:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zobrazit operace nasazení pro nasazení do skupiny prostředků, použijte příkaz [seznamu seznamu operací skupiny nasazení az.](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Chcete-li zobrazit neúspěšné operace, filtrujte operace se stavem **Selhání.**

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Chcete-li získat zprávu o stavu neúspěšných operací, použijte následující příkaz:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[Protokol HTTP](#tab/http)

Chcete-li získat operace nasazení, použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v požadavku, naleznete v [tématu Deployment Operations – List](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Odpověď obsahuje chybovou zprávu.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Další kroky

* Nápovědu k řešení konkrétních chyb nasazení najdete [v tématu Řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](common-deployment-errors.md).
* Informace o použití protokolů aktivit ke sledování jiných typů akcí najdete v tématu [Zobrazení protokolů aktivit ke správě prostředků Azure](../management/view-activity-logs.md).
* Informace o ověření nasazení před jeho spuštěním najdete [v tématu Nasazení skupiny prostředků pomocí šablony Azure Resource Manager](deploy-powershell.md).

