---
title: Operací nasazení pomocí Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje postup zobrazení operací nasazení Azure Resource Manageru pomocí portálu, Powershellu, rozhraní příkazového řádku Azure a rozhraní REST API.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 8fee1e29ab3a267d77e4e43beb2c42587da5314d
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103855"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Zobrazení operací nasazení pomocí Azure Resource Manageru

Operace pro nasazení na webu Azure portal můžete zobrazit. Bude nejvíc zajímat to sledovat operace, i když jste dojde k chybě během nasazení, tento článek se zaměřuje na zobrazení operací, které selhaly. Portál poskytuje rozhraní, které vám umožní snadno najít chyby a zjistit potenciální opravy.

Odstranění problémů s nasazením zobrazením protokolů auditu nebo operace nasazení. Tento článek ukazuje obě metody. Nápovědu k řešení chyb konkrétní nasazení, najdete v článku [řešení běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál
Pokud chcete zobrazit operací nasazení, postupujte následovně:

1. Pro skupinu prostředků zahrnutých v nasazení Všimněte si, že stav posledního nasazení. Můžete vybrat tento stav zobrazíte další podrobnosti.
   
    ![Stav nasazení](./media/resource-manager-deployment-operations/deployment-status.png)
2. Uvidíte nedávné historii nasazení. Vyberte nasazení, které se nezdařilo.
   
    ![Stav nasazení](./media/resource-manager-deployment-operations/select-deployment.png)
3. Vyberte na odkaz zobrazíte popis Proč nasazení se nezdařilo. Na obrázku níže záznam DNS není jedinečný.  
   
    ![Zobrazit neúspěšné nasazení](./media/resource-manager-deployment-operations/view-error.png)
   
    Tato chybová zpráva by vám měly dostatečně můžete začít řešit potíže. Pokud potřebujete další podrobnosti o úlohy, které byly dokončeny, operace můžete zobrazit, jak je znázorněno v následujícím postupu.
4. Zobrazí se všechny operace nasazení. Vyberte všechny operace zobrazíte další podrobnosti.
   
    ![zobrazení operací](./media/resource-manager-deployment-operations/view-operations.png)
   
    V takovém případě uvidíte, že účet úložiště, virtuální sítě a dostupnosti byly úspěšně vytvořeny. Veřejné IP adresy se nezdařilo a dalších prostředků nebyly uplatněny.
5. Události pro nasazení můžete zobrazit tak, že vyberete **události**.
   
    ![Zobrazit události](./media/resource-manager-deployment-operations/view-events.png)
6. Zobrazit všechny události pro nasazení a vyberte některou další podrobnosti. Všimněte si, že ID korelace. Tato hodnota může být užitečné při práci s technickou podporu k řešení potíží s nasazení.
   
    ![Zobrazit události](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Chcete-li získat celkový stav nasazení, použijte **Get-AzureRmResourceGroupDeployment** příkazu. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Nebo můžete filtrovat výsledky pro pouze nasazení, které selhaly.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
1. Pokud chcete získat ID korelace, použijte:

  ```powershell
  (Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
  ```

1. Každé nasazení obsahuje více operací. Každá operace představuje krok v procesu nasazení. Pokud chcete zjistit, co se nepovedlo s nasazením, obvykle musíte zobrazíte podrobnosti o operací nasazení. Zobrazí se stav operací s **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Který vrátí více operací s každé z nich v následujícím formátu:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

1. Pokud chcete získat další podrobnosti o neúspěšných operacích, načíst vlastnosti pro operace s **neúspěšné** stavu.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Který vrátí všechny neúspěšné operace se každé z nich v následujícím formátu:

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

    Mějte na paměti, serviceRequestId a trackingId pro operaci. ServiceRequestId může být užitečné při práci s technickou podporu k řešení potíží s nasazení. Použijete v dalším kroku trackingId zaměřit na konkrétní operace.
1. Chcete-li získat stavové zprávy konkrétní neúspěšnou operaci, použijte následující příkaz:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Který vrátí:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
1. Každé operaci nasazení v Azure zahrnuje obsah požadavku a odpovědi. Obsah požadavku je, co jste odeslali do Azure během nasazení (například vytvořit virtuální počítač, disk s operačním systémem a dalším prostředkům). Obsah odpovědi je, co Azure odesílá zpět z požadavku nasazení. Během nasazení, můžete použít **DeploymentDebugLogLevel** parametr k určení, že žádost nebo odpověď jsou zachovány v protokolu. 

  Získání těchto informací z protokolu a uložte ho místně pomocí následujících příkazů Powershellu:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Získat celkový stav nasazení se **azure skupiny nasazení zobrazit** příkazu.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. Jedním z vrácených hodnot je **correlationId**. Tato hodnota se používá ke sledování souvisejících událostí a může být užitečné při práci s technickou podporu k řešení potíží s nasazení.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Pokud chcete zobrazit operace pro nasazení, použijte:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Získejte informace o nasazení [získat informace o nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) operace.

  ```http
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

2. Získejte informace o nasazení s [výpisu všech operací nasazení šablony](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
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

