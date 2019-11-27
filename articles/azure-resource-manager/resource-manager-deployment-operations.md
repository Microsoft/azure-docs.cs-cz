---
title: Historie nasazení
description: Popisuje postup zobrazení Azure Resource Manager operací nasazení pomocí portálu, PowerShellu, rozhraní příkazového řádku Azure a REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 895704e5c4cb8acc60067809bdd7e7baa6f05142
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538910"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Zobrazit historii nasazení pomocí Azure Resource Manager

Azure Resource Manager vám umožní zobrazit historii nasazení a prozkoumávat konkrétní operace v předchozích nasazeních. Můžete zobrazit nasazené prostředky a získat informace o případných chybách.

Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Získání nasazení a ID korelace

Podrobnosti o nasazení můžete zobrazit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Každé nasazení má ID korelace, které se používá ke sledování souvisejících událostí. Může být užitečné při práci s technickou podporou pro řešení potíží s nasazením.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte skupinu prostředků, kterou chcete prošetřit.

1. Vyberte odkaz v části **nasazení**.

   ![Vybrat historii nasazení](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. V historii nasazení vyberte jedno z nasazení.

   ![Vybrat nasazení](./media/resource-manager-deployment-operations/select-details.png)

1. Zobrazí se souhrn nasazení, včetně ID korelace. 

    ![Souhrn nasazení](./media/resource-manager-deployment-operations/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit seznam nasazení pro skupinu prostředků, použijte příkaz [AZ Group Deployment list](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Pokud chcete získat konkrétní nasazení, použijte příkaz [AZ Group Deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
ID korelace získáte pomocí:

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Chcete-li zobrazit seznam nasazení pro skupinu prostředků, použijte následující operaci. Nejnovější číslo verze rozhraní API, které se má použít v žádosti, najdete v tématu [nasazení – seznam podle skupiny prostředků](/rest/api/resources/deployments/listbyresourcegroup). 

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

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. V souhrnu pro nasazení vyberte **Podrobnosti operace**.

    ![Vybrat operace nasazení](./media/resource-manager-deployment-operations/get-operation-details.png)

1. Zobrazí se podrobnosti o tomto kroku nasazení. Pokud dojde k chybě, Podrobnosti obsahují chybovou zprávu.

    ![Zobrazit podrobnosti o operaci](./media/resource-manager-deployment-operations/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit operace nasazení pro nasazení do skupiny prostředků, použijte příkaz [AZ Group Deployment Operation list](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list) .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Chcete-li zobrazit neúspěšné operace, vyfiltrujte operace se stavem **selhání** .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Stavovou zprávu o neúspěšných operacích získáte pomocí následujícího příkazu:

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

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

* Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Další informace o používání protokolů aktivit k monitorování jiných typů akcí najdete v tématu [zobrazení protokolů aktivit pro správu prostředků Azure](resource-group-audit.md).
* Chcete-li před spuštěním ověřit nasazení, přečtěte si téma [nasazení skupiny prostředků pomocí šablony Azure Resource Manager](resource-group-template-deploy.md).

