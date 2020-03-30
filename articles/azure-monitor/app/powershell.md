---
title: Automatizace přehledů aplikací Azure pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Automatizujte vytváření a správu testů prostředků, výstrah a dostupnosti v Prostředí PowerShell pomocí šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275877"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Správa prostředků Application Insights pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek ukazuje, jak automatizovat vytváření a aktualizaci prostředků [Application Insights](../../azure-monitor/app/app-insights-overview.md) automaticky pomocí Azure Resource Management. Můžete tak například provést jako součást procesu sestavení. Spolu se základním prostředkem Application Insights můžete vytvářet [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md), nastavovat [výstrahy](../../azure-monitor/app/alerts.md), nastavit [cenové schéma](pricing.md)a vytvářet další prostředky Azure.

Klíčem k vytvoření těchto prostředků jsou šablony JSON pro [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). Základním postupem je: stáhnout json definice stávajících zdrojů; parametrizovat určité hodnoty, jako jsou názvy; a potom spusťte šablonu, kdykoli chcete vytvořit nový prostředek. Můžete zabalit několik prostředků dohromady a vytvořit je všechny najednou – například monitor aplikace s testy dostupnosti, výstrahy a úložiště pro nepřetržitý export. Některé parametrizace jsou některé jemnosti, které vysvětlíme zde.

## <a name="one-time-setup"></a>Jednorázové nastavení
Pokud jste powershell s předplatným Azure ještě nepoužívali:

Nainstalujte modul Azure Powershell do počítače, kde chcete spouštět skripty:

