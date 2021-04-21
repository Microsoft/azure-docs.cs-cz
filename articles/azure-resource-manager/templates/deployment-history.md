---
title: Historie nasazení
description: Popisuje postup zobrazení Azure Resource Manager operací nasazení pomocí portálu, PowerShellu, rozhraní příkazového řádku Azure a REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781860"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Zobrazit historii nasazení pomocí Azure Resource Manager

Azure Resource Manager vám umožní zobrazit historii nasazení. Můžete prozkoumat konkrétní operace v předchozích nasazeních a zjistit, jaké prostředky se nasadily. Tato historie obsahuje informace o všech chybách.

Historie nasazení pro skupinu prostředků je omezená na 800 nasazení. Po dosažení limitu se nasazení z historie automaticky odstraní. Další informace najdete v tématu [Automatické odstraňování z historie nasazení](deployment-history-deletions.md).

Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Získání nasazení a ID korelace

Podrobnosti o nasazení můžete zobrazit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Každé nasazení má ID korelace, které se používá ke sledování souvisejících událostí. Pokud [vytvoříte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), může vám podpora požádat o ID korelace. Podpora používá ID korelace k identifikaci operací nasazení, které selhalo.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte skupinu prostředků, kterou chcete prošetřit.

1. Vyberte odkaz v části **nasazení**.

   ![Vybrat historii nasazení](./media/deployment-history/select-deployment-history.png)

1. V historii nasazení vyberte jedno z nasazení.

   ![Vybrat nasazení](./media/deployment-history/select-details.png)

1. Zobrazí se souhrn nasazení, včetně ID korelace.

    ![Souhrn nasazení](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete zobrazit seznam všech nasazení pro skupinu prostředků, použijte příkaz [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Pokud chcete získat konkrétní nasazení ze skupiny prostředků, přidejte parametr pro **nasazení** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

ID korelace získáte pomocí:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit seznam nasazení pro skupinu prostředků, použijte příkaz [AZ Deployment Group list](/cli/azure/group/deployment#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Konkrétní nasazení získáte pomocí [skupiny AZ Deployment Group show](/cli/azure/group/deployment#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

ID korelace získáte pomocí:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Chcete-li zobrazit seznam nasazení pro skupinu prostředků, použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v žádosti, najdete v tématu  [nasazení – seznam podle skupiny prostředků](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Pro získání konkrétního nasazení. použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v žádosti, najdete v tématu [nasazení – získat](/rest/api/resources/deployments/get).

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

Každé nasazení může zahrnovat více operací. Pokud chcete zobrazit další podrobnosti o nasazení, Prohlédněte si operace nasazení. V případě neúspěšného nasazení budou operace nasazení zahrnovat chybovou zprávu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V souhrnu pro nasazení vyberte **Podrobnosti operace**.

    ![Vybrat podrobnosti operace](./media/deployment-history/get-operation-details.png)

1. Zobrazí se podrobnosti o tomto kroku nasazení. Pokud dojde k chybě, Podrobnosti obsahují chybovou zprávu.

    ![Zobrazit podrobnosti o operaci](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete zobrazit operace nasazení pro nasazení do skupiny prostředků, použijte příkaz [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Chcete-li zobrazit neúspěšné operace, vyfiltrujte operace se stavem **selhání** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Stavovou zprávu o neúspěšných operacích získáte pomocí následujícího příkazu:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit operace nasazení pro nasazení do skupiny prostředků, použijte příkaz [AZ Deployment Operation Group list](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) . Musíte mít Azure CLI 2.6.0 nebo novější.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Chcete-li zobrazit neúspěšné operace, vyfiltrujte operace se stavem **selhání** .

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Stavovou zprávu o neúspěšných operacích získáte pomocí následujícího příkazu:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

K získání operací nasazení použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v žádosti, najdete v tématu věnovaném [operacím nasazení – seznam](/rest/api/resources/deploymentoperations/list).

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

* Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](common-deployment-errors.md).
* Další informace o tom, jak se nasazení spravují v historii, najdete v tématu [Automatické odstraňování z historie nasazení](deployment-history-deletions.md).
* Chcete-li před spuštěním ověřit nasazení, přečtěte si téma [nasazení skupiny prostředků pomocí šablony Azure Resource Manager](deploy-powershell.md).
