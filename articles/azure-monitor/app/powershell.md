---
title: Automatizace Azure Application Insights s využitím PowerShellu | Microsoft Docs
description: Automatizujte vytváření testů prostředků, výstrah a dostupnosti v PowerShellu pomocí šablony Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mbullwin
ms.openlocfilehash: b4f3d2eba70be39b23e86ebde3c71dfc7c19a374
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936708"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Vytvoření prostředků Application Insights pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto článku se dozvíte, jak automaticky automatizovat vytváření a aktualizaci [Application Insightsch](../../azure-monitor/app/app-insights-overview.md) prostředků pomocí správy prostředků Azure. Můžete to například udělat jako součást procesu sestavení. Spolu se základním prostředkem Application Insights můžete vytvářet [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md), nastavit [výstrahy](../../azure-monitor/app/alerts.md), nastavit [cenové schéma](pricing.md)a vytvářet další prostředky Azure.

Klíčem k vytváření těchto prostředků jsou šablony JSON pro [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). V kostce je postup: Stáhněte si definice JSON existujících prostředků; parametrizovat určité hodnoty, jako jsou názvy; a pak šablonu spusťte vždy, když chcete vytvořit nový prostředek. Můžete zabalit několik prostředků dohromady a vytvořit je vše v jednom z nich – například monitorování aplikací s testy dostupnosti, výstrahy a úložiště pro průběžný export. Existují některé odlišností k některým z parameterizations, které tady vysvětlíme.

## <a name="one-time-setup"></a>Nastavení jednorázového času
Pokud jste ještě nepoužili prostředí PowerShell s předplatným Azure, postupujte takto:

Na počítač, na který chcete spouštět skripty, nainstalujte modul Azure PowerShell:

1. Nainstalujte [Instalace webové platformy Microsoft (verze 5 nebo novější)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Použijte ji k instalaci Microsoft Azure PowerShellu.

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager
Vytvoření nového souboru. JSON – Pojďme ho volat `template1.json` v tomto příkladu. Kopírovat tento obsah do tohoto obsahu:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
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
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
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
                    "retentionInDays": "[variables('retentionInDays')]",
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
1. V PowerShellu se přihlaste k Azure:
   
    `Connect-AzAccount`
2. Spusťte příkaz podobný tomuto:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` je skupina, ve které chcete vytvořit nové prostředky.
   * `-TemplateFile` se musí vyskytovat před vlastními parametry.
   * `-appName` název prostředku, který chcete vytvořit.

Můžete přidat další parametry – jejich popisy najdete v části Parameters (parametry) v šabloně.

## <a name="to-get-the-instrumentation-key"></a>Získání klíče instrumentace
Po vytvoření prostředku aplikace budete chtít klíč instrumentace: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Nastavení cenového plánu

Můžete nastavit [cenový plán](pricing.md).

Pokud chcete vytvořit prostředek aplikace s plánem ceny Enterprise, použijte šablonu výše:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|Rozhraní|
|---|---|
|první|Základní|
|odst|Enterprise|

* Pokud chcete použít výchozí cenový plán Basic, můžete prostředek CurrentBillingFeatures vynechat ze šablony.
* Pokud chcete změnit plán cen po vytvoření prostředku komponenty, můžete použít šablonu, která vynechá prostředek "Microsoft. Insights/Components". Vynechejte také uzel `dependsOn` z fakturačního prostředku. 

Aktualizovaný cenový plán ověříte tak, že se v prohlížeči zobrazí okno **Stránka využití a odhadované náklady** . **Aktualizujte zobrazení prohlížeče** a ujistěte se, že vidíte nejnovější stav.



## <a name="add-a-metric-alert"></a>Přidat upozornění metriky

Chcete-li nastavit výstrahu metriky ve stejnou dobu jako prostředek aplikace, slučte kód podobný tomuto souboru šablony:

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

Pokud vyvoláte šablonu, můžete volitelně přidat tento parametr:

    `-responseTime 2`

Můžete samozřejmě použít i další pole. 

Chcete-li zjistit názvy typů a podrobnosti o konfiguraci jiných pravidel upozornění, vytvořte pravidlo ručně a pak ho zkontrolujte v [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Přidat test dostupnosti

Tento příklad slouží pro test testu a testování jedné stránky.  

V testu dostupnosti **jsou dvě části** : samotný test a výstraha upozorňující na selhání.

Sloučí následující kód do souboru šablony, který vytváří aplikaci.

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
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
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

Chcete-li zjistit kódy pro jiná testovací umístění nebo automatizovat vytváření složitějších webových testů, vytvořte příklad ručně a potom tento kód z [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Přidat další zdroje

Chcete-li automatizovat vytváření jakýchkoli dalších prostředků jakéhokoliv druhu, vytvořte příklad ručně a potom zkopírujte a parametrizovat svůj kód z [Azure Resource Manager](https://resources.azure.com/). 

1. Otevřete [Azure Resource Manager](https://resources.azure.com/). Přejděte dolů do prostředku aplikace pomocí `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`. 
   
    ![Navigace v Azure Resource Explorer](./media/powershell/01.png)
   
    *Komponenty* jsou základní Application Insights prostředky pro zobrazování aplikací. Pro přidružená pravidla výstrah a webové testy dostupnosti jsou k dispozici samostatné prostředky.
2. Zkopírujte kód JSON součásti na příslušné místo v `template1.json`.
3. Odstranit tyto vlastnosti:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otevřete oddíly webtests a alertrules a zkopírujte kód JSON pro jednotlivé položky do šablony. (Nekopírovat z webtests nebo uzlů alertrules: přejít do položek pod nimi.)
   
    Každý webový test má přidružené pravidlo výstrahy, takže je nutné oba zkopírovat.
   
    Můžete také zahrnout výstrahy na metriky. [Názvy metrik](powershell-alerts.md#metric-names).
5. Vložit tento řádek do každého prostředku:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizovat šablonu
Nyní musíte nahradit konkrétní názvy parametry. Chcete-li [parametrizovat šablonu](../../azure-resource-manager/resource-group-authoring-templates.md), zapište výrazy pomocí [sady pomocných funkcí](../../azure-resource-manager/resource-group-template-functions.md). 

Nemůžete parametrizovat pouze část řetězce, takže použijte `concat()` k sestavení řetězců.

Tady jsou příklady náhrad, které chcete udělat. Existuje několik výskytů každé náhrady. V šabloně budete možná potřebovat jiné. V těchto příkladech se používají parametry a proměnné, které jsme definovali v horní části šablony.

| find | nahradit |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (malý případ) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Odstraňte identifikátor GUID a ID. |

### <a name="set-dependencies-between-the-resources"></a>Nastavení závislostí mezi prostředky
Azure by měl nastavit prostředky v přísném pořadí. Chcete-li zajistit, aby bylo jedno nastavení dokončeno před dalším začátkem, přidejte řádky závislosti:

* V prostředku testu dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* V prostředku výstrahy pro test dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Další kroky
Další články o automatizaci:

* [Vytvořte rychlou metodu Application Insights prostředků](powershell-script-create-resource.md) bez použití šablony.
* [Nastavení výstrah](powershell-alerts.md)
* [Vytváření webových testů](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Odeslat Azure Diagnostics do Application Insights](powershell-azure-diagnostics.md)
* [Nasazení do Azure z GitHubu](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Vytvořit poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)