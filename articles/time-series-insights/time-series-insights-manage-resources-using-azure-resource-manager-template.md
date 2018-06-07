---
title: Správa prostředí Statistika řady čas Azure pomocí šablon Azure Resource Manager | Microsoft Docs
description: Tento článek popisuje, jak ke správě prostředí Azure časové řady Statistika programově pomocí Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 99aabc01132da60a1b09fcf65f439b8e084bbffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651960"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Vytvoření statistiky časové řady prostředků pomocí šablony Azure Resource Manager

Tento článek popisuje, jak vytvořit a nasadit časové řady Statistika prostředků pomocí šablony Azure Resource Manager, prostředí PowerShell a poskytovatele prostředků Statistika časové řady.

Časové řady Insights podporuje následující prostředky:
   | Prostředek | Popis |
   | --- | --- |
   | Prostředí | Prostředí časové řady Insights je logické seskupení událostí, které jsou číst z událostí zprostředkovatelé, uloženy a k dispozici pro dotaz. Další informace najdete v části [plánování prostředí Statistika Azure časové řady](time-series-insights-environment-planning.md) |
   | Zdroj události | Zdroje událostí je připojení k události služby broker, ze kterých časové řady Statistika čte a ingestuje události do prostředí. Zdroje událostí aktuálně podporované jsou IoT Hub a centra událostí. |
   | Referenční datové sady | Referenční datové sady poskytují metadata o událostech v prostředí. Připojený metadata v referenční datové sady s událostmi během příchozí. Referenční datové sady jsou definovány jako prostředky jejich vlastnosti klíče události. Skutečné metadata, která tvoří sadu dat odkaz se nahrál nebo upraveny prostřednictvím datové roviny rozhraní API. |
   | Zásady přístupu | Zásady přístupu udělit oprávnění k vydávat dotazy na data, pracovat s daty odkaz v prostředí a sdílet uložené dotazy a perspektivy, které jsou spojené s prostředím. Další informace najdete v části [udělit přístup k datům časové řady Statistika prostředí pomocí portálu Azure](time-series-insights-data-access.md) |

Šablony Resource Manageru je soubor JSON, která definuje infrastrukturu a konfiguraci prostředků ve skupině prostředků. Další informace najdete v následujících dokumentech:

- [Přehled Azure Resource Manageru - nasazení šablony](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)

[201-timeseriesinsights prostředí s eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) šablony rychlý start je publikována na Githubu. Tato šablona vytvoří prostředí časové řady Statistika zdroji událostí podřízené nakonfigurován, aby přijímat události z centra událostí a získat přístup k zásadám, která udělují přístup k datům v prostředí. Pokud není zadán existující centra událostí, bude vytvořen s nasazením.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Nasazení šablony rychlý start místně pomocí prostředí PowerShell

Následující postup popisuje, jak pomocí prostředí PowerShell pro nasazení šablonu Azure Resource Manager, která vytvoří prostředí časové řady Statistika zdroji událostí podřízené nakonfigurován, aby přijímat události z centra událostí a zásady, která udělují přístup k přístupu data na prostředí. Pokud není zadán existující centra událostí, bude vytvořen s nasazením.

Přibližná pracovní postup je následující:

1. Instalace prostředí PowerShell.
1. Vytvořte šablonu a soubor s parametry.
1. V prostředí PowerShell Přihlaste se k účtu Azure.
1. Pokud žádný neexistuje, vytvořte novou skupinu prostředků.
1. Testovací nasazení.
1. Nasazení šablony.

### <a name="install-powershell"></a>Instalace PowerShellu

