---
title: Monitorování funkcí Azure pomocí protokolů monitorování Azure
description: Zjistěte, jak pomocí protokolů monitorování Azure pomocí funkcí Azure monitorovat spuštění funkcí.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649870"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorování funkcí Azure pomocí protokolů monitorování Azure

Funkce Azure nabízí integraci s [protokoly monitorování Azure](../azure-monitor/platform/data-platform-logs.md) pro monitorování funkcí. Tento článek ukazuje, jak nakonfigurovat funkce Azure pro odesílání protokolů generovaných systémem a protokoly generované uživateli do protokolů monitorování Azure.

Protokoly monitorování Azure vám umožňuje konsolidovat protokoly z různých prostředků ve stejném pracovním prostoru, kde je možné analyzovat pomocí [dotazů](../azure-monitor/log-query/log-query-overview.md) rychle načíst, konsolidovat a analyzovat shromážděná data.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](../azure-monitor/log-query/portals.md) na webu Azure Portal a pak buď přímo analyzovat data pomocí těchto nástrojů nebo uložit dotazy pro použití s [vizualizacemi](../azure-monitor/visualizations.md) nebo [pravidly výstrah](../azure-monitor/platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) používanou aplikací Azure Data Explorer, která je vhodná pro jednoduché dotazy protokolu, ale také zahrnuje pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk se můžete rychle naučit pomocí [několika lekcí](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integrace s protokoly monitorování Azure je aktuálně ve verzi Public Preview pro funkční aplikace spuštěné v plánech Windows Consumption, Premium a Dedicated hosting.

## <a name="setting-up"></a>Nastavení

V části **Monitorování** vyberte **Nastavení diagnostiky** a potom klepněte na **tlačítko Přidat diagnostické nastavení**.

![Přidání diagnostického nastavení](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na stránce **Nastavení diagnostiky** zvolte **Odeslat do analýzy protokolů**a pak vyberte pracovní prostor Analýzy protokolů. V **části log** zvolte **FunctionAppLogs**, tato tabulka obsahuje požadované protokoly.

![Přidání diagnostického nastavení](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Protokoly generované uživatelem

Chcete-li generovat vlastní protokoly, můžete použít konkrétní příkaz protokolování v závislosti na vašem jazyce, zde jsou ukázkové fragmenty kódu:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Dotazování na protokoly

Chcete-li zadat dotaz na generované protokoly, přejděte do pracovního prostoru Analýzy protokolů, který jste nakonfigurovali tak, aby odesílal protokoly funkcí, a klepněte na **položku Protokoly**.

![Okno dotazu v pracovním prostoru LA](media/functions-monitor-log-analytics/querying.png)

Azure Functions zapisuje všechny protokoly do tabulky **FunctionAppLogs,** tady jsou některé ukázkové dotazy.

### <a name="all-logs"></a>Všechny protokoly

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Protokoly specifických funkcí

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Výjimky

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Další kroky

- Kontrola [přehledu funkcí Azure](functions-overview.md)
- Další informace o [protokolech monitorování Azure](../azure-monitor/platform/data-platform-logs.md)
- Přečtěte si další informace o [dotazovacím jazyce](../azure-monitor/log-query/get-started-queries.md).
