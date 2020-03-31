---
title: Správa prostředí pomocí šablon Azure Resource Manager – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat prostředí Azure Time Series Insights programově pomocí Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1caa80469504d52d3103fb2776fb3e7210971690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024394"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Vytváření prostředků Time Series Insights pomocí šablon Azure Resource Manager

Tento článek popisuje, jak vytvořit a nasadit prostředky Time Series Insights pomocí [šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), PowerShellu a poskytovatele prostředků Time Series Insights.

Time Series Insights podporuje následující zdroje:

   | Prostředek | Popis |
   | --- | --- |
   | Prostředí | Prostředí Time Series Insights je logické seskupení událostí, které jsou čteny z zprostředkovatelů událostí, uloženy a zpřístupněny pro dotaz. Další informace najdete v [článek Plánování prostředí Azure Time Series Insights.](time-series-insights-environment-planning.md) |
   | Zdroj události | Zdroj událostí je připojení k zprostředkovateli událostí, ze kterého Time Series Insights čte a ingestuje události do prostředí. Aktuálně podporované zdroje událostí jsou IoT Hub a Event Hub. |
   | Sada referenčních dat | Sady referenčních dat poskytují metadata o událostech v prostředí. Metadata v referenčních datových sadách budou spojena s událostmi během příchozího přenosu dat. Sady referenčních dat jsou definovány jako prostředky podle vlastností klíče události. Skutečná metadata, která tvoří sadu referenčních dat, se nahrají nebo upraví prostřednictvím rozhraní API roviny dat. |
   | Zásady přístupu | Zásady přístupu udělují oprávnění k vydávání datových dotazů, manipulaci s referenčními daty v prostředí a sdílení uložených dotazů a perspektiv přidružených k prostředí. Další informace najdete v [článek Udělení přístupu k datům do prostředí Time Series Insights pomocí portálu Azure Portal.](time-series-insights-data-access.md) |

Šablona Správce prostředků je soubor JSON, který definuje infrastrukturu a konfiguraci prostředků ve skupině prostředků. Následující dokumenty popisují soubory šablon podrobněji:

