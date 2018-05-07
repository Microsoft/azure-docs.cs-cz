---
title: Vytvoření aplikace logiky ze šablon Azure Resource Manager | Microsoft Docs
description: Vytvoření a nasazení pracovní postupy aplikace logiky pomocí šablony Azure Resource Manager
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 69be968f34d756cc725f5353dcf074c01fad65ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Vytvoření a nasazení aplikací logiky pomocí šablony Azure Resource Manager

Azure Logic Apps poskytuje šablony Azure Resource Manager, které můžete použít nejen k vytvoření aplikace logiky pro automatizaci pracovních postupů, ale také kvůli definice prostředků a parametry, které se používají pro nasazení. Můžete tuto šablonu použít pro vaše vlastní obchodní scénáře nebo si přizpůsobit šablonu, aby vyhovovalo vašim požadavkům. Další informace o [šablony Resource Manageru pro logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) a [strukturu šablony Azure Resource Manager a syntaxe](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definování aplikace logiky

Tuto definici aplikace logiky příklad spouští jednou za hodinu a odešle příkaz ping umístění zadané v `testUri` parametr.
Šablona používá hodnoty parametrů pro název aplikace logiky (```logicAppName```) a umístění na příkaz ping pro testování (```testUri```). Další informace o [definování těchto parametrů v šabloně](#define-parameters). Šablona taky Nastaví umístění pro aplikaci logiky do stejného umístění jako skupina prostředků Azure. 

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

### <a name="define-parameters"></a>Definujte parametry

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Zde je uveden popis pro parametry v šabloně:

| Parametr | Popis | Příklad definici JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Definuje název aplikace logiky Tato šablona vytvoří. | "logicAppName": {"typ": "řetězec", "metadata": {"Popis": "myExampleLogicAppName"}} |
| `testUri` | Definuje umístění na příkaz ping pro testování. | "testUri": {"typ": "řetězec", "Výchozí": "http://azure.microsoft.com/status/feed/"} | 
||||

Další informace o [REST API pro definici aplikace logiky pracovního postupu a vlastnosti](https://docs.microsoft.com/rest/api/logic/workflows) a [vychází definice aplikace logiky s JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Automatické nasazení aplikací logiky

Chcete-li vytvořit a automaticky nasadit aplikaci logiky do Azure, zvolte **nasadit do Azure** tady:

[![Nasazení do Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Tato akce se přihlásí můžete k portálu Azure, kde můžete poskytnout logiky aplikace podrobnosti a proveďte požadované změny do šablony nebo parametry. Například portál Azure vás vyzve k zadání tyto podrobnosti:

* Název předplatného Azure
* Skupinu prostředků, kterou chcete použít
* Umístění aplikace logiky
* Název pro svou aplikaci logiky
* Identifikátor URI testu
* Přijetí zadané podmínky a ujednání

## <a name="deploy-logic-apps-with-commands"></a>Nasazení aplikací logiky pomocí příkazů

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