---
title: Automatizace Azure Application Insights v prostředí PowerShell | Microsoft Docs
description: Automatizovat vytváření prostředků, výstrahy a dostupnost testů v prostředí PowerShell pomocí šablony Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 46ba4ce992640e8a6d171ab839dd7cdb24e0b404
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
#  <a name="create-application-insights-resources-using-powershell"></a>Vytváření prostředků Application Insights v prostředí PowerShell
Tento článek ukazuje, jak automatizovat vytváření a aktualizace [Application Insights](app-insights-overview.md) prostředky automaticky pomocí nástroje Správa prostředků Azure. Může například uděláte jako součást procesu sestavení. Společně s základní prostředku Application Insights, můžete vytvořit [testy dostupnosti webu](app-insights-monitor-web-app-availability.md), nastavte [výstrahy](app-insights-alerts.md), nastavte [ceny schéma](app-insights-pricing.md)a vytvořte další prostředky Azure .

Klíčem k vytváření těchto prostředků je šablony JSON pro [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md). Stručně řečeno, je postup: stažení JSON definice prostředků existující; Parametrizace určité hodnoty, například názvy; a spusťte šablonu vždy, když chcete vytvořit nový prostředek. Několik prostředků můžete balíček dohromady, k jejich vytvoření vše v jednom přejděte – například monitorování aplikace s testy dostupnosti, výstrahy a úložiště pro nepřetržitou export. Existují některé odlišnosti k některým z parameterizations, které vám objasníme sem.

## <a name="one-time-setup"></a>Jednorázové instalace
Pokud jste nepoužili prostředí PowerShell s předplatným Azure před:

Instalace modulu Azure Powershell na počítači, kde chcete spustit skripty:

1. Nainstalujte [instalačního programu webové platformy (verze 5 nebo novější)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Jeho použití k instalaci aplikace Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Vytvořit šablonu Azure Resource Manager
Vytvořte nový soubor .json – umožňuje volání `template1.json` v tomto příkladu. Zkopírujte do ní tento obsah:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Vytvořit prostředky Application Insights
1. V prostředí PowerShell Přihlaste se k Azure:
   
    `Login-AzureRmAccount`
2. Spusťte příkaz takto:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` je skupina, kde chcete vytvořit nové prostředky.
   * `-TemplateFile` musí nastat před vlastní parametry.
   * `-appName` Název prostředek pro vytvoření.

Můžete přidat další parametry – jejich popisy najdete v sekci parametrů šablony.

## <a name="to-get-the-instrumentation-key"></a>Získat klíč instrumentace
Po vytvoření prostředek aplikace, budete muset klíč instrumentace: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Nastavte plán cena

Můžete nastavit [cena plán](app-insights-pricing.md).

Chcete-li vytvořit prostředek aplikace s plánem cena Enterprise, pomocí výše uvedené šablony:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plán|
|---|---|
|1|Basic|
|2|Enterprise|

* Pokud chcete použít výchozí plán základní ceny, můžete vynechat CurrentBillingFeatures prostředků ze šablony.
* Pokud chcete změnit plán cena po vytvoření součásti prostředků, můžete šablonu, která vynechá prostředků "microsoft.insights/components". Navíc vynechejte `dependsOn` uzel z fakturace prostředku. 

Chcete-li ověřit, aktualizovaná cena plánu, podívejte se **využití a odhadované náklady na stránce** okna v prohlížeči. **Aktualizujte zobrazení prohlížeče** a ujistěte se, vidíte nejnovější stav.



## <a name="add-a-metric-alert"></a>Přidání metriky oznámení

Pokud chcete nastavit upozornění na metriky ve stejnou dobu jako prostředek vaší aplikace, sloučení do souboru šablony kódu takto:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Při vyvolání šablony, můžete přidat tento parametr:

    `-responseTime 2`

Samozřejmě můžete parametrizovat další pole. 

Pokud chcete zjistit názvy typů a podrobnosti o konfiguraci pravidel dalších výstrah, ručně vytvořit pravidlo a poté zkontrolovat v [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Přidat test dostupnosti

V tomto příkladu je pro test příkazem ping (k testování jediné stránce).  

**Existují dvě části** v testu dostupnosti: sama a příslušnou výstrahu, která vás informuje o selhání.

Slučte následující kód do souboru šablony, který vytvoří aplikaci.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Ke zjištění kódy pro jiné umístění testu, nebo k automatizaci vytváření složitějších webové testy, vytvořte příklad ručně a pak Parametrizace kód z [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Přidat další prostředky

K automatizaci vytváření jiný prostředek libovolného typu, například vytvořit ručně a pak zkopírujte a Parametrizace jeho kód z [Azure Resource Manager](https://resources.azure.com/). 

1. Otevřete [Azure Resource Manager](https://resources.azure.com/). Přejděte dolů prostřednictvím `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, na prostředek vaší aplikace. 
   
    ![Navigace v Průzkumníku prostředků Azure.](./media/app-insights-powershell/01.png)
   
    *Součásti* jsou základní prostředky Application Insights pro zobrazení aplikace. Existují samostatné prostředky pro přidružená pravidla výstrah a testy dostupnosti webu.
2. Zkopírujte JSON komponenty do odpovídajícího místa v `template1.json`.
3. Odstraňte tyto vlastnosti:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otevřete webtests a alertrules části a zkopírujte JSON pro jednotlivé položky do šablony. (Není zkopírovat z uzlů webtests nebo alertrules: přejděte do položky pod nimi.)
   
    Každý test webu má přidružené pravidlo výstrahy, takže musíte zkopírovat oba dva.
   
    Můžete použít také výstrahy o metrikách. [Metriky názvy](app-insights-powershell-alerts.md#metric-names).
5. Vložte tento řádek v každého prostředku:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizace šablony
Nyní máte nahraďte konkrétní názvy s parametry. K [Parametrizace šablonu](../azure-resource-manager/resource-group-authoring-templates.md), můžete psát pomocí výrazy [sadu pomocných funkcí](../azure-resource-manager/resource-group-template-functions.md). 

Nelze Parametrizace jenom část řetězce, takže použijte `concat()` k sestavení řetězce.

Zde jsou příklady nahrazení, který budete chtít provést. Existuje několik výskyty každé nahrazování. Ostatní bude pravděpodobně nutné ve vaší šabloně. Tyto příklady použít parametry a proměnné, které jsme definovali v horní části šablony.

| Najít | Nahraďte |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (malá písmena) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Odstranit Guid a ID. |

### <a name="set-dependencies-between-the-resources"></a>Nastavte závislosti mezi prostředky
Azure měli nastavit prostředky v striktní pořadí. Pokud chcete mít jistotu, že jeden instalační program dokončí před zahájením dalších, přidejte závislosti řádky:

* V testu prostředku dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* U výstrah prostředku pro test dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Další postup
Další články automatizace:

* [Vytvořte prostředek Application Insights](app-insights-powershell-script-create-resource.md) -rychlý způsob bez použití šablony.
* [Nastavení výstrah](app-insights-powershell-alerts.md)
* [Tvorba webových testů](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Odesílání Diagnostiky Azure do Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Nasazení do Azure z Githubu](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Vytvořit poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

