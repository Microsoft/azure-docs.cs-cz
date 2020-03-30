---
title: Automatizace nasazení prostředků aplikace pro funkce do Azure
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru, která nasazuje vaši aplikaci funkcí.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276891"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizace nasazení prostředků pro vaši funkční aplikaci ve službě Azure Functions

K nasazení aplikace pro funkce můžete použít šablonu Azure Resource Manageru. Tento článek popisuje požadované prostředky a parametry pro to. Možná budete muset nasadit další prostředky, v závislosti na [aktivační události a vazby](functions-triggers-bindings.md) v aplikaci funkce.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md).

Ukázkové šablony naleznete v tématu:
- [Aplikace Funkce v plánu spotřeby]
- [Funkční aplikace v plánu služby Azure App Service]

## <a name="required-resources"></a>Požadované zdroje

Nasazení Azure Functions se obvykle skládá z těchto prostředků:

| Prostředek                                                                           | Požadavek | Odkaz na syntaxi a vlastnosti                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Aplikace pro funkce                                                                     | Požaduje se    | [Web/weby společnosti Microsoft](/azure/templates/microsoft.web/sites)                             |   |
| Účet [úložiště Azure](../storage/index.yml)                                   | Požaduje se    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Součást [Application Insights](../azure-monitor/app/app-insights-overview.md) | Nepovinné    | [Microsoft.Insights/komponenty](/azure/templates/microsoft.insights/components)         |   |
| [Hostingový plán](./functions-scale.md)                                             | Volitelně<sup>1</sup>    | [Farmy Microsoft.Web/server](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1.</sup> Hostingový plán je vyžadován pouze v případě, že se rozhodnete spustit aplikaci funkcí v [plánu Premium](./functions-premium-plan.md) (ve verzi Preview) nebo v [plánu služby App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> I když to není nutné, důrazně doporučujeme nakonfigurovat Application Insights pro vaši aplikaci.

<a name="storage"></a>
### <a name="storage-account"></a>Účet úložiště

Pro aplikaci funkce je vyžadován účet úložiště Azure. Potřebujete účet pro obecné účely, který podporuje objekty BLOB, tabulky, fronty a soubory. Další informace najdete v tématu [požadavky na účet úložiště Azure Functions](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Kromě toho musí `AzureWebJobsStorage` být vlastnost zadána jako nastavení aplikace v konfiguraci webu. Pokud aplikace funkce nepoužívá Application Insights pro monitorování, `AzureWebJobsDashboard` měla by také určit jako nastavení aplikace.

Runtime Azure Functions `AzureWebJobsStorage` používá připojovací řetězec k vytvoření vnitřních front.  Pokud application insights není povolena, `AzureWebJobsDashboard` runtime používá připojovací řetězec k protokolování do úložiště Azure Table a napájení **monitor** kartu na portálu.

Tyto vlastnosti jsou `appSettings` určeny `siteConfig` v kolekci v objektu:

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

Application Insights se doporučuje pro sledování aplikací funkcí. Prostředek Application Insights je definován s typem **Microsoft.Insights/components** a **druhweb**:

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Kromě toho musí být klíč instrumentace poskytnut aplikaci `APPINSIGHTS_INSTRUMENTATIONKEY` funkce pomocí nastavení aplikace. Tato vlastnost je `appSettings` určena v `siteConfig` kolekci v objektu:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hostingový plán

Definice hostingového plánu se liší a může být jedna z následujících možností:
* [Plán spotřeby](#consumption) (výchozí)
* [Prémiový plán](#premium) (ve verzi Preview)
* [Plán služby App Service](#app-service-plan)

### <a name="function-app"></a>Function App

Prostředek aplikace funkce je definován pomocí prostředku typu **Microsoft.Web/sites** a kind **functionapp**:

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
}
```

> [!IMPORTANT]
> Pokud explicitně definujete plán hostování, bude potřeba další položka v poli dependsOn:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikace funkce musí obsahovat tato nastavení aplikace:

| Název nastavení                 | Popis                                                                               | Příklady hodnot                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Připojovací řetězec k účtu úložiště, který runtime Funkce používá pro interní fronty | Viz [Účet úložiště](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Verze runtime Funkce Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Zásobník jazyků, který se má použít pro funkce v této aplikaci                                   | `dotnet`, `node` `java`, `python`, , nebo`powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Je potřeba pouze `node` v případě, že používáte zásobník jazyka, určuje verzi, která má být              | `10.14.1`                             |

Tyto vlastnosti jsou `appSettings` určeny `siteConfig` v kolekci ve vlastnosti:

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

## <a name="deploy-on-consumption-plan"></a>Plán nasazení při spotřebě

Plán spotřeby automaticky přiděluje výpočetní výkon při spuštění kódu, škáluje podle potřeby pro zpracování zatížení a pak se škáluje, když kód není spuštěn. Nemusíte platit za nečinné virtuální chody a nemusíte předem rezervovat kapacitu. Další informace najdete v [tématu Škálování funkcí Azure a hostování](functions-scale.md#consumption-plan).

Ukázková šablona Azure Resource Manager uvidíte [v tématu Aplikace Function v plánu spotřeby].

### <a name="create-a-consumption-plan"></a>Vytvoření plánu spotřeby

Plán spotřeby nemusí být definován. Jeden bude automaticky vytvořen nebo vybrán na základě pro-oblast při vytváření samotného prostředku aplikace funkce.

Plán spotřeby je zvláštní typ prostředku serverové farmy. Pro systém Windows jej můžete `Dynamic` zadat pomocí `computeMode` `sku` hodnoty vlastností a:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Plán spotřeby nelze explicitně definovat pro Linux. Bude vytvořen automaticky.

Pokud explicitně definujete plán spotřeby, budete `serverFarmId` muset nastavit vlastnost v aplikaci tak, aby nastoňovala na ID prostředku plánu. Měli byste se ujistit, `dependsOn` že aplikace funkce má nastavení pro plán také.

### <a name="create-a-function-app"></a>Vytvoření Function App

#### <a name="windows"></a>Windows

Plán spotřeby v systému Windows vyžaduje dvě `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` další `WEBSITE_CONTENTSHARE`nastavení v konfiguraci lokality: a . Tyto vlastnosti nakonfigurují účet úložiště a cestu k souboru, kde jsou uloženy kód aplikace funkce a konfigurace.

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

V Systému Linux musí `kind` mít `functionapp,linux`aplikace funkce nastavenou `reserved` na `true`a musí mít vlastnost nastavenou na :

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

## <a name="deploy-on-premium-plan"></a>Nasazení v plánu Premium

Plán Premium nabízí stejné škálování jako plán Spotřeba, ale obsahuje vyhrazené prostředky a další možnosti. Další informace najdete v [tématu Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Vytvoření prémiového tarifu

Plán Premium je zvláštní typ prostředku serverové farmy. Můžete ji zadat pomocí `EP1` `EP2`aplikace `EP3` , `Name` nebo hodnoty `sku` vlastnosti v [objektu popisu](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Vytvoření Function App

Aplikace funkce v plánu Premium `serverFarmId` musí mít vlastnost nastavenou na ID prostředku plánu vytvořeného dříve. Kromě toho plán Premium vyžaduje dvě další nastavení `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`v konfiguraci lokality: a . Tyto vlastnosti nakonfigurují účet úložiště a cestu k souboru, kde jsou uloženy kód aplikace funkce a konfigurace.

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

## <a name="deploy-on-app-service-plan"></a>Nasazení v plánu služby App Service

V plánu služby App Service se vaše aplikace funkcí spouští na vyhrazených virtuálních počítačích na základních, standardních a prémiových virtuálních počítačích, podobně jako webové aplikace. Podrobnosti o tom, jak plán služby App Service funguje, najdete v tématu [plány azure app service podrobný přehled](../app-service/overview-hosting-plans.md).

Ukázková šablona Azure Resource Manager uvidíte v [tématu Function app on Azure App Service plan].

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

Plán služby App Service je definován prostředkem serverové farmy.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Chcete-li aplikaci spustit v Systému `Linux`Linux, musíte také nastavit hodnotu `kind` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Vytvoření Function App

Aplikace funkce v plánu služby `serverFarmId` App Service musí mít vlastnost nastavenou na ID prostředku plánu vytvořeného dříve.

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

Linuxové aplikace by `linuxFxVersion` měly také obsahovat vlastnost v části `siteConfig`. Pokud právě nasazujete kód, hodnota pro to to je určena požadovaný zásobník runtime:

| Zásobník            | Příklad hodnoty                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

Pokud [nasazujete vlastní bitovou kopii kontejneru](./functions-create-function-linux-custom-image.md), musíte ji zadat `linuxFxVersion` pomocí konfigurace a zahrnout ji, která umožní vytažení bitové kopie, jako ve Webové [aplikaci pro kontejnery](/azure/app-service/containers). Taky nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`na , protože obsah aplikace je k dispozici v samotném kontejneru:

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

## <a name="customizing-a-deployment"></a>Přizpůsobení nasazení

Aplikace funkce má mnoho podřízených prostředků, které můžete použít ve vašem nasazení, včetně nastavení aplikace a možností správy zdrojového kódu. Můžete také odebrat **zdrojové ovládací prvky** podřízeného prostředku a místo toho použít jinou [možnost nasazení.](functions-continuous-deployment.md)

> [!IMPORTANT]
> Chcete-li úspěšně nasadit aplikaci pomocí Azure Resource Manager, je důležité pochopit, jak se prostředky nasazují v Azure. V následujícím příkladu jsou konfigurace nejvyšší úrovně použity pomocí **siteConfig**. Je důležité nastavit tyto konfigurace na nejvyšší úrovni, protože předávají informace modulu runtime a nasazení funkce. Informace nejvyšší úrovně je vyžadována před použitím **podřízených zdrojových ovládacích prvků nebo webového** prostředku. I když je možné nakonfigurovat tato nastavení v prostředku **konfigurace/nastavení podřízené** úrovně, v některých případech musí být aplikace funkce nasazena *před* použitím **konfigurace/appSettings.** Například pokud používáte funkce s [Logic Apps](../logic-apps/index.yml), vaše funkce jsou závislost jiného prostředku.

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
> Tato šablona používá hodnotu nastavení aplikace [Project,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) která nastavuje základní adresář, ve kterém modul pro nasazení funkce (Kudu) hledá nasaditelný kód. V našem úložišti jsou naše funkce v podsložce složky **src.** Takže v předchozím příkladu jsme nastavili hodnotu nastavení aplikace na `src`. Pokud jsou vaše funkce v kořenovém adresáři úložiště nebo pokud nenasazujete ze správy zdrojového kódu, můžete tuto hodnotu nastavení aplikace odebrat.

## <a name="deploy-your-template"></a>Nasazení šablony

K nasazení šablony můžete použít některý z následujících způsobů:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Portál Azure](../azure-resource-manager/templates/deploy-portal.md)
* [ROZHRANÍ API PRO ODPOČINEK](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Tlačítko Nasazení do Azure

Nahraďte ```<url-encoded-path-to-azuredeploy-json>``` url [kódovanou](https://www.bing.com/search?q=url+encode) verzí nezpracované `azuredeploy.json` cesty souboru v GitHubu.

Zde je příklad, který používá markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Zde je příklad, který používá HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Nasazení pomocí PowerShellu

Následující příkazy Prostředí PowerShell vytvoří skupinu prostředků a nasadí šablonu, která vytvoří aplikaci funkcí s požadovanými prostředky. Chcete-li spustit místně, musíte mít [nainstalovaný Azure PowerShell.](/powershell/azure/install-az-ps) Spustit [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) a přihlásit se.

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

Chcete-li otestovat toto nasazení, můžete použít šablonu, jako je [tato,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) která vytvoří aplikaci funkce ve Windows v plánu spotřeby. Nahraďte `<function-app-name>` jedinečnýnázev aplikace funkce.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak vyvíjet a konfigurovat funkce Azure.

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Jak nakonfigurovat nastavení aplikace pro funkce Azure](functions-how-to-use-azure-function-app-settings.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikace Funkce v plánu spotřeby]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Funkční aplikace v plánu služby Azure App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
