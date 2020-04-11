---
title: Přepnutí na nové rozhraní API upozornění Azure
description: Přehled starších uložených rozhraní API upozornění na analýzu protokolů založeného na vyhledávání a procesu přepnutí pravidel výstrah do nového rozhraní API ScheduledQueryRules s podrobnostmi o řešení běžných problémů zákazníků.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7950b3f584c36b68a4eff66b05e83ba94c1ec1dd
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114356"
---
# <a name="switch-api-preference-for-log-alerts"></a>Přepnout rozhraní API před upozornění

> [!NOTE]
> Obsah uvedený platí pouze pro uživatele Azure public cloud a **ne** pro Azure Government nebo Azure China cloud.  

> [!NOTE]
> Jakmile se uživatel rozhodne přepnout předvolbu na nové [rozhraní API scheduledQueryRules,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) není možné se vrátit k používání staršího [staršího rozhraní API upozornění log analytics](api-alerts.md).

Až donedávna jste spravovali pravidla výstrah na portálu Microsoft Operations Management Suite. Nové prostředí výstrah bylo integrováno s různými službami v Microsoft Azure, včetně Log Analytics, a požádali jsme o [rozšíření pravidel výstrah z portálu OMS do Azure](alerts-extend.md). Ale aby bylo zajištěno minimální narušení pro zákazníky, proces nezměnil programové rozhraní pro jeho spotřebu - [Log Analytics Alert API](api-alerts.md) založené na Uložené Vyhledávání.

