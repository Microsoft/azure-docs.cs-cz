---
title: Správa prostředí pomocí šablon Azure Resource Manager – Azure Time Series Insights | Microsoft Docs
description: Naučte se spravovat prostředí Azure Time Series Insights programově pomocí Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011655"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Vytvoření Azure Time Series Insightsch prostředků 1. generace pomocí šablon Azure Resource Manager

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje, jak vytvořit a nasadit Azure Time Series Insights prostředky pomocí [šablon Azure Resource Manager](../azure-resource-manager/index.yml), PowerShellu a poskytovatele prostředků Azure Time Series Insights.

Azure Time Series Insights podporuje následující zdroje:

   | Prostředek | Popis |
   | --- | --- |
   | Prostředí | Azure Time Series Insights prostředí je logické seskupení událostí, které jsou čteny od zprostředkovatelů událostí, uloženy a zpřístupněny pro dotaz. Další informace najdete v článku [plánování Azure Time Series Insightsho prostředí](time-series-insights-environment-planning.md) . |
   | Zdroj události | Zdroj události je připojení ke zprostředkovateli událostí, ze kterého Azure Time Series Insights čte události do prostředí a ingestuje je. Aktuálně podporované zdroje událostí jsou IoT Hub a centra událostí. |
   | Referenční sada dat | Referenční sady dat poskytují metadata o událostech v prostředí. Metadata v referenčních sadách dat budou během příchozího přenosu propojena s událostmi. Referenční sady dat jsou definovány jako prostředky podle jejich vlastností klíče události. Skutečná metadata, která tvoří referenční datovou sadu, se nahrají nebo upraví prostřednictvím rozhraní API roviny dat. |
   | Zásady přístupu | Zásady přístupu udělují oprávnění k vydávání dotazů na data, manipulaci s referenčními daty v prostředí a sdílení uložených dotazů a perspektiv přidružených k prostředí. Další informace najdete v článku [udělení přístupu k datům Azure Time Series Insights prostředí pomocí Azure Portal](./concepts-access-policies.md) |

Šablona Správce prostředků je soubor JSON, který definuje infrastrukturu a konfiguraci prostředků ve skupině prostředků. Následující dokumenty popisují soubory šablon podrobněji:

