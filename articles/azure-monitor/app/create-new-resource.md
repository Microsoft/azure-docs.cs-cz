---
title: Vytvořit nový prostředek služby Azure Application Insights | Microsoft Docs
description: Ručně nastavte Application Insights monitorování pro novou živou aplikaci.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2e367e84aed7abac70d803f28d26070f7b0a85e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323124"
---
# <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Azure Application Insights zobrazuje data o vaší aplikaci v *prostředku*Microsoft Azure. Vytvoření nového prostředku je proto součástí [nastavení Application Insights k monitorování nové aplikace][start]. Po vytvoření nového prostředku můžete získat jeho klíč instrumentace a použít ho ke konfiguraci sady Application Insights SDK. Klíč instrumentace propojuje vaši telemetrii s prostředkem.

## <a name="sign-in-to-microsoft-azure"></a>Přihlášení k Microsoft Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Přihlaste se k [Azure Portal](https://portal.azure.com)a vytvořte prostředek Application Insights:

![Klikněte na symbol + v levém horním rohu. Vyberte Vývojářské nástroje následované Application Insights](./media/create-new-resource/new-app-insights.png)

   | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | `Unique value` | Název, který identifikuje monitorovanou aplikaci. |
   | **Skupina prostředků**     | `myResourceGroup`      | Název nové nebo existující skupiny prostředků pro hostování dat App Insights |
   | **Oblast** | `East US` | Vyberte umístění poblíž vaší aplikace nebo poblíž místa, kde je vaše aplikace hostovaná. |
   | **Režim prostředků** | `Classic` nebo `Workspace-based` | Prostředky založené na pracovních prostorech jsou momentálně ve verzi Public Preview a umožňují vám odeslat Application Insights telemetrii do společného pracovního prostoru Log Analytics. Další informace najdete v [článku o prostředcích založených na pracovních prostorech](create-workspace-resource.md).

> [!NOTE]
> I když můžete použít stejný název prostředku v různých skupinách prostředků, může být výhodné použít globálně jedinečný název. To může být užitečné v případě, že plánujete [provádění dotazů mezi prostředky](../log-query/cross-workspace-query.md#identifying-an-application) , protože je zjednodušená požadovaná syntaxe.

Zadejte příslušné hodnoty do požadovaných polí a potom vyberte **zkontrolovat + vytvořit**.

![Zadejte hodnoty do požadovaných polí a potom vyberte "zkontrolovat + vytvořit".](./media/create-new-resource/review-create.png)

Po vytvoření aplikace se otevře nové podokno. V tomto podokně vidíte data o výkonu a využití monitorovaných aplikací. 

## <a name="copy-the-instrumentation-key"></a>Zkopírování klíče instrumentace

Klíč instrumentace identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Budete muset zkopírovat klíč instrumentace a přidat ho do kódu vaší aplikace.

![Klikněte a zkopírujte klíč instrumentace.](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalace sady SDK do aplikace

Nainstalujte do aplikace sadu Application Insights SDK. Tento krok závisí silně na typu vaší aplikace.

Použijte klíč instrumentace ke konfiguraci [sady SDK, kterou nainstalujete do aplikace][start].

Sada SDK obsahuje standardní moduly, které odesílají telemetrii, aniž byste museli psát žádný další kód. Pokud chcete sledovat akce uživatelů nebo diagnostikovat problémy podrobněji, [použijte rozhraní API][api] k odeslání vlastní telemetrie.

## <a name="creating-a-resource-automatically"></a>Automatické vytvoření prostředku

### <a name="powershell"></a>PowerShell

Vytvořit nový prostředek Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Příklad

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Výsledky

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Úplnou dokumentaci k prostředí PowerShell pro tuto rutinu a informace o tom, jak načíst klíč instrumentace, najdete v [dokumentaci Azure PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Rozhraní příkazového řádku Azure (Preview)

Abyste měli přístup k verzi Preview Application Insights příkazy rozhraní příkazového řádku Azure CLI, musíte nejdřív spustit:

```azurecli
 az extension add -n application-insights
```

Pokud tento příkaz nespustíte `az extension add` , zobrazí se chybová zpráva s oznámením: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nyní můžete spustit následující a vytvořit prostředek Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Příklad

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Výsledky

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Úplnou dokumentaci k Azure CLI pro tento příkaz a informace o tom, jak načíst klíč instrumentace, najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Další kroky
* [Diagnostické hledání](./diagnostic-search.md)
* [Zkoumání metrik](../platform/metrics-charts.md)
* [Psaní analytických dotazů](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

