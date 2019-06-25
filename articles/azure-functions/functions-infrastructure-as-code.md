---
title: Automatizace nasazování prostředků pro aplikaci funkcí ve službě Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru, která nasadí vaši aplikaci function app.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, architektura bez serverů, infrastruktury jako kódu, azure resource Manageru
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 283487eeb0f1f85940da4db8c932602e1b45efd3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695807"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizace nasazování prostředků pro vaši aplikaci funkcí ve službě Azure Functions

Šablony Azure Resource Manageru můžete nasadit aplikaci function app. Tento článek popisuje požadované prostředky a parametry to udělat. Možná budete muset nasazovat další prostředky, v závislosti na tom [aktivačními událostmi a vazbami](functions-triggers-bindings.md) ve své aplikaci function app.

Další informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

Ukázkové šablony najdete tady:
- [Aplikace funkcí v plánu Consumption]
- [Aplikace Function app na plán služby App Service]

> [!NOTE]
> Plán Premium pro hostování služby Azure Functions je aktuálně ve verzi preview. Další informace najdete v tématu [plán Premium funkce Azure](functions-premium-plan.md).

## <a name="required-resources"></a>Požadované prostředky

Nasazení služby Azure Functions se obvykle skládá z těchto zdrojů:

| Resource                                                                           | Požadavek | Informace o syntaxi a vlastnosti                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Aplikace function app                                                                     | Požaduje se    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Služby Azure Storage](../storage/index.yml) účtu                                   | Požaduje se    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) komponenty | Nepovinné    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [plán hostování](./functions-scale.md)                                             | Volitelné<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>plán hostování je pouze požadováno, když budete chtít spustit vaši aplikaci function app [plán Premium](./functions-premium-plan.md) (ve verzi preview) nebo [plán služby App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> Přestože se nevyžaduje, důrazně doporučujeme konfigurovat Application Insights pro vaši aplikaci.

<a name="storage"></a>
### <a name="storage-account"></a>Účet úložiště

Účet úložiště Azure je vyžadován pro aplikaci function app. Budete potřebovat účet pro obecné účely, který podporuje objekty BLOB, tabulky, fronty a soubory. Další informace najdete v tématu [požadavky na účet úložiště Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
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
]
```

### <a name="application-insights"></a>Application Insights

Application Insights se doporučuje pro monitorování vaší aplikace function App. Prostředek Application Insights je definován s typem **Microsoft.Insights/components** a druh **webové**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Kromě toho instrumentačním klíčem musí být zadaná pomocí funkce aplikace `APPINSIGHTS_INSTRUMENTATIONKEY` nastavení aplikace. Tato vlastnost je určena v `appSettings` kolekce `siteConfig` objektu:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plán hostování

Definice plánu hostování se liší a může být jedna z následujících akcí:
* [Plán consumption](#consumption) (výchozí)
* [Plán Premium](#premium) (ve verzi preview)
* [Plán služby App Service](#app-service-plan)

### <a name="function-app"></a>Function App

Prostředek aplikace funkcí se definuje pomocí prostředek typu **Microsoft.Web/sites** a druh **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Pokud jsou explicitně definovat plán hostování, bylo by potřeba další položky v poli dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikace function app musí obsahovat tato nastavení aplikace:

| Název nastavení                 | Popis                                                                               | Příklady hodnot                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Připojovací řetězec do úložiště účtu, který modul runtime Functions pro interní zařazení do fronty | Zobrazit [účtu úložiště](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Verze modulu runtime Azure Functions                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Zásobník jazyka, který má být použit pro funkce v této aplikaci                                   | `dotnet`, `node`, `java`, nebo `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Potřeba pouze v případě použití `node` zásobník jazyka, který určuje verze se má použít              | `10.14.1`                             |

Tyto vlastnosti jsou uvedeny v `appSettings` kolekce `siteConfig` vlastnost:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Nasazení v plánu Consumption

Plán Consumption automaticky přiděluje výpočetní výkon, pokud váš kód běží, horizontálně navýší kapacitu podle potřeby pro zpracování zátěže a pak se škáluje, když kód není spuštěný. Nemusíte platit za nečinných virtuálních počítačů a není nutné předem záložní kapacita. Další informace najdete v tématu [hostování a škálování Azure Functions](functions-scale.md#consumption-plan).

Ukázkové šablony Azure Resource Manageru, najdete v části [aplikace funkcí v plánu Consumption].

### <a name="create-a-consumption-plan"></a>Vytvoření plánu Consumption

Plán Consumption není nutné definovat. Jeden bude automaticky vytvořen nebo vybrána na základě jednotlivých oblastech při vytváření vlastního prostředku aplikace funkce.

Plán Consumption je speciální typ prostředku "serverová farma". Pro Windows, můžete je zadat pomocí `Dynamic` hodnota `computeMode` a `sku` vlastnosti:

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

> [!NOTE]
> Plán Consumption není explicitně definovat pro Linux. Vytvoří se automaticky.

Pokud váš plán consumption explicitně definovat, budete muset nastavit `serverFarmId` vlastnosti v aplikaci tak, že odkazuje na ID prostředku plánu. Měli byste zajistit, že se aplikace function app `dependsOn` nastavení pro plán i.

### <a name="create-a-function-app"></a>Vytvoření Function App

#### <a name="windows"></a>Windows

Na Windows, plánu Consumption vyžaduje další dvě nastavení v konfiguraci webu: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` a `WEBSITE_CONTENTSHARE`. Tyto vlastnosti nakonfigurovat úložiště souborů a cesta kde jsou uloženy kód aplikace funkcí a konfigurace.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

V systému Linux, musíte mít aplikaci function app jeho `kind` nastavena na `functionapp,linux`, a musí mít `reserved` nastavenou na `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Nasazení na plán Premium

Plán Premium nabízí stejné škálování jako plán consumption, ale zahrnuje vyhrazené prostředky a další možnosti. Další informace najdete v tématu [Azure Functions Premium plánování (Preview)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Vytvoření plánu Premium

Plán Premium je speciální typ prostředku "serverová farma". Můžete je zadat pomocí `EP1`, `EP2`, nebo `EP3` pro `sku` hodnotu vlastnosti.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Vytvoření Function App

Musíte mít aplikaci function app na plán Premium `serverFarmId` nastavenou na ID prostředku plánu vytvořili dříve. Kromě toho plán Premium vyžaduje další dvě nastavení v konfiguraci webu: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` a `WEBSITE_CONTENTSHARE`. Tyto vlastnosti nakonfigurovat úložiště souborů a cesta kde jsou uloženy kód aplikace funkcí a konfigurace.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Nasazení na plán služby App Service

V plánu služby App Service aplikace function app běží na vyhrazených virtuálních počítačích na Basic, Standard a SKU úrovně Premium, podobně jako webové aplikace. Podrobnosti o tom, jak funguje plán služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](../app-service/overview-hosting-plans.md).

Ukázkové šablony Azure Resource Manageru, najdete v části [aplikace Function app na plán služby App Service].

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

Plán služby App Service je definován prostředek "serverová farma".

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

Ke spuštění vaší aplikace v Linuxu, musíte taky nastavit `kind` k `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
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

Aplikace function app na plán služby App Service musí mít `serverFarmId` nastavenou na ID prostředku plánu vytvořili dříve.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linuxové aplikace by měly zahrnovat taky `linuxFxVersion` vlastnosti v části `siteConfig`. Pokud právě provádíte nasazení kódu, jako hodnota je určeno svůj zásobník modulu runtime požadované:

| Zásobník            | Příklad hodnoty                                         |
|------------------|-------------------------------------------------------|
| Python (Preview) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Pokud jste [nasazení vlastní image kontejneru](./functions-create-function-linux-custom-image.md), je nutné zadat ho s `linuxFxVersion` a nezahrnují konfiguraci, který umožňuje bitové kopie načíst, například [Web App for Containers](/azure/app-service/containers). Navíc nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` k `false`, protože obsah aplikace je k dispozici v kontejner sám o sobě:

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Vlastní nastavení nasazení

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
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
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

### <a name="deploy-using-powershell"></a>Nasazení pomocí Powershellu

Následující příkazy Powershellu vytvořte skupinu prostředků a nasazovat šablony, vytvoříte aplikaci function app se požadované prostředky. Chcete-li spustit místně, musíte mít [prostředí Azure PowerShell](/powershell/azure/install-az-ps) nainstalované. Spustit [ `Connect-AzAccount` ](/powershell/module/az.accounts/connect-azaccount) k přihlášení.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

K otestování tohoto nasazení, můžete použít [šablony, jako je ten](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) , který vytvoří aplikaci funkcí ve Windows v plánu Consumption. Nahraďte `<function-app-name>` jedinečný název pro vaši aplikaci function app.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak vyvíjet a nakonfigurovat Azure Functions.

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Jak nakonfigurovat nastavení aplikace Azure function app](functions-how-to-use-azure-function-app-settings.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikace funkcí v plánu Consumption]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikace Function app na plán služby App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
