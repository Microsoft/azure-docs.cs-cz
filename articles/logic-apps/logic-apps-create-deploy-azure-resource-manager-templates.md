---
title: Vytváření aplikací logiky ze šablon Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvoření a nasazení pracovních postupů aplikace logiky pomocí šablony Azure Resource Manageru
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0772ed0e6cca98c4e59b563a23549909636d55d0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38572616"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Vytvoření a nasazení aplikací logiky s využitím šablon Azure Resource Manageru

Služba Azure Logic Apps poskytuje šablony Azure Resource Manageru, které můžete použít nejen k vytváření aplikací logiky pro automatizaci pracovních postupů, ale také k definování prostředky a parametry, které se používají pro nasazení. Můžete použít tuto šablonu pro vaše vlastní obchodní scénáře nebo šablonu přizpůsobit, aby splňovaly vaše požadavky. Další informace o [šablony Resource Manageru pro logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) a [strukturu šablony Azure Resource Manageru a syntaxe](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definice aplikace logiky

Tento příklad definice aplikace logiky spouští jednou za hodinu a odešle příkaz ping umístění zadaném v `testUri` parametru.
Šablona používá hodnoty parametrů pro název aplikace logiky (```logicAppName```) a umístění na příkaz ping pro testování (```testUri```). Další informace o [definování tyto parametry v šabloně](#define-parameters). Šablona se nastaví také umístění pro aplikaci logiky do stejného umístění jako skupina prostředků Azure. 

``` json
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2016-06-01",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "tags": {
      "displayName": "LogicApp"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
              "type": "Http",
              "inputs": {
                  "method": "GET",
                  "uri": "@parameters('testUri')"
              },
              "runAfter": {}
           }
         },
         "outputs": {}
      },
      "parameters": {}
   }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Definovat parametry

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Tady jsou popisy parametrů v šabloně:

| Parametr | Popis | Příklad definice JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Definuje název aplikace logiky se tato šablona vytvoří. | "logicAppName": {"type": "string", "metadat": {"Popis": "myExampleLogicAppName"}} |
| `testUri` | Definuje umístění na příkaz ping pro testování. | "testUri": {"type": "string", "Výchozí": "http://azure.microsoft.com/status/feed/"} | 
||||

Další informace o [rozhraní REST API pro definici pracovního postupu aplikace logiky a vlastnosti](https://docs.microsoft.com/rest/api/logic/workflows) a [vytváření definic aplikací logiky pomocí kódu JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Automatické nasazení aplikací logiky

Chcete-li vytvořit a automaticky nasazovat aplikace logiky do Azure, zvolte **nasadit do Azure** tady:

[![Nasazení do Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Tato akce přihlášení k webu Azure portal, kde zadejte podrobnosti aplikace logiky a provést změny na šablonu nebo parametry. Například na webu Azure portal vás vyzve k zadání tyto podrobnosti:

* Název předplatného Azure
* Skupinu prostředků, kterou chcete použít
* Umístění aplikace logiky
* Název aplikace logiky
* Test identifikátoru URI
* Přijetí zadané podmínky a ujednání

## <a name="deploy-logic-apps-with-commands"></a>Nasazení aplikací logiky s příkazy

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Monitorování Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)