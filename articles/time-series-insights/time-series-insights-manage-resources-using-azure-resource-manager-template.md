---
title: Správa prostředí Azure Time Series Insights pomocí šablon Azure Resource Manageru | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat prostředí Azure Time Series Insights prostřednictvím kódu programu pomocí Azure Resource Manageru.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: f5e350e8a9093936f1e747afda7c3192b4d8368d
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471714"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Vytvořit prostředky služby Time Series Insights pomocí šablon Azure Resource Manageru

Tento článek popisuje postup vytvoření a nasazení prostředků Time Series Insights pomocí šablony Azure Resource Manageru, Powershellu a poskytovatele prostředků služby Time Series Insights.

Time Series Insights podporuje následující zdroje:

   | Resource | Popis |
   | --- | --- |
   | Prostředí | Prostředí Time Series Insights je logické seskupení událostí, které se čtou zprostředkovatelé událostí, uložených a k dispozici pro dotaz. Další informace najdete v tématu [plánování prostředí Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Zdroj události | Zdroj událostí je připojení k zprostředkovatele událostí, ze kterého čte Time Series Insights a ingestuje události do prostředí. Zdroje událostí aktuálně podporované se službou IoT Hub a centra událostí. |
   | Referenční sady dat. | Referenční datové sady poskytují metadata o událostech v prostředí. Metadata v referenční datové sady se jde připojit k události během příchozího přenosu. Referenční datové sady se definují jako prostředky podle jejich vlastnosti klíče události. Skutečné metadat, který vytvoří referenční sady dat se nahraje nebo změní prostřednictvím roviny dat rozhraní API. |
   | Zásady přístupu | Zásady přístupu k udělení oprávnění k vydávání dotazů na data, zpracování referenčních dat v prostředí a sdílení uložených dotazů a perspektiv přidruženým k prostředí. Další informace najdete v článku [udělení přístupu k datům do prostředí Time Series Insights pomocí webu Azure portal](time-series-insights-data-access.md) |

Šablony Resource Manageru je soubor JSON, která definuje infrastrukturu a konfiguraci prostředků ve skupině prostředků. Tyto dokumenty popisují soubory šablon podrobněji:

- [Přehled Azure Resource Manageru – nasazení šablony](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
- [Typy prostředků Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

[201-timeseriesinsights prostředí s eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) šablonu pro rychlý start se publikoval na Githubu. Tato šablona vytvoří prostředí Time Series Insights, zdroj události podřízené nakonfigurovaný tak, aby přijímat události z centra událostí a zásadami, které udělují přístup k datům prostředí. Pokud není zadaný existující centrum událostí, bude vytvořen s nasazením.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Zadejte šablonu nasazení a parametry

Následující postup popisuje použití Powershellu k nasazení šablony Azure Resource Manageru, který vytvoří prostředí Time Series Insights, zdroj události podřízené nakonfigurovaný tak, aby přijímat události z centra událostí a zásady, které udělují přístup k přístupu data prostředí. Pokud není zadaný existující centrum událostí, bude vytvořen s nasazením.

1. Nainstalovat Azure PowerShell podle pokynů v [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps).

1. Klonování nebo kopírování [201-timeseriesinsights prostředí s eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) šablony z Githubu.

   * Vytvoření souboru parametrů

     Chcete-li vytvořit soubor parametrů, zkopírujte [201-timeseriesinsights prostředí s eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) souboru.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Požadované parametry

     | Parametr | Popis |
     | --- | --- |
     | eventHubNamespaceName | Obor názvů centra událostí, zdroje. |
     | eventHubName | Název centra událostí zdroje. |
     | consumerGroupName | Název skupiny příjemců, který bude služba Time Series Insights používat ke čtení dat z centra událostí. **POZNÁMKA:** Předejít sporu prostředků, musí tuto skupinu příjemců vyhrazená pro služby Time Series Insights a neměly by být sdíleny s jinými čtenáři. |
     | environmentName | Název prostředí. Název nesmí obsahovat: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, a žádné řídicí znaky. Všechny ostatní znaky jsou povolené.|
     | eventSourceName | Název prostředku podřízený zdroj událostí. Název nesmí obsahovat: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, a žádné řídicí znaky. Všechny ostatní znaky jsou povolené. |

    <div id="optional-parameters"></div>

   * Volitelné parametry

     | Parametr | Popis |
     | --- | --- |
     | existingEventHubResourceId | Volitelné resource ID existující centrum událostí, které budou připojeny k prostředí Time Series Insights prostřednictvím zdroje událostí. **POZNÁMKA:** Uživatel nasazení šablony musí mít oprávnění k provedení této operace klíče listkey v Centru událostí. Pokud není předána žádná hodnota, vytvoří se nové Centrum událostí pomocí šablony. |
     | environmentDisplayName | Volitelný popisný název pro zobrazení v nástroji nebo uživatelské rozhraní místo názvu prostředí. |
     | environmentSkuName | Název sku. Další informace najdete v tématu [stránce s cenami čas Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Kapacita jednotek SKU. Další informace najdete v tématu [stránce s cenami čas Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Minimální časový rozsah události prostředí bude k dispozici pro dotaz. Nutné zadat hodnotu ve formátu ISO 8601, například `P30D` pro zásady uchovávání informací o 30 dnech. |
     | eventSourceDisplayName | Volitelný popisný název pro zobrazení v rozhraní nástroje nebo uživatele místo názvu zdroje událostí. |
     | eventSourceTimestampPropertyName | Vlastnosti události, který se použije jako časové razítko zdroje událostí. Pokud není zadána hodnota pro timestampPropertyName nebo pokud je zadána hodnota null nebo prázdný řetězec, použije se čas vytvoření události. |
     | eventSourceKeyName | Název sdílený přístupový klíč, který bude služba Time Series Insights používat pro připojení k Centru událostí. |
     | accessPolicyReaderObjectIds | Seznam ID uživatele nebo aplikace ve službě Azure AD, který by měl mít čtečky přístup k prostředí objektů. ObjectId instančního objektu služby lze získat voláním **Get-AzADUser** nebo **Get-AzADServicePrincipal** rutiny. Vytvoření zásad přístupu pro skupiny služby Azure AD se ještě nepodporuje. |
     | accessPolicyContributorObjectIds | Seznam ID uživatele nebo aplikace ve službě Azure AD, který by měl mít přístup přispěvatele k prostředí objektů. ObjectId instančního objektu služby lze získat voláním **Get-AzADUser** nebo **Get-AzADServicePrincipal** rutiny. Vytvoření zásad přístupu pro skupiny služby Azure AD se ještě nepodporuje. |

   * Například následující soubor parametrů se použije k vytvoření prostředí a zdroje událostí, která načítá události z existující centrum událostí. Vytvoří také dvě zásady přístupu, které udělují přístup přispěvatele k prostředí.

     ```json
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
  
    * Další informace najdete v tématu [parametry](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) článku.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Nasazení šablony rychlý start místně s použitím prostředí PowerShell

> [!IMPORTANT]
> Operace příkazového řádku, které jsou zobrazeny níže popisují [modulu Powershellu pro Az](https://docs.microsoft.com/powershell/azure/overview).

1. V prostředí PowerShell Přihlaste se ke svému účtu Azure.

    * Z příkazového řádku Powershellu spusťte následující příkaz:

      ```powershell
      Connect-AzAccount
      ```

    * Zobrazí se výzva k přihlášení k účtu Azure. Po přihlášení, spusťte následující příkaz, chcete-li zobrazit dostupná předplatná:

      ```powershell
      Get-AzSubscription
      ```

    * Tento příkaz vrátí seznam hodnot dostupná předplatná Azure. Spuštěním následujícího příkazu vyberte předplatné pro aktuální relaci. Nahraďte `<YourSubscriptionId>` s identifikátorem GUID předplatného Azure, kterou chcete použít:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Pokud ještě neexistuje, vytvořte novou skupinu prostředků.

   * Pokud nemáte existující prostředek skupiny, vytvořte novou skupinu prostředků s **New-AzResourceGroup** příkazu. Zadejte název skupiny prostředků a umístění, které chcete použít. Příklad:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * V případě úspěchu, zobrazí se souhrn novou skupinu prostředků.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Test nasazení.

   * Ověřit nasazení spuštěním `Test-AzResourceGroupDeployment` rutiny. Při testování nasazení, zadejte parametry stejným způsobem jako při spuštění nasazení.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Vytvoření nasazení

    * Pokud chcete vytvořit nové nasazení, spusťte `New-AzResourceGroupDeployment` rutiny a zadejte potřebné parametry po zobrazení výzvy. Parametry jsou název pro nasazení, název vaší skupiny prostředků a cesta nebo adresa URL k souboru šablony. Pokud **režimu** parametr není zadán, výchozí hodnota **přírůstkové** se používá. Další informace najdete v tématu [přírůstkové a úplné nasazení](../azure-resource-manager/deployment-modes.md).

    * Následující příkaz vás vyzve k zadání pět požadovaných parametrů v okně Powershellu:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Chcete-li místo toho zadejte soubor parametrů, použijte následující příkaz:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Můžete také použít vložených parametrů při spuštění rutiny nasazení. Příkaz vypadá takto:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Ke spuštění [kompletní](../azure-resource-manager/deployment-modes.md) nasazení, nastavte **režimu** parametr **Complete**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Ověření nasazení

    * Pokud se prostředky nasadí úspěšně, zobrazí se souhrn nasazení v okně Powershellu:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 5/8/2019 10:28:34 PM
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

1. Nasazení šablony rychlý start na webu Azure portal

   * Domovská stránka šablony quickstart na Githubu zahrnuje také **nasadit do Azure** tlačítko. Kliknutím otevřete stránku vlastní nasazení na webu Azure Portal. Na této stránce můžete zadat nebo vybrat hodnoty pro každý z parametrů z [požadované parametry](#required-parameters) nebo [volitelné parametry](#optional-parameters) tabulky. Po vyplnění nastavení kliknutím **nákupní** tlačítko opraví, zahájí se nasazení šablony.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Další postup

- Informace o Programová správa zdroje Time Series Insights pomocí rozhraní REST API najdete v tématu [Správa času Series Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
