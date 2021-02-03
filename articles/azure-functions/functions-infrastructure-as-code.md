---
title: Automatizace nasazení prostředků aplikace Function App do Azure
description: Naučte se, jak vytvořit šablonu Azure Resource Manager, která nasadí vaši aplikaci Function App.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9df4c62a65fd133c6ea8dc84e33d7c7b02d94cbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494035"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizace nasazení prostředků pro aplikaci Function App v Azure Functions

K nasazení aplikace Function App můžete použít šablonu Azure Resource Manager. V tomto článku najdete informace o požadovaných prostředcích a parametrech. Možná budete muset nasadit další prostředky v závislosti na [triggerech a vazbách](functions-triggers-bindings.md) ve vaší aplikaci Function App.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md).

Ukázkové šablony naleznete zde:
- [Aplikace Function App v plánu spotřeby]
- [Aplikace Function App v plánu Azure App Service]

## <a name="required-resources"></a>Požadované prostředky

Nasazení Azure Functions se typicky skládá z těchto prostředků:

| Prostředek                                                                           | Požadavek | Reference k syntaxi a vlastnostem                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Aplikace Function App                                                                     | Vyžadováno    | [Microsoft. Web/weby](/azure/templates/microsoft.web/sites)                             |
| Účet [Azure Storage](../storage/index.yml)                                   | Vyžadováno    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Komponenta [Application Insights](../azure-monitor/app/app-insights-overview.md) | Volitelné    | [Microsoft. Insights/Components](/azure/templates/microsoft.insights/components)         |
| [Plán hostování](./functions-scale.md)                                             | Volitelné<sup>1</sup>    | [Microsoft. Web/serverových farem](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Plán hostování se vyžaduje jenom v případě, že se rozhodnete spustit aplikaci Function App na [plánu Premium](./functions-premium-plan.md) nebo v [plánu App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> I když to není nutné, důrazně doporučujeme, abyste pro svou aplikaci nakonfigurovali Application Insights.

<a name="storage"></a>
### <a name="storage-account"></a>Účet úložiště

Pro aplikaci Function App je vyžadován účet služby Azure Storage. Potřebujete účet pro obecné účely, který podporuje objekty blob, tabulky, fronty a soubory. Další informace najdete v tématu [Azure Functions požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Kromě toho `AzureWebJobsStorage` musí být vlastnost zadána jako nastavení aplikace v konfiguraci lokality. Pokud aplikace Function App nepoužívá Application Insights ke sledování, měla by také určovat `AzureWebJobsDashboard` nastavení aplikace.

Modul runtime Azure Functions používá `AzureWebJobsStorage` připojovací řetězec k vytvoření interních front.  Když Application Insights není povolený, modul runtime použije `AzureWebJobsDashboard` připojovací řetězec k přihlášení do úložiště tabulek Azure a napájení karty **monitor** na portálu.

Tyto vlastnosti jsou uvedeny v `appSettings` kolekci v `siteConfig` objektu:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights se doporučuje pro monitorování aplikací Function App. Prostředek Application Insights je definován pomocí typu **Microsoft. Insights/Components** a druh **webu**:

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

Kromě toho je potřeba zadat klíč instrumentace aplikace Function App pomocí `APPINSIGHTS_INSTRUMENTATIONKEY` nastavení aplikace. Tato vlastnost je určena v `appSettings` kolekci v `siteConfig` objektu:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plán hostování

Definice plánu hostování se liší a může to být jedna z následujících:
* [Plán spotřeby](#consumption) (výchozí)
* [Plán Premium](#premium)
* [Plán App Service](#app-service-plan)

### <a name="function-app"></a>Aplikace funkcí

Prostředek Function App je definován pomocí prostředku typu **Microsoft. Web/Sites** a druhu **functionapp**:

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
> Pokud explicitně definujete plán hostování, bude v poli dependsOn potřeba další položka: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikace Function App musí zahrnovat tato nastavení aplikace:

| Název nastavení                 | Description                                                                               | Příklady hodnot                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Připojovací řetězec k účtu úložiště, který modul runtime služby Functions používá pro interní zařazení do fronty | Zobrazit [účet úložiště](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Verze modulu runtime Azure Functions                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Jazyková sada, která se má použít pro funkce v této aplikaci                                   | `dotnet`, `node` , `java` , `python` nebo `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Je potřeba jenom v případě `node` , že používáte sadu jazyků, určuje verzi, která se má použít.              | `10.14.1`                             |

Tyto vlastnosti jsou zadány v `appSettings` kolekci ve `siteConfig` vlastnosti:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Nasazení na plán spotřeby

Plán spotřeby automaticky přiděluje výpočetní výkon, když je váš kód spuštěný, škáluje se podle potřeby pro zpracování zátěže a pak se škáluje, když kód neběží. Nemusíte platit za nečinné virtuální počítače a nemusíte rezervovat kapacitu předem. Další informace najdete v tématu [škálování Azure functions a hostování](consumption-plan.md).

Ukázkové Azure Resource Managerovou šablonu najdete v tématu [aplikace Function App na základě plánu spotřeby].

### <a name="create-a-consumption-plan"></a>Vytvořit plán spotřeby

Plán spotřeby není nutné definovat. Při vytváření samotného prostředku aplikace Function App se jedna z jednotlivých oblastí automaticky vytvoří nebo vybere.

Plán spotřeby je speciální typ prostředku "serverová farma". V případě systému Windows jej můžete zadat pomocí `Dynamic` hodnoty `computeMode` `sku` vlastností a:

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
> Plán spotřeby nelze explicitně definovat pro Linux. Vytvoří se automaticky.

Pokud budete plán spotřeby explicitně definovat, budete muset nastavit `serverFarmId` vlastnost v aplikaci tak, aby odkazovala na ID prostředku plánu. Měli byste se ujistit, že aplikace Function App má `dependsOn` také nastavení pro plán.

### <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

Nastavení vyžadované aplikací Function App běžícími v plánu spotřeby se odloží mezi systémy Windows a Linux. 

#### <a name="windows"></a>Windows

V systému Windows plán spotřeby vyžaduje další nastavení v konfiguraci lokality: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Tato vlastnost konfiguruje účet úložiště, ve kterém se ukládají kódy a konfigurace funkcí aplikace Function App.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Nenastavte [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) nastavení tak, jak je vygenerováno při prvním vytvoření webu.  

#### <a name="linux"></a>Linux

V systému Linux musí mít aplikace funkcí `kind` nastavenou hodnotu `functionapp,linux` a musí mít `reserved` nastavenou vlastnost na hodnotu `true` . 

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)Nastavení a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) nejsou podporovaná v systému Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Nasazení na plán Premium

Plán Premium nabízí stejné škálování jako plán spotřeby, ale zahrnuje vyhrazené prostředky a další funkce. Další informace najdete v tématu [plán Azure Functions Premium](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Vytvořit plán Premium

Plán Premium je zvláštní typ prostředku "serverová farma". Můžete ji zadat buď pomocí `EP1` , `EP2` nebo `EP3` pro `Name` hodnotu vlastnosti v `sku` [objektu Description](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

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

### <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

Aplikace funkcí v plánu Premium musí mít `serverFarmId` vlastnost nastavenou na ID prostředku plánu, který jste vytvořili dříve. Plán Premium navíc vyžaduje další nastavení v konfiguraci lokality: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Tato vlastnost konfiguruje účet úložiště, ve kterém se ukládají kódy a konfigurace funkcí aplikace Function App.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Nenastavte [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) nastavení tak, jak je vygenerováno při prvním vytvoření webu.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Nasazení v plánu App Service

V plánu App Service aplikace Function App běží na vyhrazených virtuálních počítačích na jednotkách Basic, Standard a Premium, podobně jako Web Apps. Podrobnosti o tom, jak plán App Service funguje, najdete v podrobném [přehledu Azure App Service plány](../app-service/overview-hosting-plans.md).

Ukázkovou Azure Resource Managerovou šablonu najdete v tématu [aplikace Function App v plánu Azure App Service].

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

Plán App Service je definovaný prostředkem "serverová farma".

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

Chcete-li spustit aplikaci v systému Linux, je nutné také nastavit na `kind` `Linux` :

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

### <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

Aplikace funkcí v plánu App Service musí mít `serverFarmId` vlastnost nastavenou na ID prostředku plánu, který jste vytvořili dříve.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

Aplikace pro Linux by měly také obsahovat `linuxFxVersion` vlastnost `siteConfig` . Pokud právě nasazujete kód, hodnota pro tuto hodnotu je určena požadovaným zásobníkem modulu runtime ve formátu ```runtime|runtimeVersion``` :

| Zásobník            | Příklad hodnoty                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Pokud [nasazujete vlastní image kontejneru](./functions-create-function-linux-custom-image.md), musíte ji zadat s `linuxFxVersion` a zahrnout konfiguraci, která umožňuje, aby se vaše image obnovila, jako v [Web App for Containers](../app-service/index.yml). Nastavte také `WEBSITES_ENABLE_APP_SERVICE_STORAGE` na `false` , protože obsah vaší aplikace je k dispozici v kontejneru samotném:

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
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

Aplikace Function App má mnoho podřízených prostředků, které můžete použít ve svém nasazení, včetně nastavení aplikací a možností správy zdrojového kódu. Také se můžete rozhodnout pro odebrání podřízeného prostředku **sourcecontrols** a místo toho použít jinou [možnost nasazení](functions-continuous-deployment.md) .

> [!IMPORTANT]
> K úspěšnému nasazení aplikace pomocí Azure Resource Manager je důležité pochopit, jak se prostředky nasazují v Azure. V následujícím příkladu jsou konfigurace nejvyšší úrovně aplikovány pomocí **siteConfig**. Je důležité nastavit tyto konfigurace na nejvyšší úrovni, protože přenáší informace do modulu runtime funkcí a modulu nasazení. Před použitím podřízeného **sourcecontrols nebo webového** prostředku se vyžadují informace nejvyšší úrovně. I když je možné nakonfigurovat tato nastavení v prostředku **config/appSettings** na úrovni podřízeného objektu, musí být v některých případech vaše aplikace Function App nasazena *před* použitím **souboru config/appSettings** . Například pokud používáte funkce s [Logic Apps](../logic-apps/index.yml), jsou vaše funkce závislé na jiném prostředku.

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
                "value": "~3"
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
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
> Tato šablona používá hodnotu nastavení aplikace [projektu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) , která nastavuje základní adresář, ve kterém modul pro nasazení funkcí (Kudu) hledá nasaditelné kódy. V našem úložišti jsou naše funkce v podsložce složky **Src** . Takže v předchozím příkladu nastavíme hodnotu nastavení aplikace na `src` . Pokud jsou vaše funkce v kořenovém adresáři vašeho úložiště nebo pokud neprovádíte nasazení ze správy zdrojového kódu, můžete tuto hodnotu nastavení aplikace odebrat.

## <a name="deploy-your-template"></a>Nasazení šablony

K nasazení šablony můžete použít kterýkoli z následujících způsobů:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Tlačítko pro nasazení do Azure

Nahraďte ```<url-encoded-path-to-azuredeploy-json>``` verzí inpracovaná cesta ve formátu [adresy URL](https://www.bing.com/search?q=url+encode) `azuredeploy.json` souboru na GitHubu.

Tady je příklad, který používá Markdownu:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Tady je příklad, který používá HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Nasazení pomocí PowerShellu

Následující příkazy PowerShellu vytvoří skupinu prostředků a nasadí šablonu, která vytvoří aplikaci funkcí s požadovanými prostředky. Pokud chcete spustit místně, musíte mít nainstalovanou [Azure PowerShell](/powershell/azure/install-az-ps) . Spusťte [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) , abyste se přihlásili.

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

K otestování tohoto nasazení můžete použít [šablonu, jako je tato](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) , která vytvoří aplikaci funkcí ve Windows v plánu spotřeby. Nahraďte `<function-app-name>` jedinečným názvem vaší aplikace Function App.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o vývoji a konfiguraci Azure Functions.

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Jak nakonfigurovat nastavení Azure Function App](functions-how-to-use-azure-function-app-settings.md)
* [Vytvoření první funkce Azure Functions](./functions-get-started.md)

<!-- LINKS -->

[Aplikace Function App v plánu spotřeby]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikace Function App v plánu Azure App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