- [Nasazení šablony Azure Resource Manageru](../azure-resource-manager/templates/overview.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
- [Typy prostředků Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Šablona rychlého startu [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) se publikuje na GitHubu. Tato šablona vytvoří prostředí Time Series Insights, podřízený zdroj událostí nakonfigurovaný tak, aby spotřebovával události z centra událostí, a zásady přístupu, které udělují přístup k datům prostředí. Pokud není zadáno existující centrum událostí, bude vytvořeno s nasazením.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Určení šablony nasazení a parametrů

Následující postup popisuje, jak pomocí PowerShellu nasadit šablonu Azure Resource Manageru, která vytvoří prostředí Time Series Insights, podřízený zdroj událostí nakonfigurovaný tak, aby využíval události z centra událostí, a zásady přístupu, které udělují přístup k údajů prostředí. Pokud není zadáno existující centrum událostí, bude vytvořeno s nasazením.

1. Nainstalujte Azure PowerShell podle pokynů v [začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Klonujte nebo zkopírujte šablonu [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) z GitHubu.

   * Vytvoření souboru parametrů

     Chcete-li vytvořit soubor parametrů, zkopírujte soubor [201-timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Povinné parametry

     | Parametr | Popis |
     | --- | --- |
     | eventHubNamespaceName | Obor názvů zdrojového centra událostí. |
     | eventHubName | Název centra zdrojových událostí. |
     | consumerGroupName | Název skupiny spotřebitelů, kterou služba Time Series Insights použije ke čtení dat z centra událostí. **POZNÁMKA:** Aby se zabránilo tvrzení o prostředku, tato skupina spotřebitelů musí být vyhrazena pro službu Time Series Insights a nesmí být sdílena s ostatními čtenáři. |
     | název prostředí | Název prostředí. Název nemůže `<`obsahovat: `%` `&`, `:` `>`, `?` `/`, , `\\`, , , a žádné řídicí znaky. Všechny ostatní znaky jsou povolené.|
     | eventSourceName | Název podřízeného prostředku zdroje události. Název nemůže `<`obsahovat: `%` `&`, `:` `>`, `?` `/`, , `\\`, , , a žádné řídicí znaky. Všechny ostatní znaky jsou povolené. |

    <div id="optional-parameters"></div>

   * Volitelné parametry

     | Parametr | Popis |
     | --- | --- |
     | existingEventHubResourceId | Volitelné ID prostředku existujícího centra událostí, které bude připojeno k prostředí Time Series Insights prostřednictvím zdroje událostí. **POZNÁMKA:** Uživatel, který šablonu nasazuje, musí mít oprávnění k provedení operace listkeys v centru událostí. Pokud není předána žádná hodnota, vytvoří šablona nové centrum událostí. |
     | environmentDisplayName | Volitelný popisný název, který se zobrazí v nástrojích nebo uživatelských rozhraních namísto názvu prostředí. |
     | environmentSkuName | Název sku. Další informace naleznete na [stránce Spotřeba přehledů času](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Jednotková kapacita sku. Další informace naleznete na [stránce Spotřeba přehledů času](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | prostředíDataRetentionTime | Minimální časový rozsah události prostředí budou k dispozici pro dotaz. Hodnota musí být zadána ve formátu ISO 8601, například `P30D` pro zásady uchovávání informací 30 dní. |
     | eventSourceDisplayName | Volitelný popisný název, který se zobrazí v nástrojích nebo uživatelských rozhraních namísto názvu zdroje události. |
     | eventSourceTimestampPropertyName | Vlastnost události, která bude použita jako časové razítko zdroje události. Pokud hodnota není zadána pro timestampPropertyName nebo pokud je zadán a null nebo prázdný řetězec, bude použit čas vytvoření události. |
     | eventSourceKeyName | Název sdíleného přístupového klíče, který služba Time Series Insights použije pro připojení k centru událostí. |
     | accessPolicyReaderObjectIds | Seznam ID objektů uživatelů nebo aplikací ve službě Azure AD, které by měly mít přístup ke čtenáři prostředí. Id objektu instančního objektu lze získat voláním rutin **Get-AzADUser** nebo **Get-AzADServicePrincipal.** Vytvoření zásadpřístupu pro skupiny Azure AD ještě není podporováno. |
     | accessPolicyContributorObjectIds | Seznam ID objektů uživatelů nebo aplikací ve službě Azure AD, které by měly mít přístup přispěvatele k prostředí. Id objektu instančního objektu lze získat voláním rutin **Get-AzADUser** nebo **Get-AzADServicePrincipal.** Vytvoření zásadpřístupu pro skupiny Azure AD ještě není podporováno. |

   * Jako příklad by následující parametry souboru použít k vytvoření prostředí a zdroj událostí, který čte události z existujícího centra událostí. Také vytvoří dvě zásady přístupu, které udělují přispěvateli přístup k prostředí.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```

    * Další informace naleznete v článku [Parametry.](../azure-resource-manager/templates/parameter-files.md)

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Nasazení šablony rychlého startu místně pomocí Prostředí PowerShell

> [!IMPORTANT]
> Níže uvedené operace příkazového řádku popisují [modul Az PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. V PowerShellu se přihlaste ke svému účtu Azure.

    * Z výzvy prostředí PowerShell spusťte následující příkaz:

      ```powershell
      Connect-AzAccount
      ```

    * Budete vyzváni k přihlášení k účtu Azure. Po přihlášení zobrazte dostupná předplatná následujícím příkazem:

      ```powershell
      Get-AzSubscription
      ```

    * Tento příkaz vrátí seznam dostupných předplatných Azure. Vyberte předplatné pro aktuální relaci spuštěním následujícího příkazu. Nahraďte `<YourSubscriptionId>` identifikátorem GUID pro předplatné Azure, které chcete použít:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Vytvořte novou skupinu prostředků, pokud neexistuje.

   * Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu prostředků pomocí příkazu **New-AzResourceGroup.** Zadejte název skupiny prostředků a umístění, které chcete použít. Například:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * V případě úspěchu se zobrazí souhrn nové skupiny prostředků.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Otestujte nasazení.

   * Ověřte nasazení `Test-AzResourceGroupDeployment` spuštěním rutiny. Při testování nasazení zadejte parametry přesně tak, jak byste při provádění nasazení.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Vytvoření nasazení

    * Chcete-li vytvořit nové `New-AzResourceGroupDeployment` nasazení, spusťte rutinu a po zobrazení výzvy zadejte potřebné parametry. Parametry zahrnují název vašeho nasazení, název skupiny prostředků a cestu nebo adresu URL k souboru šablony. Pokud není zadán parametr **Mode,** použije se výchozí hodnota **Přírůstková.** Další informace naleznete [v informacích, které jsou přírůstkové a úplné .](../azure-resource-manager/templates/deployment-modes.md)

    * Následující příkaz zobrazí výzvu k zadání pěti požadovaných parametrů v okně Prostředí PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Chcete-li místo toho určit soubor parametrů, použijte následující příkaz:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Při spuštění rutiny nasazení můžete také použít vsazené parametry. Příkaz vypadá takto:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Chcete-li spustit [úplné](../azure-resource-manager/templates/deployment-modes.md) nasazení, nastavte parametr **Režim** na **Hodnotu Dokončeno**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Ověření nasazení

    * Pokud jsou prostředky nasazeny úspěšně, zobrazí se v okně Prostředí PowerShell souhrn nasazení:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Nasazení šablony rychlého startu prostřednictvím portálu Azure

   * Domovská stránka šablony rychlého startu na GitHubu obsahuje také tlačítko **Nasadit do Azure.** Kliknutím na něj otevře stránku vlastní nasazení na webu Azure Portal. Na této stránce můžete zadat nebo vybrat hodnoty pro každý z parametrů z [požadovaných parametrů](#required-parameters) nebo [volitelných parametrů](#optional-parameters) tabulky. Po vyplnění nastavení kliknutím na tlačítko **Nákup** zahájíte nasazení šablony.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Další kroky

- Informace o programové správě prostředků Time Series Insights pomocí api REST naleznete v [tématu Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/).
