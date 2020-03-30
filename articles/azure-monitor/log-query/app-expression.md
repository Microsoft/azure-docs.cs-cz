---
title: výraz aplikace() v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Výraz aplikace se používá v dotazu protokolu Azure Monitor k načtení dat z konkrétní aplikace Application Insights ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: 5502df1cd119c0f63c65945d73431a17282ebc0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670250"
---
# <a name="app-expression-in-azure-monitor-query"></a>výraz aplikace() v dotazu Azure Monitoru

Výraz `app` se používá v dotazu Azure Monitor k načtení dat z konkrétní aplikace Application Insights ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném. To je užitečné zahrnout data aplikací do dotazu protokolu Azure Monitor a dotazovat data napříč více aplikacemi v dotazu Application Insights.



## <a name="syntax"></a>Syntaxe

`app(`*Identifikátor*`)`


## <a name="arguments"></a>Argumenty

- *Identifikátor*: Identifikuje aplikaci pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Název prostředku | Lidsky čitelný název aplikace (AKA "název komponenty") | app("fabrikamapp") |
| Kvalifikovaný název | Úplný název aplikace ve formuláři: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | IDENTIFIKÁTOR GUID aplikace | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID prostředků Azure | Identifikátor prostředku Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení do aplikace.
* Identifikace aplikace podle jejího názvu předpokládá, že je jedinečný ve všech přístupných předplatných. Pokud máte více aplikací se zadaným názvem, dotaz se nezdaří z důvodu nejednoznačnosti. V takovém případě musíte použít jeden z dalších identifikátorů.
* Pomocí souvisejícího [pracovního prostoru výrazů](workspace-expression.md) můžete dotazovat v pracovních prostorech Analýzy protokolů.
* Výraz app() není aktuálně podporován ve vyhledávacím dotazu při použití portálu Azure k vytvoření [vlastního pravidla výstrahy hledání protokolu](../platform/alerts-log.md), pokud se jako prostředek pro pravidlo výstrahy nepoužívá aplikace Application Insights.

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

- Viz [výraz pracovního prostoru,](workspace-expression.md) který odkazuje na pracovní prostor Analýzy protokolů.
- Přečtěte si o tom, jak se data [Azure Monitoru](../../azure-monitor/log-query/log-query-overview.md) ukládají.
- Získejte přístup k úplné dokumentaci [k dotazovacímu jazyku Kusto](/azure/kusto/query/).
