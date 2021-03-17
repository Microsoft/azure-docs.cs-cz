---
title: Automatizace Azure Application Insights s využitím PowerShellu | Microsoft Docs
description: Automatizujte vytváření a správu prostředků, upozornění a testů dostupnosti v PowerShellu pomocí šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: c2e3d33be487b6a92cb7038d814e17fcd5a10064
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589810"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Správa prostředků Application Insights pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto článku se dozvíte, jak automaticky automatizovat vytváření a aktualizaci [Application Insightsch](./app-insights-overview.md) prostředků pomocí správy prostředků Azure. Můžete to například udělat jako součást procesu sestavení. Spolu se základním prostředkem Application Insights můžete vytvářet [webové testy dostupnosti](./monitor-web-app-availability.md), nastavit [výstrahy](../alerts/alerts-log.md), nastavit [cenové schéma](pricing.md)a vytvářet další prostředky Azure.

Klíčem k vytváření těchto prostředků jsou šablony JSON pro [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). Základní postup: Stáhněte si definice JSON existujících prostředků; parametrizovat určité hodnoty, jako jsou názvy; a pak šablonu spusťte vždy, když chcete vytvořit nový prostředek. Můžete zabalit několik prostředků dohromady a vytvořit je vše v jednom z nich – například monitorování aplikací s testy dostupnosti, výstrahy a úložiště pro průběžný export. Existují některé odlišností k některým z parameterizations, které tady vysvětlíme.

## <a name="one-time-setup"></a>Nastavení jednorázového času
Pokud jste ještě nepoužili prostředí PowerShell s předplatným Azure, postupujte takto:

Na počítač, na kterém chcete spouštět skripty, nainstalujte modul Azure PowerShell:

