---
title: Operace nasazení s Azure Resource Manager | Microsoft Docs
description: Popisuje postup zobrazení operace nasazení Azure Resource Manager s portálem, prostředí PowerShell, rozhraní příkazového řádku Azure a rozhraní REST API.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 26c2c333a97abff75f6b4caefb1e351dea826081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Operace nasazení zobrazení pomocí Azure Resource Manageru

Můžete zobrazit činnosti pro nasazení prostřednictvím portálu Azure. Můžete mít nejvíc zajímat zobrazení operace, když jste obdrželi chybu během nasazení, tento článek zaměřuje na zobrazení operace, které selhaly. Na portálu poskytuje rozhraní, které vám umožňuje snadno najít chyby a zjistit potenciální opravy.

Lze řešit potíže s nasazením prohlížením protokolů auditu nebo operace nasazení. Tento článek ukazuje obě metody. Nápovědu k řešení chyb při konkrétní nasazení naleznete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál
Pokud chcete zobrazit operace nasazení, použijte následující kroky:

1. Pro skupinu prostředků zahrnutých v nasazení Všimněte si, stav posledního nasazení. Můžete vybrat tento stav zobrazíte další podrobnosti.
   
    ![Stav nasazení](./media/resource-manager-deployment-operations/deployment-status.png)
2. Uvidíte nedávné historii nasazení. Vyberte nasazení, které se nezdařilo.
   
    ![Stav nasazení](./media/resource-manager-deployment-operations/select-deployment.png)
3. Vyberte na odkaz zobrazíte popis nezdaru nasazení. Na obrázku níže záznam DNS není jedinečný.  
   
    ![Zobrazit neúspěšné nasazení](./media/resource-manager-deployment-operations/view-error.png)
   
    Tato chybová zpráva by vám měly dostatečně pro vámi na zahájení odstraňování potíží. Pokud potřebujete další podrobnosti o úkoly, které byly dokončeny, můžete zobrazit činnosti, jak je znázorněno v následujícím postupu.
4. Můžete zobrazit všechny operace nasazení. Vyberte všechny operace zobrazíte další podrobnosti.
   
    ![Zobrazit operace](./media/resource-manager-deployment-operations/view-operations.png)
   
    V takovém případě uvidíte, že účet úložiště, virtuální sítě a skupina dostupnosti byly úspěšně vytvořeny. Veřejné IP adresy se nezdařilo a dalším prostředkům nebyly uplatněny.
5. Události pro nasazení můžete zobrazit výběrem **události**.
   
    ![zobrazení událostí](./media/resource-manager-deployment-operations/view-events.png)
6. Zobrazit všechny události pro nasazení a vyberte všechny další podrobnosti. Všimněte si, ID korelace. Tato hodnota může být užitečné při práci se službou technické podpory k řešení nasazení.
   
    ![Zobrazit události](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Chcete-li získat celkový stav nasazení, použijte **Get-AzureRmResourceGroupDeployment** příkaz. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Nebo můžete filtrovat výsledky pro jenom nasazení, které selhaly.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Každé nasazení obsahuje více operací. Každé operace představuje krokem v procesu nasazení. Pokud chcete zjistit, kde došlo k chybě s nasazením, budete muset obvykle zobrazit podrobnosti o operace nasazení. Zobrazí stav operace s **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Která vrací více operací s každým z nich v následujícím formátu:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Chcete-li získat další informace o neúspěšné operace, se načíst vlastnosti pro operace s **se nezdařilo** stavu.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Která vrací všechny neúspěšné operace s každým z nich v následujícím formátu:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Poznámka: serviceRequestId a trackingId pro operaci. ServiceRequestId může být užitečné při práci se službou technické podpory k řešení nasazení. Použijete trackingId a zaměřit se na konkrétní operace v dalším kroku.
4. Chcete-li získat stavové zprávy konkrétní operace se nezdařila, použijte následující příkaz:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Která vrací:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Všechny operace nasazení v Azure zahrnuje obsah žádosti a odpovědi. Obsah žádosti je, co jste poslali do Azure během nasazení (například vytvořit virtuální počítač, disk operačního systému a další prostředky). Obsah odpovědi je Azure odeslána zpět ze svého požadavku nasazení. Během nasazení, můžete použít **DeploymentDebugLogLevel** parametr k určení, že požadavku nebo odpovědi jsou uchovány v protokolu. 

  Získat tyto informace z protokolu a uložit ho místně pomocí následujících příkazů prostředí PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Získat celkový stav nasazení pomocí **skupiny azure nasazení zobrazit** příkaz.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. Jedna z hodnot vrácených je **correlationId**. Tato hodnota se používá ke sledování související události a může být užitečné při práci se službou technické podpory k řešení nasazení.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Pokud chcete zobrazit operace pro nasazení, použijte:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Získat informace o nasazení pomocí [získat informace o nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) operaci.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    V odpovědi, Všimněte si, zejména **provisioningState**, **correlationId**, a **chyba** elementy. **CorrelationId** slouží ke sledování související události a může být užitečné při práci se službou technické podpory k řešení nasazení.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Získat informace o nasazení s [výpisu všech operací nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    Odpověď obsahuje požadavku nebo odpovědi informace, které jsou založené na co jste zadali v **debugSetting** vlastnost během nasazení.

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
* Nápovědu k řešení chyb při konkrétní nasazení naleznete v tématu [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Další informace o použití protokoly aktivity monitorování jiné typy akcí, najdete v části [zobrazit protokoly aktivity ke správě prostředků Azure](resource-group-audit.md).
* K ověření vašeho nasazení, než ho spustíte, najdete v části [nasazení skupiny prostředků pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

