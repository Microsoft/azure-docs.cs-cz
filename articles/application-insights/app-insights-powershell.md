---
title: Automatizace Azure Application Insights pomocí Powershellu | Dokumentace Microsoftu
description: Automatizace vytváření prostředků, upozornění a dostupnost testů v prostředí PowerShell pomocí šablony Azure Resource Manageru.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 262d2511879047d1723d3b15c4c107b80ba1c297
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250335"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Vytváření prostředků Application Insights v prostředí PowerShell
Tento článek ukazuje, jak automatizovat vytváření a aktualizaci [Application Insights](app-insights-overview.md) prostředky automaticky pomocí Azure Resource Manageru. Například provádět jako součást procesu sestavení. Spolu s základní prostředek Application Insights, můžete vytvořit [testy dostupnosti webu](app-insights-monitor-web-app-availability.md), nastavit [výstrahy](app-insights-alerts.md), nastavte [cenové schéma](app-insights-pricing.md)a vytvořit další prostředky Azure .

Klíčem k vytvoření těchto prostředků je šablony JSON pro [Azure Resource Manageru](../azure-resource-manager/powershell-azure-resource-manager.md). Řečeno v kostce, postup je: stáhnout definice JSON z existujících prostředků; parametrizovat určité hodnoty jako jsou názvy; a spusťte šablonu pokaždé, když chcete vytvořit nový prostředek. Několik prostředků můžete zabalit dohromady, k jejich vytvoření všechno v jednom přejděte – například monitorování aplikace s testy dostupnosti, upozornění a úložiště pro průběžný export. Existují některé odlišnosti k některým parameterizations, které vám objasníme tady.

## <a name="one-time-setup"></a>Jednorázová nastavení
Pokud jste ještě nepoužívali prostředí PowerShell ve vašem předplatném Azure před:

Instalace modulu Azure Powershellu na počítači, ve kterém chcete spustit skripty:

1. Nainstalujte [instalačního programu webové platformy (verze 5 nebo vyšší)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Jeho použití k instalaci prostředí Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru
Vytvořte nový soubor .json – ho budeme nazývat `template1.json` v tomto příkladu. Zkopírujte do něj tento obsah:

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



## <a name="create-application-insights-resources"></a>Vytvoření prostředků Application Insights
1. V prostředí PowerShell Přihlaste se k Azure:
   
    `Connect-AzureRmAccount`
2. Spusťte příkaz takto:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` je skupina, ve které chcete vytvářet nové prostředky.
   * `-TemplateFile` musí vyskytovat před vlastní parametry.
   * `-appName` Název prostředku, který chcete vytvořit.

Můžete přidat další parametry - jejich popis najdete v sekci parametrů šablony.

## <a name="to-get-the-instrumentation-key"></a>Chcete-li získat klíč instrumentace
Po vytvoření prostředek aplikace, je vhodné Instrumentační klíč: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Nastavit cenový plán

Můžete nastavit [cenový plán](app-insights-pricing.md).

Vytvořte prostředek aplikace s cenový plán Enterprise, pomocí výše uvedené šablony:

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

* Pokud chcete použít výchozí základní cenový plán, můžete vynechat CurrentBillingFeatures prostředků ze šablony.
* Pokud chcete změnit cenový plán po vytvoření prostředku komponenty, můžete použít šablonu, která vynechává prostředku "microsoft.insights/components". Navíc vynechat, nechte `dependsOn` uzlu z účtování prostředku. 

Pokud chcete ověřit aktualizovanou cenový plán, podívejte se na **využití a odhadované náklady na stránce** okna v prohlížeči. **Aktualizujte zobrazení prohlížeče** k Ujistěte se, že vidíte nejnovější stav.



## <a name="add-a-metric-alert"></a>Přidat upozornění metriky

Nastavení upozornění na metriku ve stejnou dobu jako prostředek vaší aplikace, sloučit kód do souboru šablony:

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

Při vyvolání šablony, můžete volitelně přidat tento parametr:

    `-responseTime 2`

Samozřejmě můžete parametrizovat ostatní pole. 

Můžete zjistit názvy typů a podrobnosti o konfiguraci dalších pravidel upozornění, ručně vytvořit pravidlo a poté zkontrolujte v [Azure Resource Manageru](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Přidat test dostupnosti

V tomto příkladu je otestovat příkazem ping (Chcete-li otestovat jediné stránce).  

**Existují dvě části** v testu dostupnosti: samotný test a příslušnou výstrahu, která upozorní na selhání.

Slučte následující kód do souboru šablony, který vytváří aplikaci.

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

Ke zjištění kódy pro jiné umístění testu, nebo k automatizaci vytváření složitějších webové testy, vytvořte příklad ručně a pak parametrizovat kód z [Azure Resource Manageru](https://resources.azure.com/).

## <a name="add-more-resources"></a>Přidat další prostředky

Automatizace vytváření jiný prostředek jakéhokoli druhu, vytvořit příklad ručně a pak zkopírujte a parametrizovat jeho kód z [Azure Resource Manageru](https://resources.azure.com/). 

1. Otevřít [Azure Resource Manageru](https://resources.azure.com/). Procházejte dolů `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, na prostředek vaší aplikace. 
   
    ![Navigace v Průzkumníku prostředků Azure](./media/app-insights-powershell/01.png)
   
    *Součásti* jsou základní prostředky Application Insights pro zobrazení aplikace. Existují samostatné prostředky pro přidružená pravidla upozornění a webové testy dostupnosti.
2. Zkopírujte do příslušného místa v JSON komponenty `template1.json`.
3. Odstraňte tyto vlastnosti:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otevřete webové testy a alertrules části a zkopírujte JSON pro jednotlivé položky do šablony. (Nekopírujte z uzlů webové testy nebo alertrules: přejděte na položky pod nimi.)
   
    Každý webový test má přidružené pravidlo upozornění, proto musíte zkopírovat obou z nich.
   
    Můžete použít také výstrahy týkající se metrik. [Názvy metrik](app-insights-powershell-alerts.md#metric-names).
5. Vložte tento řádek do každého prostředku:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizaci šablony
Teď je potřeba nahradit konkrétní názvy parametrů. K [parametrizaci šablony](../azure-resource-manager/resource-group-authoring-templates.md), psaní výrazů pomocí [sadu pomocných funkcí](../azure-resource-manager/resource-group-template-functions.md). 

Nelze parametrizovat jenom část řetězce, proto použijte `concat()` k vytvoření řetězce.

Tady jsou příklady nahrazení, který budete chtít provést. Existuje několik výskytů každé nahrazení. Může být nutné ostatní ve vaší šabloně. Tyto příklady používají parametry a proměnné, které jsme definovali v horní části šablony.

| find | Nahraďte |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (malá písmena) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Odstraní identifikátor Guid a ID. |

### <a name="set-dependencies-between-the-resources"></a>Nastavte závislosti mezi prostředky
Azure by měl nastavit prostředky v přísného pořadí. Pokud chcete mít jistotu, že jeden instalační program dokončí před začátkem další, přidejte závislosti řádky:

* V prostředku testů dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* V prostředku upozornění pro test dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Další postup
Další články automation:

* [Vytvořte prostředek Application Insights](app-insights-powershell-script-create-resource.md) – rychlé metody bez použití šablony.
* [Nastavení výstrah](app-insights-powershell-alerts.md)
* [Vytvářejte webové testy](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Odesílání Diagnostiky Azure do Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Nasazení do Azure z Githubu](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Vytvoření poznámek](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

