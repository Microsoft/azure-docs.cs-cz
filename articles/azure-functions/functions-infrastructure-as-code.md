---
title: Automatizace nasazování prostředků pro aplikaci funkcí ve službě Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru, která nasadí vaši aplikaci function app.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, architektura bez serverů, infrastruktury jako kódu, azure resource Manageru
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 488b3797c7e18855a60b84a77a05e4e0a5654475
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023653"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizace nasazování prostředků pro vaši aplikaci funkcí ve službě Azure Functions

Šablony Azure Resource Manageru můžete nasadit aplikaci function app. Tento článek popisuje požadované prostředky a parametry to udělat. Možná budete muset nasazovat další prostředky, v závislosti na tom [aktivačními událostmi a vazbami](functions-triggers-bindings.md) ve své aplikaci function app.

Další informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

Ukázkové šablony najdete tady:
- [Aplikace funkcí v plánu Consumption]
- [Aplikace Function app na plán služby App Service]

## <a name="required-resources"></a>Požadované prostředky

Aplikace function app vyžaduje tyto prostředky:

* [Služby Azure Storage](../storage/index.yml) účtu
* Plán hostování (plán Consumption a plán služby App Service)
* Aplikace function app 

Syntaxi JSON a vlastnosti pro tyto prostředky naleznete v tématu:

* [Microsoft.Storage/storageAccounts.](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)

### <a name="storage-account"></a>Účet úložiště

Účet úložiště Azure je vyžadován pro aplikaci function app. Budete potřebovat účet pro obecné účely, který podporuje objekty BLOB, tabulky, fronty a soubory. Další informace najdete v tématu [požadavky na účet úložiště Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Kromě toho, vlastnost `AzureWebJobsStorage` musí být zadán jako nastavení aplikace v konfiguraci webu. Pokud aplikace function app nepoužívá Application Insights pro monitorování, měla by také obsahovat `AzureWebJobsDashboard` jako nastavení aplikace.

Modul runtime Azure Functions používá `AzureWebJobsStorage` připojovací řetězec k vytvoření interní fronty.  Pokud služba Application Insights není povolená, modul runtime používá `AzureWebJobsDashboard` připojovací řetězec k přihlášení k Azure Table storage a power **monitorování** karta na portálu.

Tyto vlastnosti jsou uvedeny v `appSettings` kolekce `siteConfig` objektu:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plán hostování

Definice plánu hostování se liší v závislosti na tom, jestli používáte plán Consumption nebo služby App Service. Zobrazit [nasazení aplikace funkcí v plánu Consumption](#consumption) a [nasadit aplikaci function app na plán služby App Service](#app-service-plan).

### <a name="function-app"></a>Function App

Prostředek aplikace funkcí se definuje pomocí prostředek typu **Microsoft.Web/Site** a druh **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Nasazení aplikace funkcí v plánu Consumption

Aplikace function app můžete spouštět ve dvou různých režimech: plán Consumption a plán služby App Service. Plán Consumption automaticky přiděluje výpočetní výkon, pokud váš kód běží, horizontálně navýší kapacitu podle potřeby pro zpracování zátěže a pak se škáluje, když kód není spuštěný. Ano nemusíte platit za nečinných virtuálních počítačů a není nutné předem záložní kapacita. Další informace o plánech hostování najdete v tématu [plány Azure Functions Consumption a App Service](functions-scale.md).

Ukázkové šablony Azure Resource Manageru, najdete v části [aplikace funkcí v plánu Consumption].

### <a name="create-a-consumption-plan"></a>Vytvoření plánu Consumption

Plán Consumption je speciální typ prostředku "serverová farma". Zadat pomocí `Dynamic` hodnota `computeMode` a `sku` vlastnosti:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Vytvoření Function App

Kromě toho plán Consumption vyžaduje další dvě nastavení v konfiguraci webu: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` a `WEBSITE_CONTENTSHARE`. Tyto vlastnosti nakonfigurovat úložiště souborů a cesta kde jsou uloženy kód aplikace funkcí a konfigurace.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Nasazení aplikace funkcí v plánu služby App Service

V plánu služby App Service aplikace function app běží na vyhrazených virtuálních počítačích na Basic, Standard a SKU úrovně Premium, podobně jako webové aplikace. Podrobnosti o tom, jak funguje plán služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](../app-service/overview-hosting-plans.md). 

Ukázkové šablony Azure Resource Manageru, najdete v části [aplikace Function app na plán služby App Service].

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Vytvoření Function App 

Po výběru škálování možnost vytvoření aplikace function app. Aplikace je kontejner, který obsahuje všechny funkce.

Aplikace function app má mnoho podřízené prostředky, které můžete použít ve vašem nasazení, včetně nastavení aplikace a možností správy zdrojového kódu. Můžete také zvolit odebrat **sourcecontrols** podřízený prostředek a použijte jiný [možnost nasazení](functions-continuous-deployment.md) místo.

> [!IMPORTANT]
> K úspěšnému nasazení vaší aplikace pomocí Azure Resource Manageru, je důležité pochopit, jak jsou prostředky nasazené v Azure. V následujícím příkladu je použita nejvyšší úrovně konfigurace s použitím **siteConfig**. Je důležité nastavit tyto konfigurace na nejvyšší úrovni, protože sdělují informace k modulu runtime a nasazení funkce. Nejvyšší úrovně je nutné znát před podřízeným objektem **sourcecontrols nebo webová** je zdroj aplikován. I když je možné nakonfigurovat tato nastavení na podřízené úrovni **config/appSettings** prostředků, v některých případech se musí nasadit vaši aplikaci function app *před* **config/appSettings**  platí. Například při použití funkce s [Logic Apps](../logic-apps/index.yml), vaše funkce se závislost jiný prostředek.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Tato šablona používá [projektu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) hodnotu nastavení aplikace, která nastaví základního adresáře, ve kterém funkce modul pro nasazení (Kudu) vyhledá nasaditelný kód. V našem úložišti naše funkce jsou v podsložce **src** složky. Ano, v předchozím příkladu nastavíme hodnotu nastavení aplikace na `src`. Pokud vaše funkce jsou v kořenovém adresáři úložiště nebo nenasazujete ze správy zdrojového kódu, můžete odebrat tuto hodnotu nastavení aplikace.

## <a name="deploy-your-template"></a>Nasazení šablony

Můžete použít některý z následujících způsobů k nasazení vaší šablony:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Tlačítko nasazení do Azure

Nahraďte ```<url-encoded-path-to-azuredeploy-json>``` s [kódovaná adresou URL](https://www.bing.com/search?q=url+encode) verzi nezpracovaná cesta k vaší `azuredeploy.json` souboru na Githubu.

Tady je příklad, který používá markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Tady je příklad, který využívá HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Další postup

Další informace o tom, jak vyvíjet a nakonfigurovat Azure Functions.

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Jak nakonfigurovat nastavení aplikace Azure function app](functions-how-to-use-azure-function-app-settings.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikace funkcí v plánu Consumption]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikace Function app na plán služby App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
