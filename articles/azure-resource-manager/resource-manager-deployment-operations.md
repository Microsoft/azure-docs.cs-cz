---
title: Historie nasazení pomocí Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje postup zobrazení operací nasazení Azure Resource Manageru pomocí portálu, Powershellu, rozhraní příkazového řádku Azure a rozhraní REST API.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606024"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Zobrazit historii nasazení pomocí Azure Resource Manageru

Azure Resource Manager vám umožňuje zobrazit historii nasazení a zkontrolovat konkrétní operace v posledních nasazení. Můžete zobrazit prostředky, které se nasadily a získat informace o případných chybách.

Nápovědu k řešení chyb konkrétní nasazení, najdete v článku [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál

Chcete-li získat podrobné informace o nasazení z historie nasazení.

1. Vyberte skupinu prostředků, které chcete prověřit.

1. Vyberte odkaz pod **nasazení**.

   ![Vyberte historie nasazení](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Vyberte jedno z nasazení z historie nasazení.

   ![Výběr možnosti nasazení](./media/resource-manager-deployment-operations/select-details.png)

1. Zobrazí se souhrn nasazení, včetně seznamu prostředků, které se nasadily.

    ![Shrnutí nasazení](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Chcete-li zobrazit Šablona použitá pro nasazení, vyberte **šablony**. Můžete stáhnout šablonu, kterou chcete znovu použít.

    ![Zobrazit šablonu](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Pokud vaše nasazení se nezdařilo, zobrazí chybová zpráva. Výběr chybové zprávy pro další podrobnosti.

    ![Zobrazit neúspěšné nasazení](./media/resource-manager-deployment-operations/show-error.png)

1. Podrobná chybová zpráva.

    ![Zobrazit podrobnosti o chybě](./media/resource-manager-deployment-operations/show-details.png)

1. ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporu k řešení potíží s nasazení.

    ![Získejte ID korelace](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Další informace o kroku, který selhal, vyberte **podrobnosti o operaci**.

    ![Vyberte operace nasazení](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Zobrazí podrobnosti pro daný krok nasazení.

    ![Zobrazit podrobnosti o operaci](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li získat celkový stav nasazení, použijte **Get-AzResourceGroupDeployment** příkazu.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Nebo můžete filtrovat výsledky pro pouze nasazení, které selhaly.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporu k řešení potíží s nasazení. Pokud chcete získat ID korelace, použijte:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Každé nasazení obsahuje více operací. Každá operace představuje krok v procesu nasazení. Pokud chcete zjistit, co se nepovedlo s nasazením, obvykle musíte zobrazíte podrobnosti o operací nasazení. Zobrazí se stav operací s **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Který vrátí více operací s každé z nich v následujícím formátu:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Pokud chcete získat další podrobnosti o neúspěšných operacích, načíst vlastnosti pro operace s **neúspěšné** stavu.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Který vrátí všechny neúspěšné operace se každé z nich v následujícím formátu:

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

Mějte na paměti, serviceRequestId a trackingId pro operaci. ServiceRequestId může být užitečné při práci s technickou podporu k řešení potíží s nasazení. Použijete v dalším kroku trackingId zaměřit na konkrétní operace.

Chcete-li získat stavové zprávy konkrétní neúspěšnou operaci, použijte následující příkaz:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Který vrátí:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Každé operaci nasazení v Azure zahrnuje obsah požadavku a odpovědi. Během nasazení, můžete použít **DeploymentDebugLogLevel** parametr k určení, že se protokolují požadavků a odpovědí.

Získání těchto informací z protokolu a uložte ho místně pomocí následujících příkazů Powershellu:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Chcete-li získat celkový stav nasazení, použijte **azure skupiny nasazení zobrazit** příkazu.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
ID korelace se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporu k řešení potíží s nasazení.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Pokud chcete zobrazit operace pro nasazení, použijte:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Chcete-li získat informace o nasazení, použijte [získat informace o nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments) operace.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

V odpovědi, Všimněte si zejména **provisioningState**, **correlationId**, a **chyba** elementy. **CorrelationId** se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporu k řešení potíží s nasazení.

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

Chcete-li získat informace o nasazení, použijte [výpisu všech operací nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Odpověď obsahuje požadavek nebo odpověď informace v závislosti na zadané v **debugSetting** vlastnost během nasazení.

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

## <a name="next-steps"></a>Další postup
* Nápovědu k řešení chyb konkrétní nasazení, najdete v článku [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Další informace o použití protokolů aktivit pro ostatní typy akcí monitorování, najdete v článku [protokolů aktivit při správě prostředků Azure](resource-group-audit.md).
* Ověření nasazení před spuštěním ho, naleznete v tématu [nasazení skupiny prostředků pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