Ale teď oznamujete pro Analýzu protokolů upozorňující uživatele na skutečnou programovou alternativu [Azure, Azure Monitor – Rozhraní API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), které je také odrazující ve [vaší fakturaci Azure – pro výstrahy protokolu](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Další informace o tom, jak spravovat výstrahy protokolu pomocí rozhraní API, najdete v [tématu Správa výstrah protokolu pomocí šablony prostředků Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) a správa výstrah protokolu pomocí [PowerShellu](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Výhody přechodu na nové rozhraní Azure API

Existuje několik výhod vytváření a správu výstrah pomocí [naplánovaného rozhraní API QueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) přes [starší rozhraní API výstrah analýzy protokolů](api-alerts.md); jsme uvedli některé z hlavních z nich níže:

- Možnost [procházet vyhledávání protokolů pracovních prostorů](../log-query/cross-workspace-query.md) v pravidlech výstrah a rozsávat externí prostředky, jako jsou pracovní prostory Log Analytics nebo dokonce aplikace Application Insights
- Když více polí používá k seskupení v dotazu, pomocí [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) uživatel může určit, které pole agregovat na webu Azure Portal
- Výstrahy protokolu vytvořené pomocí [rozhraní ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) mohou mít období definované až 48 hodin a načítat data po delší dobu než dříve
- Vytvořte pravidla výstrah v jednom záběru jako jeden prostředek bez nutnosti vytvářet tři úrovně prostředků, jako je [tomu u staršího rozhraní API upozornění analýzy protokolů](api-alerts.md)
- Jediné programové rozhraní pro všechny varianty výstrah protokolu založených na dotazech v Azure – nové [rozhraní API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) api lze použít ke správě pravidel pro analýzu protokolů a také pro přehledy aplikací
- Správa upozornění na protokoly pomocí [rutin Powershellu](alerts-log.md#managing-log-alerts-using-powershell)
- Všechny nové funkce výstrahprotokolu a budoucí vývoj budou k dispozici pouze prostřednictvím nového [rozhraní API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces přechodu ze staršího rozhraní API upozornění protokolů

Uživatelé mohou používat buď [starší rozhraní API upozornění analýzy protokolů,](api-alerts.md) nebo nové rozhraní API [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Pravidla výstrah vytvořená buď rozhraním API *budou spravovatelná jenom stejným rozhraním API* – a také z portálu Azure. Ve výchozím nastavení azure monitor bude i nadále používat [starší rozhraní API výstrahy Analýzy protokolů](api-alerts.md) pro vytváření nového pravidla výstrahy z portálu Azure pro existující pracovní prostory Analýzy protokolů. Jak [bylo oznámeno nové pracovní prostor protokolu vytvořené na nebo po 1 června 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) - bude automaticky používat nové [naplánované QueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ve výchozím nastavení, včetně na webu Azure Portal.

Dopady přechodu preference na scheduledQueryRules API jsou zkompilovány níže:

- Všechny interakce provedené pro správu výstrah protokolu prostřednictvím programových rozhraní musí být nyní provedeno pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) místo. Další informace najdete v tématu [ukázkové použití prostřednictvím šablony prostředků Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) a [ukázkové použití přes PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Jakékoli nové pravidlo výstrahprotokolu vytvořené na webu Azure Portal se vytvoří jenom pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a umožní uživatelům používat [další funkce nového rozhraní API](#benefits-of-switching-to-new-azure-api) také prostřednictvím portálu Azure Portal.
- Závažnost pravidel výstrah protokolu se přesune z: *Kritické, Upozornění & Informační*, na *hodnoty závažnosti 0, 1 & 2*. Spolu s možností vytvořit / aktualizovat pravidla výstrah se závažností 3 a 4 také.

Proces přesunutí pravidel výstrah ze [staršího rozhraní API upozornění služby Log Analytics](api-alerts.md) nezahrnuje změnu definice výstrah, dotazu nebo konfigurace žádným způsobem. Vaše pravidla výstrah a monitorování nejsou ovlivněny a výstrahy se nezastaví ani nebudou zastaveny, během nebo po přepnutí. Jediné změny jsou:

- Změna předvoleb rozhraní API a přístupu k vašim pravidlům prostřednictvím nového rozhraní API.
- Identifikátor URI prostředku upraveného pravidla výstrahy obsahující ID použitá ve [starším rozhraní API výstrahy analýzy protokolů](api-alerts.md) namísto názvu pravidla výstrahy v této struktuře `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Zobrazovaný název pravidla výstrahy zůstane nezměněn.

Každý zákazník, který chce dobrovolně přejít na nový [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a blokovat využití ze [starší log Analytics Alert API](api-alerts.md); můžete tak učinit provedením volání PUT na níže uvedené rozhraní API přepnout všechna pravidla výstrah přidružené k konkrétní pracovní prostor Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

S tělo požadavku obsahující níže JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

K rozhraní API lze přistupovat také z příkazového řádku Prostředí PowerShell pomocí [armclientu](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku s otevřeným zdrojovým kódem, který zjednodušuje vyvolání rozhraní API Správce prostředků Azure. Jak je znázorněno níže, v ukázce PUT volání pomocí nástroje ARMclient přepnout všechna pravidla výstrah přidružené k konkrétní pracovní prostor Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Pokud je úspěšné přepnutí všech pravidel výstrah v pracovním prostoru Log Analytics k použití nových [pravidel scheduledQueryRules,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) bude poskytnuta následující odpověď.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Uživatelé mohou také zkontrolovat aktuální stav pracovního prostoru Log Analytics a zjistit, zda má nebo nebyl přepnut na použití pouze [scheduledQueryRules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Chcete-li zkontrolovat, mohou uživatelé provést volání GET na níže uvedené rozhraní API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Chcete-li provést výše uvedené v použití příkazového řádku prostředí PowerShell pomocí nástroje [ARMClient,](https://github.com/projectkudu/ARMClient) podívejte se na ukázku níže.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud byl zadaný pracovní prostor Analýzy protokolů přepnut na použití pouze [scheduledQueryRules;](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pak odpověď JSON bude, jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Jinak pokud zadaný pracovní prostor Log Analytic ještě nebyl přepnut na použití pouze [scheduledQueryRules;](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pak odpověď JSON bude, jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Monitor – výstrahy protokolu](alerts-unified-log.md).
- Zjistěte, jak vytvořit [výstrahy protokolu v Azure Alerts](alerts-log.md).
- Další informace o [prostředí Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
