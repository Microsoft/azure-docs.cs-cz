---
title: Přepnutí ze starší verze rozhraní API upozornění Log Analytics do nového rozhraní API Azure Alerts
description: Přehled starších Log Analytics elementu savedsearch a rozhraní API výstrah a procesu pro přepínání pravidel upozornění na nové rozhraní ScheduledQueryRules API s podrobnostmi adresování běžných otázek zákazníků.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 7b3a09c9227110d6dba205987903a2c97dccf1b8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677791"
---
# <a name="switch-api-preference-for-log-alerts"></a>Přepnout předvolby rozhraní API pro výstrahy protokolu

> [!NOTE]
> Obsah uvedený pro uživatele, kteří používají jenom veřejný cloud Azure, a **ne** pro Azure Government nebo Azure Čína Cloud.  

Až do poslední doby jste spravovali pravidla výstrahy na portálu Microsoft Operations Management Suite. Nové prostředí výstrah bylo integrováno s různými službami v Microsoft Azure včetně Log Analytics a bylo požádáno o [prodloužení vašich pravidel upozornění z portálu OMS do Azure](alerts-extend.md). Pro zajištění minimálního přerušení pro zákazníky ale proces nezměnil programové rozhraní pro své [rozhraní API pro Log Analytics výstrah](api-alerts.md) založené na elementu savedsearch.

Nyní ale oznamujete Log Analytics upozorňování uživatelů na skutečnou programovou alternativu Azure, [Azure monitor-ScheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), které se také odráží v [fakturaci Azure – výstrahy protokolu](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Další informace o tom, jak spravovat výstrahy protokolu pomocí rozhraní API, najdete v tématu [Správa upozornění protokolu pomocí šablony prostředků Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) a [Správa výstrah protokolu pomocí PowerShellu](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Výhody přepínání na nové rozhraní API Azure

Existuje několik výhod vytváření a správy výstrah pomocí [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) přes [starší verze rozhraní api pro výstrahy Log Analytics](api-alerts.md). Níže uvádíme některé hlavní verze:

- Možnost [prohledávání protokolů napříč pracovními prostory](../log-query/cross-workspace-query.md) v pravidlech výstrah a rozbírat externí prostředky, jako jsou Log Analytics pracovní prostory nebo dokonce Application Insights aplikace
- Pokud se pro seskupení v dotazu používá více polí, může pomocí [scheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) uživatel určit, které pole se má agregovat v Azure Portal
- Výstrahy protokolu vytvořené pomocí [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) můžou mít definované období až 48 hodin a načíst data po delší dobu než dřív.
- Vytvářejte pravidla výstrah v jednom snímku jako jeden prostředek, aniž byste museli vytvářet tři úrovně prostředků, stejně jako u [starších Log Analytics rozhraní API pro výstrahy](api-alerts.md) .
- Jedno programové rozhraní pro všechny varianty výstrah protokolu založených na dotazech v Azure – nové [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) je možné použít ke správě pravidel pro Log Analytics a také Application Insights
- Správa výstrah protokolu pomocí [rutin prostředí PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Všechny nové funkce upozornění protokolu a budoucí vývoj budou dostupné jenom přes nové [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) .

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces přepínání ze starší verze rozhraní API upozornění protokolů

Uživatelé můžou používat [starší rozhraní API pro výstrahy Log Analytics](api-alerts.md) nebo nové [rozhraní API pro scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Pravidla upozornění vytvořená buď rozhraním API, bude *možné spravovat jenom pomocí stejného rozhraní API* a také z Azure Portal. Ve výchozím nastavení bude Azure Monitor nadále používat [starší verze Log Analytics výstrahy API](api-alerts.md) pro vytvoření nového pravidla výstrahy z Azure Portal pro existující pracovní prostory Log Analytics. Jak [oznámil nový pracovní prostor protokolu vytvořený na 1. června 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) – ve výchozím nastavení automaticky použije nové [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , včetně v Azure Portal.

Dopady přepínače preference na rozhraní API scheduledQueryRules jsou kompilovány níže:

- Všechny interakce provedené pro správu upozornění protokolů prostřednictvím programových rozhraní se teď musí provádět pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . Další informace najdete v tématu [Ukázka použití prostřednictvím šablony prostředků Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) a [ukázkové použití prostřednictvím PowerShellu](alerts-log.md#managing-log-alerts-using-powershell) .
- Všechna nová pravidla upozornění protokolu vytvořená v Azure Portal budou vytvořena pouze pomocí [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a umožňují uživatelům používat [Další funkce nového rozhraní API](#benefits-of-switching-to-new-azure-api) prostřednictvím Azure Portal.
- Závažnost pro pravidla upozornění protokolu se posune od: *kritická, upozorňující & informativní*, na *hodnoty závažnosti 0, 1 & 2*. Společně s možností pro vytváření a aktualizaci pravidel upozornění se závažností 3 a 4 taky.

Proces přesunutí pravidel výstrah z [rozhraní API pro upozornění starší verze Log Analytics](api-alerts.md) nezahrnuje žádné změny definice upozornění, dotazování nebo konfigurace jakýmkoli způsobem. Vaše pravidla a monitorování nejsou nijak ovlivněna a výstrahy se v průběhu nebo po přepínači nezastaví nebo nebudou zastaveny. Jediná změna je změna v preference rozhraní API a přístup k vašim pravidlům prostřednictvím nového rozhraní API.

> [!NOTE]
> Jakmile uživatel výslovný přepne preference na nové [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), nemůžete se vrátit ani vrátit k používání starší [verze rozhraní API výstrah Log Analytics](api-alerts.md).

Všichni zákazníci, kteří chtějí dobrovolně přejít na nové [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a zablokovat využití z [rozhraní API pro upozornění starší verze Log Analytics](api-alerts.md); To lze provést tak, že pomocí volání metody PUT v níže uvedeném rozhraní API přepnete všechna pravidla výstrah přidružená ke konkrétnímu pracovnímu prostoru Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

S textem žádosti obsahujícím níže uvedený kód JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Rozhraní API je také možné otevřít z příkazového řádku PowerShellu pomocí [ARMClient](https://github.com/projectkudu/ARMClient), open source nástroje příkazového řádku, který zjednodušuje vyvolání rozhraní Azure Resource Manager API. Jak je znázorněno níže, v ukázce volání metody PUT pomocí nástroje ARMclient přepněte všechna pravidla výstrah přidružená ke konkrétnímu pracovnímu prostoru Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Pokud je v pracovním prostoru Log Analytics přepnutí všech pravidel upozornění na použití nového [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) úspěšné, bude poskytnuta následující odpověď.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Uživatelé můžou také kontrolovat aktuální stav pracovního prostoru Log Analytics a zjistit, jestli je nebo není přepnutý jenom na použití jenom [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . Chcete-li zjistit, mohou uživatelé provést volání metody GET v rozhraní API níže.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Postup provedení výše uvedeného v používání příkazového řádku PowerShellu pomocí nástroje [ARMClient](https://github.com/projectkudu/ARMClient) naleznete v následující ukázce.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud je zadaný Log Analytics pracovní prostor přepnut jenom na použití [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ; pak bude odpověď JSON, jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Jinak, pokud zadaný pracovní prostor analytického protokolu ještě není přepnut na použití jenom [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ; pak bude odpověď JSON, jak je uvedeno níže.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [výstrahách Azure monitor-log](alerts-unified-log.md).
- Naučte se vytvářet [výstrahy protokolu v upozorněních Azure](alerts-log.md).
- Přečtěte si další informace o [prostředí výstrah Azure](../../azure-monitor/platform/alerts-overview.md).
