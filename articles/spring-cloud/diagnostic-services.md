---
title: Analýza protokolů a metrik v Azure jaře cloudu | Microsoft Docs
description: Naučte se analyzovat diagnostická data v Azure jaře cloudu.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607863"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analýza protokolů a metrik pomocí nastavení diagnostiky

Pomocí diagnostických funkcí Azure jarního cloudu můžete analyzovat protokoly a metriky pomocí kterékoli z následujících služeb:

* Použijte Azure Log Analytics, kde se data zapisují hned, aniž byste nejdřív museli zapisovat do úložiště.
* Uložte je do účtu úložiště pro auditování nebo ruční kontrolu. Můžete určit dobu uchování (ve dnech).
* Streamujte je do centra událostí pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu.

Chcete-li začít, povolte jednu z těchto služeb pro příjem dat. Pokud se chcete dozvědět o konfiguraci Log Analytics, přečtěte si téma Začínáme [s Log Analytics v Azure monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurace nastavení diagnostiky

1. V Azure Portal přejdete do své instance Azure jaře cloudu.
1. Vyberte možnost **nastavení diagnostiky** a pak vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název nastavení a pak zvolte, kam chcete protokoly odeslat. Můžete vybrat libovolnou kombinaci následujících tří možností:
    * **Archivace do účtu úložiště**
    * **Streamování do centra událostí**
    * **Odeslat do Log Analytics**

1. Zvolte, kterou kategorii protokolů a kategorii metrik chcete monitorovat, a pak zadejte dobu uchování (ve dnech). Doba uchování se vztahuje jenom na účet úložiště.
1. Vyberte **Uložit**.

> [!NOTE]
> Může existovat mezera mezi až 15 minutami, kdy se generují protokoly nebo metriky, a když se objeví ve vašem účtu úložiště, v centru událostí nebo Log Analytics.

## <a name="view-the-logs"></a>Zobrazit protokoly

### <a name="use-log-analytics"></a>Použití Log Analytics

1. V Azure Portal v levém podokně vyberte možnost **Log Analytics**.
1. Vyberte pracovní prostor Log Analytics, který jste zvolili při přidání nastavení diagnostiky.
1. Chcete-li otevřít podokno **prohledávání protokolu** , vyberte možnost **protokoly**.
1. Do vyhledávacího pole **protokolu** zadejte jednoduchý dotaz, například:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Chcete-li zobrazit výsledek hledání, vyberte možnost **Spustit**.
1. Nastavením podmínky filtru můžete vyhledat protokol určité aplikace nebo instance.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Další informace o dotazovacím jazyku, který se používá v Log Analytics, najdete v tématu [Azure monitor protokolování dotazů](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Použití účtu úložiště 

1. V Azure Portal v levém podokně vyberte **účty úložiště**.

1. Vyberte účet úložiště, který jste zvolili při přidávání nastavení diagnostiky.
1. Pokud chcete otevřít podokno **kontejneru objektů BLOB** , vyberte **objekty blob**.
1. Pokud chcete zkontrolovat protokoly aplikací, vyhledejte kontejner s názvem **Insights-logs-applicationconsole**.
1. Pokud chcete zkontrolovat metriky aplikace, vyhledejte kontejner s názvem **Insights – metriky – pt1m**.

Další informace o odesílání diagnostických informací do účtu úložiště najdete v tématu [ukládání a zobrazení diagnostických dat v Azure Storage](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Použití centra událostí

1. V Azure Portal v levém podokně vyberte možnost **Event Hubs**.

1. Vyhledejte a vyberte centrum událostí, které jste si zvolili při přidávání nastavení diagnostiky.
1. Chcete-li otevřít podokno **seznam centra událostí** , vyberte možnost **Event Hubs**.
1. Pokud chcete zkontrolovat protokoly aplikací, vyhledejte centrum událostí s názvem **Insights-logs-applicationconsole**.
1. Pokud chcete zkontrolovat metriky aplikací, vyhledejte centrum událostí s názvem **Insights – metriky – pt1m**.

Další informace o odesílání diagnostických informací do centra událostí najdete v tématu [streamování Azure Diagnostics data v Hot Path pomocí Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analyzovat protokoly

Azure Log Analytics poskytuje Kusto, abyste mohli dotazovat protokoly pro účely analýzy. Rychlý úvod k dotazování protokolů pomocí Kusto najdete v [kurzu Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Protokoly aplikací poskytují důležité informace o stavu, výkonu a dalších aplikacích vaší aplikace. V dalších částech najdete několik jednoduchých dotazů, které vám pomůžou pochopit aktuální a poslední stavy vaší aplikace.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Zobrazit protokoly aplikací ze jarního cloudu Azure

Pokud chcete zkontrolovat seznam protokolů aplikací ze jarního cloudu Azure, seřazené podle času s nejnovějšími protokoly zobrazenými jako první, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Zobrazit položky protokolů obsahující chyby nebo výjimky

Chcete-li zkontrolovat neseřazené položky protokolu, které uváděly chybu nebo výjimku, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Pomocí tohoto dotazu můžete najít chyby nebo upravit tyto výrazy a vyhledat konkrétní kódy chyb nebo výjimky. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Zobrazit počet chyb a výjimek hlášených vaší aplikací za poslední hodinu

Chcete-li vytvořit výsečový graf, který zobrazuje počet chyb a výjimek protokolovaných aplikací během poslední hodiny, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Další informace o dotazování protokolů aplikací

Azure Monitor poskytuje rozsáhlou podporu pro dotazování protokolů aplikací pomocí Log Analytics. Další informace o této službě najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](../azure-monitor/log-query/get-started-queries.md). Další informace o vytváření dotazů k analýze protokolů aplikací najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md).
