---
title: Monitorování Azure Functions pomocí protokolů Azure Monitor
description: Naučte se používat protokoly Azure Monitor s Azure Functions k monitorování provádění funkcí.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 51c611b2565ae0a5a054a45f0aedcb039351b46b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208362"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorování Azure Functions pomocí protokolů Azure Monitor

Azure Functions nabízí integraci s [protokoly Azure monitor](../azure-monitor/platform/data-platform-logs.md) pro monitorování funkcí. V tomto článku se dozvíte, jak nakonfigurovat Azure Functions pro odesílání protokolů generovaných systémem a uživatelem do Azure Monitor protokolů.

Protokoly Azure Monitor poskytují možnost konsolidovat protokoly z různých prostředků ve stejném pracovním prostoru, kde je lze analyzovat pomocí [dotazů](../azure-monitor/log-query/log-query-overview.md) pro rychlé načítání, konsolidaci a analýzu shromážděných dat.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](../azure-monitor/log-query/log-query-overview.md) v Azure Portal a potom buď přímo analyzovat data pomocí těchto nástrojů, nebo ukládat dotazy pro použití s [vizualizacemi](../azure-monitor/visualizations.md) nebo [pravidly výstrah](../azure-monitor/platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) , kterou používá služba Azure Průzkumník dat, která je vhodná pro jednoduché dotazy protokolů, ale zahrnuje i pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk můžete rychle zjistit pomocí [více lekcí](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integrace s protokoly Azure Monitor je v současnosti ve verzi Public Preview pro aplikace Function App běžící ve Windows, Premium a vyhrazených plánech hostování.

## <a name="setting-up"></a>Nastavení

1. V části **monitorování** aplikace Function app v [Azure Portal](https://portal.azure.com)vyberte **nastavení diagnostiky**a pak vyberte **Přidat nastavení diagnostiky**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Vybrat nastavení diagnostiky":::

1. Na stránce **nastavení diagnostiky** v části **Podrobnosti** a **protokol**kategorie vyberte možnost **FunctionAppLogs**.

   Tabulka **FunctionAppLogs** obsahuje požadované protokoly.

1. V části **Podrobnosti o cíli**zvolte **Odeslat do Log Analytics**a pak vyberte **pracovní prostor Log Analytics**. 

1. Zadejte **název nastavení diagnostiky**a pak vyberte **Uložit**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Vybrat nastavení diagnostiky":::

## <a name="user-generated-logs"></a>Uživatelem vygenerované protokoly

K vygenerování vlastních protokolů použijte příkaz protokolování, který je specifický pro váš jazyk. Tady jsou ukázkové fragmenty kódu:


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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Dotazování protokolů

Dotaz na vygenerované protokoly:
 
1. Z aplikace Function App vyberte **nastavení diagnostiky**. 

1. V seznamu **nastavení diagnostiky** vyberte pracovní prostor Log Analytics, který jste nakonfigurovali pro odeslání protokolů funkce. 

1. Na stránce **Log Analytics pracovní prostor** vyberte **protokoly**.

   Azure Functions zapisuje všechny protokoly do tabulky **FunctionAppLogs** pod **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Vybrat nastavení diagnostiky":::

Tady je několik ukázkových dotazů:

### <a name="all-logs"></a>Všechny protokoly

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Konkrétní protokoly funkcí

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

- Přečtěte si [přehled Azure Functions](functions-overview.md).
- Přečtěte si další informace o [Azure Monitorch protokolech](../azure-monitor/platform/data-platform-logs.md).
- Přečtěte si další informace o [dotazovacím jazyce](../azure-monitor/log-query/get-started-queries.md).
