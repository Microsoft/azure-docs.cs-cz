---
title: Vytvoření nového prostředku Azure Application Insights | Dokumenty společnosti Microsoft
description: Ručně nastavte monitorování Application Insights pro novou živou aplikaci.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537572"
---
# <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Azure Application Insights zobrazuje data o vaší aplikaci v *prostředku*Microsoft Azure . Vytvoření nového prostředku je proto součástí [nastavení Application Insights pro sledování nové aplikace][start]. Po vytvoření nového prostředku můžete získat jeho instrumentační klíč a použít jej ke konfiguraci sady Application Insights SDK. Klíč instrumentace propojuje telemetrická data s prostředkem.

## <a name="sign-in-to-microsoft-azure"></a>Přihlášení k Microsoft Azure

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Přihlaste se k [portálu Azure](https://portal.azure.com)a vytvořte prostředek Application Insights:

![Klikněte na znaménko +v levém horním rohu. Vyberte nástroje pro vývojáře následované přehledy aplikací](./media/create-new-resource/new-app-insights.png)

   | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete. |
   | **Skupina prostředků**     | myResourceGroup      | Název nové nebo existující skupiny prostředků pro hostování dat Přehledů aplikací. |
   | **Umístění** | USA – východ | Zvolte místo ve vašem okolí nebo v blízkosti místa, kde je vaše aplikace hostovaná. |

> [!NOTE]
> I když můžete použít stejný název prostředku v různých skupinách prostředků, může být výhodné použít globálně jedinečný název. To může být užitečné, pokud plánujete [provádět dotazy mezi prostředky,](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) protože zjednodušuje požadovanou syntaxi.

Do požadovaných polí zadejte příslušné hodnoty a pak vyberte **Zkontrolovat + vytvořit**.

![Zadejte hodnoty do povinných polí a pak vyberte "revize + vytvořit".](./media/create-new-resource/review-create.png)

Po vytvoření aplikace se otevře nové podokno. V tomto podokně se zobrazují údaje o výkonu a využití monitorované aplikace. 

## <a name="copy-the-instrumentation-key"></a>Kopírování klíče instrumentace

Klíč instrumentace identifikuje prostředek, ke kterému chcete přidružit telemetrická data. Budete muset zkopírovat instrumentace klíč a přidat jej do kódu aplikace.

![Klikněte a zkopírujte klíč instrumentace](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalace sady SDK do aplikace

Nainstalujte do aplikace sadou SDK application insights. Tento krok do značné míry závisí na typu aplikace.

Pomocí klíče instrumentace nakonfigurujte [sadu SDK, kterou nainstalujete do aplikace][start].

Sada SDK obsahuje standardní moduly, které odesílají telemetrii, aniž byste museli psát další kód. Chcete-li sledovat akce uživatele nebo diagnostikovat problémy podrobněji, [použijte rozhraní API][api] k odeslání vlastní telemetrie.

## <a name="creating-a-resource-automatically"></a>Automatické vytvoření zdroje

### <a name="powershell"></a>PowerShell

Vytvoření nového prostředku Application Insights

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

Úplnou dokumentaci k prostředí PowerShell pro tuto rutinu a zjistěte, jak načíst klíč instrumentace, přečtěte si [dokumentaci k prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Azure CLI (preview)

Chcete-li získat přístup k příkazům příkazového příkazu Azure CLI ve verzi Preview Application Insights, musíte nejdřív spustit:

```azurecli
 az extension add -n application-insights
```

Pokud příkaz nespustíte, `az extension add` zobrazí se chybová zpráva, která uvádí:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nyní můžete spustit následující a vytvořit tak, abyste vytvořili prostředek Application Insights:

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

Úplné dokumentaci k příkazu Azure CLI pro tento příkaz a zjistěte, jak načíst klíč instrumentace, přečtěte si [dokumentaci k příkazovému příkazu k řešení Azure](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Další kroky
* [Diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Zkoumání metrik](../../azure-monitor/platform/metrics-charts.md)
* [Psaní analytických dotazů](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
