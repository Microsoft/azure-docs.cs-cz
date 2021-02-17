---
title: výraz App () v Azure Monitorch dotazech k protokolu | Microsoft Docs
description: Výraz aplikace se používá v dotazu protokolu Azure Monitor k načtení dat z konkrétní aplikace Application Insights ve stejné skupině prostředků, v jiné skupině prostředků nebo jiném předplatném.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: cbfc9c8a32eb5345cfc69f51f40e0634e05bd978
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609463"
---
# <a name="app-expression-in-azure-monitor-query"></a>výraz App () v Azure Monitor dotazu

`app`Výraz se používá v Azure monitor dotazech k načtení dat z konkrétní Application Insights aplikace ve stejné skupině prostředků, v jiné skupině prostředků nebo jiném předplatném. To je užitečné, pokud chcete zahrnout data aplikace do dotazu protokolu Azure Monitor a dotazovat data napříč více aplikacemi v dotazu Application Insights.

> [!IMPORTANT]
> Výraz App () se nepoužívá, pokud používáte [prostředek Application Insights založený na pracovních prostorech](../app/create-workspace-resource.md) , protože data protokolu se ukládají v pracovním prostoru Log Analytics. Použijte výraz log () k zápisu dotazu, který obsahuje aplikaci ve více pracovních prostorech. Pro více aplikací ve stejném pracovním prostoru nepotřebujete dotaz mezi jednotlivými pracovními prostory.

## <a name="syntax"></a>Syntax

`app(`*RID*`)`


## <a name="arguments"></a>Argumenty

- *Identifikátor*: aplikace identifikuje jednu z formátů v následující tabulce.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Název prostředku | Lidský čitelný název aplikace (označuje se také jako "název součásti") | aplikace ("fabrikamapp") |
| Kvalifikovaný název | Úplný název aplikace ve formátu: "Subscription/resourceName/resourceName/název součásti" | aplikace (' AI-prototyp/Fabrikam/fabrikamapp ') |
| ID | Identifikátor GUID aplikace | aplikace ("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID prostředku Azure | Identifikátor prostředku Azure |aplikace ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Poznámky

* Musíte mít oprávnění ke čtení aplikace.
* Identifikace aplikace podle jejího názvu předpokládá, že je v rámci všech přístupných předplatných jedinečná. Pokud máte více aplikací se zadaným názvem, dotaz selže z důvodu nejednoznačnosti. V takovém případě je nutné použít jeden z ostatních identifikátorů.
* K dotazování napříč Log Analyticsmi pracovními prostory použijte související [pracovní prostor](../logs/workspace-expression.md) výrazu.
* Výraz App () není v současné době ve vyhledávacím dotazu podporován, pokud se používá Azure Portal k vytvoření [vlastního pravidla upozornění prohledávání protokolu](../alerts/alerts-log.md), pokud se jako prostředek pro pravidlo výstrahy nepoužívá aplikace Application Insights.

## <a name="examples"></a>Příklady

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete odkazovat na pracovní prostor Log Analytics, podívejte se do [výrazu pracovní prostor](../logs/workspace-expression.md) .
- Přečtěte si informace o tom, jak jsou uložená [data Azure monitor](../log-query/log-query-overview.md) .
- Přístup k celé dokumentaci pro [dotazovací jazyk Kusto](/azure/kusto/query/).

