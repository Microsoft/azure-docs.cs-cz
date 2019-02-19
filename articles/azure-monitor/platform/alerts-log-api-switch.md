---
title: Přepnout ze starší verze Log Analytics výstrahy rozhraní API do nového rozhraní API Azure výstrahy
description: Přehled o vyřazení starší verze elementu savedSearch podle rozhraní API upozornění Log Analytics a proces přepnout pravidla upozornění na nové rozhraní API ScheduledQueryRules s podrobnostmi o adresování běžné problémy zákazníků.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6af1c5347a522f7e42feecb6722dfbb64439d086
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341007"
---
# <a name="switch-api-preference-for-log-alerts"></a>Přepnout předvoleb rozhraní API pro výstrahy protokolu

> [!NOTE]
> Obsah uvedeno **není** použitelná pro uživatele Azure GovCloud a jenom na uživatele, veřejného cloudu Azure.  

Dokud se nedávno jste spravovali pravidla upozornění na portálu Microsoft Operations Management Suite. Nové prostředí upozornění byla integrována s různými službami v Microsoft Azure včetně služby Log Analytics a budeme muset [rozšíření pravidel upozornění z portálu OMS do Azure](alerts-extend.md). Ale zajistit minimální narušení pro zákazníky, kteří proces nezmění programové rozhraní pro jeho využití – [API upozornění Log Analytics](api-alerts.md) podle elementu SavedSearch.

Ale nyní oznamujeme ke službě Log Analytics upozorňování uživatelů hodnotu true Azure prostřednictvím kódu programu nebo naopak [Azure Monitor – rozhraní API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), což je také reflektivní v vaše [fakturace Azure – pro výstrahy protokolu](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Další informace o tom, jak spravovat upozornění protokolu pomocí rozhraní API najdete v tématu [Správa upozornění protokolů pomocí šablony Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) a [upozornění protokolů správa pomocí Powershellu, CLI nebo API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Výhody přechodu k nové rozhraní API Azure

Má několik výhod, vytváření a Správa výstrah pomocí [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) přes [starší verze API upozornění Log Analytics](api-alerts.md); uvádíme některé z hlavních těm, které jsou následující:

- Schopnost [pro různé pracovní prostor log search](../log-query/cross-workspace-query.md) pravidla upozornění a rozsahu externích prostředků jako pracovní prostory Log Analytics nebo dokonce i aplikace Application Insights
- Když více polí používaných v dotazu do skupiny, pomocí [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) může uživatel zadat, které pole na agregace – na webu Azure Portal
- Upozornění vytvořené pomocí protokolů [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) může mít definované období až 48 hodin a načítat data delší dobu než dřív
- Vytvořit pravidla výstrah v jednom kroku jako jeden prostředek, aniž by bylo nutné vytvořit tři úrovně prostředky jako [starší verze API upozornění Log Analytics](api-alerts.md)
- Jeden programové rozhraní pro všechny varianty upozornění protokolů založených na dotazech v Azure – nové [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) slouží ke správě pravidel pro Log Analytics, jakož i Application Insights
- Všechny nové přihlášení výstrah funkce a budoucí vývoj bude k dispozici jenom na novém [scheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces přechod ze starší verze rozhraní API výstrahy protokolu

Proces přechodu pravidla upozornění z [starší verze API upozornění Log Analytics](api-alerts.md) nezahrnuje Změna definice upozornění, dotaz nebo konfiguraci jakýmkoli způsobem. Uživatelů je zdarma a použít některou [starší verze API upozornění Log Analytics](api-alerts.md) nebo do nového [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Pravidla buď rozhraní API, se vytvořil upozornění *možné spravovat pomocí stejného rozhraní API pouze* – stejně jako z portálu Azure portal. Ve výchozím nastavení, budou dál používat Azure Monitor [starší verze API upozornění Log Analytics](api-alerts.md) k vytvoření jakékoli nové pravidlo upozornění na webu Azure portal.

Níže jsou zkompilovány dopadů přepínač přednost scheduledQueryRules rozhraní API:

- Všechny interakce provést pro správu upozornění protokolů prostřednictvím programových rozhraní musíte teď provést pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) místo. Další informace najdete v tématu, [ukázkový používání pomocí šablony Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) a [ukázkový používání prostřednictvím Azure CLI a Powershellu](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Všechny nové pravidlo upozornění protokolu vytvořili na webu Azure portal, se vytvoří pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pouze a uživatelům povolit používání [další funkce nové rozhraní API](#Benefits-of-switching-to-new-Azure-API) prostřednictvím webu Azure portal i

Každý zákazník, kdo chce dobrovolně přepnout na nový [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a blokovat využití z [starší verze API upozornění Log Analytics](api-alerts.md); to tak, že provádí volání PUT na pod rozhraní API pro přepnutí všechny výstrahy pravidla spojená s konkrétním pracovním prostorem Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

S obsahující text požadavku následující JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Rozhraní API lze rovněž přistupovat pomocí příkazového řádku Powershellu [ARMClient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API Azure Resource Manageru. Jak je znázorněno níže, v ukázkové volání PUT nástrojem ARMclient Přepnout všechna pravidla výstrahy přidružené k konkrétní pracovní prostor Log Analytics.

```PowerShell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Pokud se přepnout na všechna pravidla výstrahy v pracovním prostoru Log Analytics, chcete-li použít nové [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) je úspěšné, následující odpověď, poskytneme vám.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Uživatelé mohou také zkontrolovat aktuální stav pracovního prostoru Log Analytics a zjistit, zda byl nebo nebyl byla přepnuta na použití [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pouze. Pokud chcete zkontrolovat, uživatelé mohou provádět volání GET na pod rozhraní API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ke spuštění výše uvedených v pomocí příkazového řádku pomocí prostředí PowerShell [ARMClient](https://github.com/projectkudu/ARMClient) nástroj, najdete v následující ukázce.

```PowerShell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud zadaný pracovní prostor Log Analytics byl přepnut na použití [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pouze; odpověď JSON bude jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Jiný, pokud nebyl dosud bylo změněno zadaný pracovní prostor analýzy protokolů použít [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pouze; odpověď JSON bude jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Další postup

- Další informace o [Azure monitorování – výstrahy protokolu](alerts-unified-log.md).
- Zjistěte, jak vytvořit [upozornění protokolů ve službě Azure Alerts](alerts-log.md).
- Další informace o [prostředí Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