1. Nainstalujte [Instalace webové platformy Microsoft (verze 5 nebo novější)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Použijte ji k instalaci Microsoft Azure PowerShell.

Kromě používání šablon Správce prostředků existuje bohatá sada [Application Insights rutin PowerShellu](/powershell/module/az.applicationinsights), která usnadňuje konfiguraci Application Insights prostředků programově. Mezi možnosti povolené rutinami patří:

* Vytváření a odstraňování Application Insightsch prostředků
* Získá seznam Application Insightsch prostředků a jejich vlastností.
* Vytvoření a Správa průběžného exportu
* Vytváření a Správa klíčů aplikací
* Nastavení denního limitu
* Nastavení cenového plánu

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Vytvoření prostředků Application Insights pomocí rutiny prostředí PowerShell

Tady je postup vytvoření nového prostředku Application Insights v datovém centru Azure Východní USA pomocí rutiny [New-AzApplicationInsights](/powershell/module/az.applicationinsights/new-azapplicationinsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Vytvoření prostředků Application Insights pomocí šablony Správce prostředků

Tady je postup vytvoření nového prostředku Application Insights pomocí šablony Správce prostředků.

### <a name="create-the-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager

Vytvoření nového souboru. JSON – Pojďme ho zavolat `template1.json` v tomto příkladu. Kopírovat tento obsah do tohoto obsahu:

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

1. Přihlášení do Azure pomocí prostředí PowerShell `$Connect-AzAccount`
2. Nastavte svůj kontext na předplatné s `Set-AzContext "<subscription ID>"`
2. Pokud chcete vytvořit nový prostředek Application Insights, spusťte nové nasazení:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` je skupina, ve které chcete vytvořit nové prostředky.
   * `-TemplateFile` musí se nacházet před vlastními parametry.
   * `-appName` Název prostředku, který se má vytvořit

Můžete přidat další parametry – jejich popisy najdete v části Parameters (parametry) v šabloně.

## <a name="get-the-instrumentation-key"></a>Získat klíč instrumentace

Po vytvoření prostředku aplikace budete chtít klíč instrumentace: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Pokud chcete zobrazit seznam mnoha dalších vlastností prostředku Application Insights, použijte:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Další vlastnosti jsou k dispozici prostřednictvím rutin:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Informace o parametrech těchto rutin najdete v [podrobné dokumentaci](/powershell/module/az.applicationinsights) .  

## <a name="set-the-data-retention"></a>Nastavení uchovávání dat

Níže jsou tři metody pro programové nastavení uchovávání dat u Application Insightsho prostředku.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Nastavení uchovávání dat pomocí příkazů PowerShellu

Tady je jednoduchá sada příkazů PowerShellu pro nastavení uchovávání dat pro váš Application Insights prostředek:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Nastavení uchovávání dat pomocí REST

Pokud chcete získat aktuální uchovávání dat pro váš Application Insights prostředek, můžete použít nástroj OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Další informace o ARMClient od článků [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Tady je příklad použití `ARMClient` pro získání aktuálního uchování:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Chcete-li nastavit uchování, je příkaz podobným VLOŽENÍm:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Chcete-li nastavit uchovávání dat na 365 dní pomocí výše uvedené šablony, spusťte příkaz:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Nastavení uchovávání dat pomocí skriptu PowerShellu

Pro změnu uchovávání lze také použít následující skript. Zkopírujte tento skript a uložte ho jako `Set-ApplicationInsightsRetention.ps1` .

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

Tento skript se pak dá použít jako:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Nastavení denního limitu

Chcete-li získat vlastnosti denního limitu, použijte rutinu [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Chcete-li nastavit vlastnosti denního limitu, použijte stejnou rutinu. Chcete-li například nastavit limit na 300 GB za den,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

[ARMClient](https://github.com/projectkudu/ARMClient) můžete použít také k získání a nastavení parametrů denního Cap.  Chcete-li získat aktuální hodnoty, použijte:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Nastavení času pro obnovení denního limitu

Chcete-li nastavit denní čas obnovení, můžete použít [ARMClient](https://github.com/projectkudu/ARMClient). Tady je příklad použití `ARMClient` , chcete-li nastavit čas obnovení na novou hodinu (v tomto příkladu 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Nastavení cenového plánu 

K získání aktuálního cenového plánu použijte rutinu [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) :

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

K nastavení cenového plánu použijte stejnou rutinu se `-PricingPlan` zadaným:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Cenové tarify pro existující Application Insights prostředek můžete také nastavit pomocí Správce prostředků šablony výše a vynechání prostředku Microsoft. Insights/Components a `dependsOn` uzlu z fakturačního prostředku. Chcete-li například nastavit plán na GB (dříve označovaný jako základní plán), spusťte příkaz:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode`Je definován jako:

|priceCode|rozhraní|
|---|---|
|1|Za GB (dříve označované jako základní plán)|
|2|Za uzel (dříve název plánu v podniku)|

Nakonec můžete pomocí [ARMClient](https://github.com/projectkudu/ARMClient) získat a nastavit cenové plány a parametry denního Cap.  Chcete-li získat aktuální hodnoty, použijte:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

A všechny tyto parametry můžete nastavit pomocí:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Tím nastavíte denní limit na 200 GB za den, nakonfigurujete denní dobu resetování na 12:00 UTC, odešlete e-maily, když je dosaženo limitu a dojde k dosažení úrovně upozornění, a nastavíte prahovou hodnotu pro upozornění na 90% limitu.  

## <a name="add-a-metric-alert"></a>Přidat upozornění metriky

Pokud chcete automatizovat vytváření výstrah metrik, Projděte si [článek šablona výstrahy metriky](../alerts/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert) .


## <a name="add-an-availability-test"></a>Přidat test dostupnosti

Pokud chcete automatizovat testy dostupnosti, Projděte si [článek šablona výstrahy metrik](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Přidat další zdroje

Chcete-li automatizovat vytváření jakýchkoli dalších prostředků jakéhokoliv druhu, vytvořte příklad ručně a potom zkopírujte a parametrizovat svůj kód z [Azure Resource Manager](https://resources.azure.com/). 

1. Otevřete [Azure Resource Manager](https://resources.azure.com/). Přejděte `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` k prostředku aplikace směrem dolů. 
   
    ![Navigace v Azure Resource Explorer](./media/powershell/01.png)
   
    *Komponenty* jsou základní Application Insights prostředky pro zobrazování aplikací. Pro přidružená pravidla výstrah a webové testy dostupnosti jsou k dispozici samostatné prostředky.
2. Zkopírujte kód JSON součásti na příslušné místo v `template1.json` .
3. Odstranit tyto vlastnosti:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otevřete `webtests` oddíly a `alertrules` a zkopírujte kód JSON pro jednotlivé položky do šablony. (Nekopírovat z `webtests` uzlů nebo `alertrules` : přejít do položek pod nimi.)
   
    Každý webový test má přidružené pravidlo výstrahy, takže je nutné oba zkopírovat.
   
5. Vložit tento řádek do každého prostředku:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizovat šablonu
Nyní musíte nahradit konkrétní názvy parametry. Chcete-li [parametrizovat šablonu](../../azure-resource-manager/templates/template-syntax.md), zapište výrazy pomocí [sady pomocných funkcí](../../azure-resource-manager/templates/template-functions.md). 

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
| `"myappname"` (malá písmena) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Nastavení závislostí mezi prostředky
Azure by měl nastavit prostředky v přísném pořadí. Chcete-li zajistit, aby bylo jedno nastavení dokončeno před dalším začátkem, přidejte řádky závislosti:

* V prostředku testu dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* V prostředku výstrahy pro test dostupnosti:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Další kroky
Další články o automatizaci:

* [Vytvořte rychlou metodu Application Insights prostředků](./create-new-resource.md#creating-a-resource-automatically) bez použití šablony.
* [Vytvářejte webové testy](../alerts/resource-manager-alerts-metric.md#availability-test-with-metric-alert)
* [Odesílání Azure Diagnostics do Application Insights](powershell-azure-diagnostics.md)
* [Vytvořit poznámky k verzi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)