- [Nasazení šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
- [Typy prostředků Microsoft. TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Šablona pro rychlý Start [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) je publikovaná na GitHubu. Tato šablona vytvoří prostředí Azure Time Series Insights, podřízený zdroj událostí nakonfigurovaný tak, aby využíval události z centra událostí, a zásady přístupu, které udělují přístup k datům prostředí. Pokud není zadané existující centrum událostí, vytvoří se s nasazením.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Zadat šablonu a parametry nasazení

Následující postup popisuje, jak pomocí PowerShellu nasadit šablonu Azure Resource Manager, která vytváří Azure Time Series Insights prostředí, podřízený zdroj událostí, který je nakonfigurovaný pro využívání událostí z centra událostí, a zásady přístupu, které udělují přístup k datům prostředí. Pokud není zadané existující centrum událostí, vytvoří se s nasazením.

1. Nainstalujte Azure PowerShell podle pokynů v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

1. Naklonujte nebo zkopírujte šablonu [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) z GitHubu.

   - Vytvoření souboru parametrů

     Chcete-li vytvořit soubor parametrů, zkopírujte soubor [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) .

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Povinné parametry

     | Parametr | Popis |
     | --- | --- |
     | eventHubNamespaceName | Obor názvů zdrojového centra událostí |
     | eventHubName | Název zdrojového centra událostí. |
     | consumerGroupName | Název skupiny uživatelů, kterou bude služba Azure Time Series Insights používat ke čtení dat z centra událostí. **Poznámka:** Aby se zabránilo kolizi prostředků, musí být tato skupina uživatelů vyhrazena službě Azure Time Series Insights a nesdílela se s ostatními čtenáři. |
     | environmentName | Název prostředí. Název nesmí obsahovat tyto   `<` `>` řídicí znaky:,,,, `%` `&` `:` , `\\` , `?` , `/` a. Všechny ostatní znaky jsou povolené.|
     | eventSourceName | Název podřízeného prostředku zdroje události. Název nesmí obsahovat tyto   `<` `>` řídicí znaky:,,,, `%` `&` `:` , `\\` , `?` , `/` a. Všechny ostatní znaky jsou povolené. |

    <div id="optional-parameters"></div>

   - Volitelné parametry

     | Parametr | Popis |
     | --- | --- |
     | existingEventHubResourceId | Volitelné ID prostředku existujícího centra událostí, které bude připojeno ke Azure Time Series Insights prostředí prostřednictvím zdroje událostí. **Poznámka:** Uživatel, který šablonu nasazuje, musí mít oprávnění k provedení operace klíče listkey v centru událostí. Pokud není předána žádná hodnota, vytvoří se v šabloně nové centrum událostí. |
     | environmentDisplayName | Volitelný popisný název, který se zobrazí v nástrojích nebo uživatelských rozhraních místo názvu prostředí. |
     | environmentSkuName | Název sku. Další informace najdete na stránce s [cenami pro Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Kapacita jednotky SKU. Další informace najdete na stránce s [cenami pro Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Minimální časové rozpětí, které budou události prostředí k dispozici pro dotaz. Hodnota musí být zadána ve formátu ISO 8601, například `P30D` pro zásady uchovávání informací po dobu 30 dnů. |
     | eventSourceDisplayName | Volitelný popisný název, který se zobrazí v nástrojích nebo uživatelských rozhraních namísto názvu zdroje události. |
     | eventSourceTimestampPropertyName | Vlastnost události, která bude použita jako časové razítko zdroje události. Pokud není zadána hodnota pro timestampPropertyName, nebo pokud je zadána hodnota null nebo prázdný řetězec, bude použit čas vytvoření události. |
     | eventSourceKeyName | Název sdíleného přístupového klíče, který bude služba Azure Time Series Insights používat pro připojení k centru událostí. |
     | accessPolicyReaderObjectIds | Seznam ID objektů uživatelů nebo aplikací ve službě Azure AD, který by měl mít přístup čtenář k prostředí. ObjectId instančního objektu se dá získat voláním rutin **Get-AzADUser** nebo **Get-AzADServicePrincipal** . Vytváření zásad přístupu pro skupiny Azure AD ještě není podporované. |
     | accessPolicyContributorObjectIds | Seznam ID objektů uživatelů nebo aplikací ve službě Azure AD, který by měl mít přístup Přispěvatel k prostředí. ObjectId instančního objektu se dá získat voláním rutin **Get-AzADUser** nebo **Get-AzADServicePrincipal** . Vytváření zásad přístupu pro skupiny Azure AD ještě není podporované. |

   - Například následující soubor parametrů by se použil k vytvoření prostředí a zdroje událostí, který čte události z existujícího centra událostí. Vytvoří také dvě zásady přístupu, které udělí přispěvatelům přístup k prostředí.

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

   - Další informace najdete v článku o [parametrech](../azure-resource-manager/templates/parameter-files.md) .

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Nasazení šablony pro rychlé zprovoznění místně pomocí PowerShellu

> [!IMPORTANT]
> Níže zobrazené operace příkazového řádku popisují [AZ PowerShell Module](/powershell/azure/).

1. V PowerShellu se přihlaste ke svému účtu Azure.

    - Z příkazového řádku PowerShellu spusťte následující příkaz:

      ```powershell
      Connect-AzAccount
      ```

    - Zobrazí se výzva, abyste se přihlásili ke svému účtu Azure. Po přihlášení spusťte následující příkaz, který zobrazí vaše dostupná předplatná:

      ```powershell
      Get-AzSubscription
      ```

    - Tento příkaz vrátí seznam dostupných předplatných Azure. Spusťte následující příkaz a vyberte odběr aktuální relace. Nahraďte `<YourSubscriptionId>` identifikátorem GUID předplatného Azure, které chcete použít:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Pokud jeden z nich neexistuje, vytvořte novou skupinu prostředků.

   - Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu prostředků pomocí příkazu **New-AzResourceGroup** . Zadejte název skupiny prostředků a umístění, které chcete použít. Například:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - V případě úspěchu se zobrazí souhrn nové skupiny prostředků.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Otestujte nasazení.

   - Ověřte nasazení spuštěním `Test-AzResourceGroupDeployment` rutiny. Při testování nasazení zadejte parametry přesně stejně jako při spuštění nasazení.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Vytvoření nasazení

    - Pokud chcete vytvořit nové nasazení, spusťte `New-AzResourceGroupDeployment` rutinu a po zobrazení výzvy zadejte potřebné parametry. Parametry zahrnují název vašeho nasazení, název vaší skupiny prostředků a cestu nebo adresu URL k souboru šablony. Pokud není zadán parametr **Mode** , je použita výchozí hodnota pro **přírůstkové** . Další informace najdete v tématu [přírůstková a kompletní nasazení](../azure-resource-manager/templates/deployment-modes.md).

    - Následující příkaz vás vyzve k zadání pěti požadovaných parametrů v okně PowerShellu:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Chcete-li místo toho zadat soubor parametrů, použijte následující příkaz:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - Vložené parametry můžete použít také při spuštění rutiny nasazení. Příkaz vypadá takto:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Chcete-li spustit [kompletní](../azure-resource-manager/templates/deployment-modes.md) nasazení, nastavte parametr **Mode** na hodnotu **Dokončit**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Ověření nasazení

    - Pokud se prostředky úspěšně nasazují, v okně PowerShellu se zobrazí souhrn nasazení:

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

1. Nasazení šablony pro rychlý Start pomocí Azure Portal

   - Domovská stránka šablony pro rychlý Start na GitHubu obsahuje také tlačítko **nasadit do Azure** . Kliknutím na něj otevřete stránku vlastního nasazení v Azure Portal. Na této stránce můžete zadat nebo vybrat hodnoty pro každý z parametrů z [požadovaných parametrů](#required-parameters) nebo z tabulek [volitelného parametru](#optional-parameters) . Po vyplnění nastavení se kliknutím na tlačítko **koupit** iniciuje nasazení šablony.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Další kroky

- Informace o programové správě Azure Time Series Insightsch prostředků pomocí rozhraní REST API najdete v článku [správa Azure Time Series Insights Management](/rest/api/time-series-insights-management/).