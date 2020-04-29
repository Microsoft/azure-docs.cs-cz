---
title: Monitorování Azure Functions pomocí protokolů Azure Monitor
description: Naučte se používat protokoly Azure Monitor s Azure Functions k monitorování provádění funkcí.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77649870"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorování Azure Functions pomocí protokolů Azure Monitor

Azure Functions nabízí integraci s [protokoly Azure monitor](../azure-monitor/platform/data-platform-logs.md) pro monitorování funkcí. V tomto článku se dozvíte, jak nakonfigurovat Azure Functions pro odesílání protokolů generovaných systémem a uživatelem do Azure Monitor protokolů.

Protokoly Azure Monitor poskytují možnost konsolidovat protokoly z různých prostředků ve stejném pracovním prostoru, kde je lze analyzovat pomocí [dotazů](../azure-monitor/log-query/log-query-overview.md) pro rychlé načítání, konsolidaci a analýzu shromážděných dat.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](../azure-monitor/log-query/portals.md) v Azure Portal a potom buď přímo analyzovat data pomocí těchto nástrojů, nebo ukládat dotazy pro použití s [vizualizacemi](../azure-monitor/visualizations.md) nebo [pravidly výstrah](../azure-monitor/platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) , kterou používá služba Azure Průzkumník dat, která je vhodná pro jednoduché dotazy protokolů, ale zahrnuje i pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk můžete rychle zjistit pomocí [více lekcí](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integrace s protokoly Azure Monitor je v současnosti ve verzi Public Preview pro aplikace Function App běžící ve Windows, Premium a vyhrazených plánech hostování.

## <a name="setting-up"></a>Nastavení

V části **monitorování** vyberte **nastavení diagnostiky** a pak klikněte na **Přidat nastavení diagnostiky**.

![Přidat nastavení diagnostiky](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na stránce **nastavení diagnostiky** zvolte **Odeslat do Log Analytics**a potom vyberte pracovní prostor Log Analytics. V části **protokol** zvolit **FunctionAppLogs**, tato tabulka obsahuje požadované protokoly.

![Přidat nastavení diagnostiky](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Uživatelem vygenerované protokoly

Chcete-li generovat vlastní protokoly, můžete použít konkrétní příkaz protokolování v závislosti na jazyku, zde jsou ukázkové fragmenty kódu:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Prostředí](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Dotazování protokolů

Pokud chcete zadat dotaz na vygenerované protokoly, přejděte do pracovního prostoru Log Analytics, který jste nakonfigurovali pro odeslání protokolů funkce, a klikněte na **protokoly**.

![Okno dotazu v pracovním prostoru LA](media/functions-monitor-log-analytics/querying.png)

Azure Functions zapisuje všechny protokoly do tabulky **FunctionAppLogs** . tady je několik ukázkových dotazů.

### <a name="all-logs"></a>Všechny protokoly

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Konkrétní protokoly funkcí

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

- Přečtěte si [přehled Azure Functions](functions-overview.md)
- Další informace o [protokolech Azure monitor](../azure-monitor/platform/data-platform-logs.md)
- Přečtěte si další informace o [dotazovacím jazyce](../azure-monitor/log-query/get-started-queries.md).