1. Nainstalujte [Instalační službu webové platformy Společnosti Microsoft (v5 nebo vyšší).](https://www.microsoft.com/web/downloads/platform.aspx)
2. Použijte ho k instalaci Microsoft Azure Powershellu.

Kromě použití šablon Správce prostředků existuje bohatá sada [rutin Application Insights PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), které usnadňují programovou konfiguraci prostředků Application Insights. Mezi možnosti povolené rutinami patří:

* Vytvoření a odstranění prostředků Application Insights
* Získání seznamů prostředků Application Insights a jejich vlastností
* Vytvoření a správa průběžného exportu
* Vytvoření a správa aplikačních klíčů
* Nastavení denního limitu
* Nastavení cenového plánu

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Vytvoření prostředků Application Insights pomocí rutiny prostředí PowerShell

Tady je postup, jak vytvořit nový prostředek Application Insights v datovém centru Azure East US pomocí rutiny [New-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Vytvoření prostředků Application Insights pomocí šablony Správce prostředků

Tady je postup, jak vytvořit nový prostředek Application Insights pomocí šablony Správce prostředků.

### <a name="create-the-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Vytvořte nový soubor JSON - nazvěme jej `template1.json` v tomto příkladu. Zkopírujte do něj tento obsah:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Vytvoření nového prostředku Application Insights pomocí šablony Správce prostředků

1. V PowerShellu se přihlaste k Azure pomocí`$Connect-AzAccount`
2. Nastavení kontextu na předplatné pomocí`Set-AzContext "<subscription ID>"`
2. Spusťte nové nasazení a vytvořte nový prostředek Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`je skupina, ve které chcete vytvořit nové prostředky.
   * `-TemplateFile`musí dojít před vlastní parametry.
   * `-appName`Název prostředku, který má být vytvořit.

Můžete přidat další parametry - jejich popisy najdete v části parametry šablony.

## <a name="get-the-instrumentation-key"></a>Získejte klíč instrumentace

Po vytvoření prostředku aplikace budete chtít klíč instrumentace: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Chcete-li zobrazit seznam mnoha dalších vlastností prostředku Application Insights, použijte:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Další vlastnosti jsou k dispozici prostřednictvím rutin:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Podívejte se na [podrobnou dokumentaci](https://docs.microsoft.com/powershell/module/az.applicationinsights) pro parametry pro tyto rutiny.  

## <a name="set-the-data-retention"></a>Nastavení uchovávání dat 

Chcete-li získat aktuální uchovávání dat pro váš prostředek Application Insights, můžete použít nástroj OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Další informace o ARMClient z článků [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Zde je příklad `ARMClient`pomocí , chcete-li získat aktuální uchovávání informací:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Chcete-li nastavit uchovávání informací, příkaz je podobný PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Chcete-li nastavit uchovávání dat na 365 dní pomocí výše uvedené šablony, spusťte:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Následující skript lze také změnit uchovávání informací. Zkopírujte tento skript, který chcete uložit jako `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Tento skript pak lze použít jako:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Nastavení denního limitu

Chcete-li získat vlastnosti denního limitu, použijte rutinu [Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Chcete-li nastavit denní vlastnosti víčka, použijte stejnou rutinu. Chcete-li například nastavit limit na 300 GB/den,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Můžete také použít [ARMClient](https://github.com/projectkudu/ARMClient) získat a nastavit parametry denní hospodařící.  Chcete-li získat aktuální hodnoty, použijte:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Nastavení doby vynulování denního limitu

Chcete-li nastavit denní čas resetování limitu, můžete použít [ARMClient](https://github.com/projectkudu/ARMClient). Zde je příklad `ARMClient`pomocí , chcete-li nastavit čas obnovení na novou hodinu (v tomto příkladu 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Nastavení cenového plánu 

Chcete-li získat aktuální cenový plán, použijte rutinu [Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Chcete-li nastavit cenový plán, použijte `-PricingPlan` stejnou rutinu se zadanou:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Cenový plán můžete také nastavit u existujícího prostředku Application Insights pomocí výše uvedené šablony Správce prostředků a `dependsOn` vynechat prostředek Microsoft.Insights/components a uzel z fakturačního prostředku. Chcete-li například nastavit na plán Na GB (dříve nazývaný základní plán), spusťte:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

Je `priceCode` definována jako:

|priceCode|Plán|
|---|---|
|1|Za GB (dříve nazvaný Základní plán)|
|2|Na uzel (dříve název Enterprise plan)|

Nakonec můžete použít [ARMClient](https://github.com/projectkudu/ARMClient) získat a nastavit cenové plány a parametry denního limitu.  Chcete-li získat aktuální hodnoty, použijte:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

A všechny tyto parametry můžete nastavit pomocí:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Tím se nastaví denní limit na 200 GB / den, nakonfiguruje denní dobu resetování limitu na 12: 00 UTC, odesílá e-maily jak při dosažení limitu, tak na úroveň varování a nastaví výstražnou prahovou hodnotu na 90% limitu.  

## <a name="add-a-metric-alert"></a>Přidání upozornění na metriku

Chcete-li automatizovat vytváření upozornění na metriky, podívejte se do [článku šablony upozornění na metriky.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Přidání testu dostupnosti

Chcete-li automatizovat testy dostupnosti, podívejte se do [článku šablony upozornění na metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Přidání dalších zdrojů

Chcete-li automatizovat vytváření jakéhokoli jiného prostředku jakéhokoli druhu, vytvořte příklad ručně a potom zkopírujte a parametrizujte jeho kód ze [Správce prostředků Azure](https://resources.azure.com/). 

1. Otevřete [Správce prostředků Azure](https://resources.azure.com/). Přejděte `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`dolů k prostředku aplikace. 
   
    ![Navigace v Průzkumníku prostředků Azure](./media/powershell/01.png)
   
    *Součásti* jsou základní prostředky Application Insights pro zobrazení aplikací. Pro přidružená pravidla výstrah a webové testy dostupnosti existují samostatné prostředky.
2. Zkopírujte JSON součásti na příslušné `template1.json`místo v .
3. Odstraňte tyto vlastnosti:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otevřete `webtests` `alertrules` oddíly a zkopírujte json pro jednotlivé položky do šablony. (Nekopírujte z `webtests` `alertrules` uzlů nebo: přejděte do položek pod nimi.)
   
    Každý webový test má přidružené pravidlo výstrahy, takže je třeba zkopírovat oba.
   
    Můžete také zahrnout upozornění na metriky. [Metrické názvy](powershell-alerts.md#metric-names).
5. Vložte tento řádek do každého zdroje:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizovat šablonu
Nyní musíte nahradit konkrétní názvy parametry. Chcete-li [parametrizovat šablonu](../../azure-resource-manager/templates/template-syntax.md), zapisujte výrazy pomocí [sady pomocných funkcí](../../azure-resource-manager/templates/template-functions.md). 

Nelze parametrizovat pouze část řetězce, takže slouží `concat()` k sestavení řetězce.

Zde jsou příklady substitucí, které budete chtít provést. Existuje několik výskytů každé substituce. Možná budete potřebovat další v šabloně. Tyto příklady používají parametry a proměnné, které jsme definovali v horní části šablony.

| find | nahradit |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(malá písmena) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Nastavení závislostí mezi prostředky
Azure by měl nastavit prostředky v přísném pořadí. Chcete-li se ujistit, že jedno nastavení bude dokončeno před dalším zahájením, přidejte řádky závislostí:

* V testovacím prostředku dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* V prostředku výstrahy pro test dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Další kroky
Další články o automatizaci:

* [Vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) – rychlá metoda bez použití šablony.
* [Nastavení výstrah](powershell-alerts.md)
* [Vytvářejte webové testy](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Odesílání Azure Diagnostics do Application Insights](powershell-azure-diagnostics.md)
* [Nasazení do Azure z GitHubu](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Vytvoření poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)