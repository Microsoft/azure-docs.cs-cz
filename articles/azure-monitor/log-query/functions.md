---
title: Funkce v dotazech protokolu Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí služby functions volání dotazu z jiného dotazu protokolu ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 6c6bd31961022957ec1a09fef6058ad32476e1c7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005093"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Pomocí funkcí v dotazech protokolu Azure Monitor

> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Použít protokol dotaz s jiným dotazem ho můžete uložit jako funkce. To umožňuje zjednodušení složitých dotazů pomocí jejich rozdělení na oddíly a umožňuje opakovaně používat společný kód s více dotazy.

## <a name="create-a-function"></a>Vytvoření funkce

Vytvoření funkce v log analytics na portálu Azure portal kliknutím **Uložit** a potom zadat informace v následující tabulce.

| Nastavení | Popis |
|:---|:---|
| Název           | Zobrazovaný název dotazu v **Průzkumníka dotazů**. |
| Uložit jako        | Funkce |
| Alias funkce | Krátký název použít funkci v dalších dotazech. Nesmí obsahovat mezery a musí být jedinečné. |
| Kategorie       | Kategorie pro uspořádání uložených dotazů a funkce v **Průzkumníka dotazů**. |

> [!NOTE]
> Funkce ve službě Azure Monitor nemůže obsahovat jiné funkci.

> [!NOTE]
> Ukládají se funkce je možné v Azure Monitor dotazů na protokoly, ale aktuálně není pro dotazy Application Insights.



## <a name="use-a-function"></a>Použijte funkci
Pomocí funkce včetně jeho alias do jiného dotazu. Může sloužit jako ostatní tabulky.

## <a name="example"></a>Příklad:
Následující ukázkový dotaz vrátí všechny chybějící aktualizace zabezpečení v poslední den. Uložit tento dotaz jako funkce s aliasem _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Vytvoření jiného dotazu a odkaz _security_updates_last_day_ funkce k vyhledání souvisejících s SQL požadovaných aktualizací zabezpečení.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro psaní dotazů na protokoly Azure monitoru:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
