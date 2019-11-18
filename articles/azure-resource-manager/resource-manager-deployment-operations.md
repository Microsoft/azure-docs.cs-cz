---
title: Historie nasazení
description: Popisuje postup zobrazení Azure Resource Manager operací nasazení pomocí portálu, PowerShellu, rozhraní příkazového řádku Azure a REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149568"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Zobrazit historii nasazení pomocí Azure Resource Manager

Azure Resource Manager vám umožní zobrazit historii nasazení a prozkoumávat konkrétní operace v předchozích nasazeních. Můžete zobrazit nasazené prostředky a získat informace o případných chybách.

Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál

Pro získání podrobností o nasazení z historie nasazení.

1. Vyberte skupinu prostředků, kterou chcete prošetřit.

1. Vyberte odkaz v části **nasazení**.

   ![Vybrat historii nasazení](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. V historii nasazení vyberte jedno z nasazení.

   ![Vybrat nasazení](./media/resource-manager-deployment-operations/select-details.png)

1. Zobrazí se souhrn nasazení, včetně seznamu nasazených prostředků.

    ![Souhrn nasazení](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Pokud chcete zobrazit šablonu, která se používá pro nasazení, vyberte **šablonu**. Šablonu si můžete stáhnout a znovu použít.

    ![Zobrazit šablonu](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Pokud se nasazení nepovedlo, zobrazí se chybová zpráva. Pro další podrobnosti vyberte chybovou zprávu.

    ![Zobrazit neúspěšné nasazení](./media/resource-manager-deployment-operations/show-error.png)

1. Zobrazí se podrobná chybová zpráva.

    ![Zobrazit podrobnosti o chybě](./media/resource-manager-deployment-operations/show-details.png)

1. ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporou pro řešení potíží s nasazením.

    ![Získat ID korelace](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Pokud se chcete dozvědět víc o kroku, který selhal, vyberte **Podrobnosti operace**.

    ![Vybrat operace nasazení](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Zobrazí se podrobnosti o tomto kroku nasazení.

    ![Zobrazit podrobnosti o operaci](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Celkový stav nasazení získáte pomocí příkazu **Get-AzResourceGroupDeployment** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Nebo můžete filtrovat výsledky jenom pro nasazení, která se nezdařila.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporou pro řešení potíží s nasazením. ID korelace získáte pomocí:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Každé nasazení zahrnuje více operací. Každá operace představuje krok v procesu nasazení. Chcete-li zjistit, co se stalo s nasazením, je obvykle nutné zobrazit podrobnosti o operacích nasazení. Stav operací můžete zobrazit pomocí **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Který vrací více operací s každým z nich v následujícím formátu:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Chcete-li získat další podrobnosti o neúspěšných operacích, načtěte vlastnosti pro operace se stavem **selhání** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Vrátí všechny neúspěšné operace s každým z nich v následujícím formátu:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Poznamenejte si serviceRequestId a trackingId pro operaci. ServiceRequestId může být užitečné při práci s technickou podporou pro řešení potíží s nasazením. V dalším kroku použijete trackingId, abyste se mohli zaměřit na konkrétní operaci.

Chcete-li získat stavovou zprávu určité operace, která selhala, použijte následující příkaz:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Který vrátí:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Každá operace nasazení v Azure zahrnuje obsah požadavků a odpovědí. Během nasazování můžete použít parametr **DeploymentDebugLogLevel** a určit tak, že žádost a odpověď jsou protokolovány.

Tyto informace získáte z protokolu a uložíte je místně pomocí následujících příkazů PowerShellu:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete získat celkový stav nasazení, použijte příkaz pro **zobrazení skupiny Azure Group Deployment** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporou pro řešení potíží s nasazením.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Pokud chcete zobrazit operace nasazení, použijte:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Chcete-li získat informace o nasazení, použijte příkaz [získat informace o operaci nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

V odpovědi si všimněte zejména prvků **provisioningState**, **ID korelace**a **Error** . **ID korelace** se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporou pro řešení potíží s nasazením.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Chcete-li získat informace o nasazeních, použijte [seznam všech operací nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Odpověď zahrnuje informace o požadavku a/nebo odpovědi na základě toho, co jste zadali ve vlastnosti **debugSetting** během nasazování.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Další kroky
* Nápovědu k řešení konkrétních chyb nasazení najdete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Další informace o používání protokolů aktivit k monitorování jiných typů akcí najdete v tématu [zobrazení protokolů aktivit pro správu prostředků Azure](resource-group-audit.md).
* Chcete-li před spuštěním ověřit nasazení, přečtěte si téma [nasazení skupiny prostředků pomocí šablony Azure Resource Manager](resource-group-template-deploy.md).

