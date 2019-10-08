---
title: Analýza protokolů a metrik v Azure jaře cloudu | Microsoft Docs
description: Naučte se analyzovat diagnostická data v Azure jaře cloudu
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038883"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analýza protokolů a metrik pomocí nastavení diagnostiky

Diagnostické funkce Azure pro jarní Cloud umožňují analyzovat protokoly a metriky pomocí jedné z následujících služeb:

* Analyzujte je pomocí Azure Log Analytics, kde se data zapisují hned do Azure Log Analytics bez nutnosti nejdřív zapisovat data do úložiště.
* Uložte je do účtu úložiště pro auditování nebo ruční kontrolu. Můžete určit dobu uchování (ve dnech).
* Streamujte je Event Hubs k ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu.

Chcete-li začít, budete muset povolit jednu z těchto služeb pro příjem dat.  Pokud se chcete dozvědět o konfiguraci Log Analytics, přečtěte si [Tento kurz](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

1. V Azure Portal přejít na svou instanci cloudu Azure na jaře.
1. Vyberte možnost nabídky **nastavení diagnostiky** .
1. Vyberte tlačítko **Přidat nastavení diagnostiky** .
1. Zadejte název nastavení a vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci následujících tří možností:
    * Archivace do účtu úložiště
    * Streamování do centra událostí
    * Odeslání do Log Analytics

1. Zvolte, kterou kategorii protokolů a kategorii metrik chcete monitorovat, a zadejte dobu uchování (ve dnech). Doba uchování se vztahuje jenom na účet úložiště.
1. Vyberte **Uložit** a použijte nastavení.

> [!NOTE]
> Může to trvat až 15 minut, než se generují protokoly nebo metriky, a když se objeví v účtu úložiště/centru událostí/Log Analytics.

## <a name="viewing-logs"></a>Zobrazení protokolů

### <a name="using-log-analytics"></a>Použití Log Analytics

1. Z Azure Portal v navigační nabídce vlevo vyberte Log Analytics.
1. Vyberte pracovní prostor Log Analytics, který jste zvolili při přidávání nastavení diagnostiky.
1. Výběrem `Logs` otevřete okno prohledávání protokolu.
1. Do vyhledávacího pole protokolu zadejte jednoduchý dotaz.  Například:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Pokud chcete zobrazit výsledky hledání, vyberte `Run`.
1. Nastavením podmínky filtru můžete vyhledat protokol určité aplikace nebo instance.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Přečtěte si další informace o dotazovacím jazyku používaném v Log Analytics [v tomto článku](../azure-monitor/log-query/query-language.md) .

### <a name="using-logs-and-metrics-in-storage-account"></a>Používání protokolů a metrik v účtu úložiště

1. Z Azure Portal v navigační nabídce vlevo vyberte účty úložiště.
1. Vyberte účet úložiště, který jste zvolili při přidávání nastavení diagnostiky.
1. Vyberte položku `Blobs` a otevřete tak okno kontejneru objektů BLOB.
1. Vyhledejte kontejner s názvem `insights-logs-applicationconsole` ke kontrole protokolů aplikací.
1. Vyhledejte kontejner s názvem `insights-metrics-pt1m` ke kontrole metrik aplikace.

[Přečtěte si další informace o odesílání diagnostických informací do účtu úložiště.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Použití Event Hubs

1. Z Azure Portal v navigační nabídce vlevo vyberte Event Hubs.
1. Vyhledejte a vyberte Event Hubs, které jste zvolili při přidávání nastavení diagnostiky.
1. Výběrem `Event Hubs` otevřete okno seznam centra událostí.
1. Vyhledejte centrum událostí s názvem `insights-logs-applicationconsole` pro kontrolu protokolů aplikací.
1. Vyhledejte centrum událostí s názvem `insights-metrics-pt1m` a zkontrolujte metriky aplikace.

[Přečtěte si další informace o odesílání diagnostických informací do centra událostí.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analýza protokolů

Azure Log Analytics poskytuje Kusto, abyste se mohli dotazovat na analýzy v protokolech.  V [kurzu Log Analytics](../azure-monitor/log-query/get-started-portal.md) najdete stručný úvod k dotazování protokolů pomocí Kusto.

Protokoly aplikací poskytují důležité informace o stavu, výkonu a dalších aplikacích vaší aplikace.  Níže jsou uvedeny některé jednoduché dotazy, které vám pomohou pochopit aktuální a poslední stavy vaší aplikace.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Zobrazit protokoly aplikací ze jarního cloudu Azure

Pokud chcete zkontrolovat seznam protokolů aplikací ze jarního cloudu Azure, seřazené podle času s nejnovějšími protokoly zobrazenými jako první:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Zobrazit položky protokolů obsahující chyby nebo výjimky

Tento dotaz umožňuje zkontrolovat položky protokolu, které uváděly chybu nebo výjimku.  Výsledky nejsou seřazené.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Pomocí tohoto dotazu můžete najít chyby nebo upravit tyto výrazy a vyhledat konkrétní kódy chyb nebo výjimky.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Zobrazit počet chyb a výjimek hlášených vaší aplikací za poslední hodinu

Tento dotaz vytvoří výsečový graf zobrazující počet chyb a výjimek protokolovaných aplikací během poslední hodiny:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Další informace o dotazování protokolů aplikací

Azure Monitor poskytuje rozsáhlou podporu pro dotazování protokolů aplikací pomocí Log Analytics.  Pokud chcete získat další informace o této službě, přečtěte si kurz o [dotazech protokolu](../azure-monitor/log-query/get-started-queries.md) pomocí Azure monitor. [Přehled dotazů protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md) poskytuje další informace o vytváření dotazů k analýze protokolů aplikací.