Nainstalovat Azure PowerShell podle pokynů v [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Vytvoření šablony

Klonování nebo kopírování [201-timeseriesinsights prostředí s eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) šablony z Githubu.

### <a name="create-a-parameters-file"></a>Vytvořte soubor parametrů

Pokud chcete vytvořit soubor parametrů, zkopírujte [201-timeseriesinsights prostředí s eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) souboru.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Požadované parametry

   | Parametr | Popis |
   | --- | --- |
   | eventHubNamespaceName | Obor názvů zdroj centra událostí. |
   | eventHubName | Název centra událostí zdroje. |
   | consumerGroupName | Název skupiny uživatelů, který službě Statistika řady čas bude používat ke čtení dat z centra událostí. **Poznámka:** předejít sporu prostředků, musí být tato skupina uživatelů vyhrazený ke službě Statistika řady čas a nesdílí se s další čtečky. |
   | EnvironmentName | Název prostředí. Název nemůže obsahovat: ' <', ' >', '%', 'a', ': ','\\','?', '/' a žádné řídicí znaky. Všechny ostatní znaky jsou povoleny.|
   | eventSourceName | Název prostředku podřízené zdroje událostí. Název nemůže obsahovat: ' <', ' >', '%', 'a', ': ','\\','?', '/' a žádné řídicí znaky. Všechny ostatní znaky jsou povoleny. |

#### <a name="optional-parameters"></a>Volitelné parametry

   | Parametr | Popis |
   | --- | --- |
   | existingEventHubResourceId | ID prostředku volitelné existující centra událostí, které bude připojené k prostředí časové řady Statistika prostřednictvím zdroj události. **Poznámka:** uživatel nasazení šablony musí mít oprávnění k provedení operace listkeys v Centru událostí. Pokud není předána žádná hodnota, vytvoří se šablonou nového centra událostí. |
   | environmentDisplayName | Volitelné popisný název pro zobrazení v nástroji nebo uživatelské rozhraní místo názvu prostředí. |
   | environmentSkuName | Název verze SKU. Další informace najdete v tématu [stránce s cenami Statistika časové řady](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Jednotka kapacity verze SKU. Další informace najdete v tématu [stránce s cenami Statistika časové řady](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Minimální časový interval v prostředí události bude k dispozici pro dotaz. Hodnota musí zadat ve formátu ISO 8601, například "P30D" pro zásady uchovávání informací 30 dní. |
   | eventSourceDisplayName | Volitelné popisný název pro zobrazení v nástroji nebo uživatelské rozhraní místo názvu zdroje událostí. |
   | eventSourceTimestampPropertyName | Vlastnosti události, který se použije jako zdroj událostí časové razítko. Pokud není zadána hodnota pro timestampPropertyName, nebo pokud je zadána hodnota null nebo prázdný řetězec, použije se čas vytvoření události. |
   | eventSourceKeyName | Název sdílený přístupový klíč, který službě Statistika řady čas bude používat pro připojení k Centru událostí. |
   | accessPolicyReaderObjectIds | Seznam ID uživatele nebo aplikace ve službě Azure AD, který by měl být čtečky přístup k prostředí objektů. Hlavní objectId služby je možné získat volání **Get-AzureRMADUser** nebo **Get-AzureRMADServicePrincipal** rutiny. Vytvoření zásad přístupu pro skupiny Azure AD není dosud podporováno. |
   | accessPolicyContributorObjectIds | Seznam ID uživatele nebo aplikace ve službě Azure AD, který by měl být Přispěvatel přístup k prostředí objektů. Hlavní objectId služby je možné získat volání **Get-AzureRMADUser** nebo **Get-AzureRMADServicePrincipal** rutiny. Vytvoření zásad přístupu pro skupiny Azure AD není dosud podporováno. |

Jako příklad následující parametry soubor se použije k vytvoření prostředí a zdroje událostí, který čte události z existující centra událostí. Vytvoří také dvě zásady přístupu, která udělují přístup přispěvatele do prostředí.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Další informace najdete v tématu [parametry](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) článku.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Přihlaste se k Azure a nastavte předplatné Azure

Z řádku prostředí PowerShell spusťte následující příkaz:

```powershell
Connect-AzureRmAccount
```

Zobrazí se výzva k přihlášení k účtu Azure. Po přihlášení, spusťte následující příkaz k zobrazení dostupných předplatných:

```powershell
Get-AzureRMSubscription
```

Tento příkaz vrátí seznam dostupných předplatných Azure. Spuštěním následujícího příkazu vyberte předplatné pro aktuální relaci. Nahraďte `<YourSubscriptionId>` s identifikátorem GUID pro předplatné Azure, kterou chcete použít:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Nastavit skupinu prostředků

Pokud nemáte existující prostředek skupiny, vytvořte novou skupinu prostředků s **New-AzureRmResourceGroup** příkaz. Zadejte název skupiny prostředků a umístění, do kterého chcete použít. Příklad:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Pokud bylo úspěšné, zobrazí se souhrn novou skupinu prostředků.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Otestování nasazení

Ověření nasazení tak, že spustíte `Test-AzureRmResourceGroupDeployment` rutiny. Při testování nasazení, zadejte parametry přesně stejně jako při provádění nasazení.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Vytvoření nasazení

Chcete-li vytvořit nové nasazení, spusťte `New-AzureRmResourceGroupDeployment` rutiny a zadejte potřebné parametry po zobrazení výzvy. Parametry jsou název pro vaše nasazení, název vaší skupiny prostředků a cesta nebo adresa URL k souboru šablony. Pokud **režimu** není zadán parametr, výchozí hodnota **přírůstkové** se používá. Další informace najdete v tématu [přírůstkové a úplné nasazení](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Následující příkaz vás vyzve k zadání pět požadované parametry v okně prostředí PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Místo toho zadat soubor parametrů, použijte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vložené parametry můžete použít také při spuštění rutiny nasazení. Příkaz vypadá takto:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Ke spuštění [dokončení](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) nasazení, nastavte **režimu** parametru **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Ověření nasazení

Pokud prostředky jsou nasazeny úspěšně, zobrazí se souhrn nasazení v okně prostředí PowerShell:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Nasazení šablony rychlý start prostřednictvím portálu Azure

Také obsahuje šablony rychlý start domovské stránky na webu GitHub **nasadit do Azure** tlačítko. Na něj kliknete, otevře se stránka vlastní nasazení na portálu Azure. Z této stránky zadejte nebo vyberte hodnoty pro každý z parametrů z [povinné parametry](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) nebo [volitelné parametry](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabulky. Po vyplnění nastavení kliknutím **nákupu** tlačítko zahájí nasazení šablony.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Další postup

- Informace o správě prostřednictvím kódu programu časové řady Přehled prostředků pomocí rozhraní REST API najdete v tématu [Správa Insights řady času](https://docs.microsoft.com/rest/api/time-series-insights-management/).
