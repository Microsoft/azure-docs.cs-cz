---
title: Analýza protokolů a metrik v Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Zjistěte, jak analyzovat diagnostická data v Azure Spring Cloudu
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 544de1b4ac46a58d533f71a46266807a3b93820a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920038"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analýza protokolů a metrik pomocí nastavení diagnostiky

Pomocí diagnostických funkcí Služby Azure Spring Cloud můžete analyzovat protokoly a metriky pomocí některé z následujících služeb:

* Použijte Azure Log Analytics, kde se data zapisují do Azure Storage. Při exportu protokolů do analýzy protokolů dochází ke zpoždění.
* Uložte protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Můžete zadat retenční čas (ve dnech).
* Streamujte protokoly do centra událostí pro příjem službou třetí strany nebo vlastním analytickým řešením.

Zvolte kategorii protokolu a kategorii metrik, kterou chcete sledovat.

## <a name="logs"></a>Protokoly

|Protokol | Popis |
|----|----|
| **Konzola aplikace** | Protokol konzoly všech zákaznických aplikací. | 
| **SystemLogs** | V současné době pouze [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) protokoly v této kategorii. |

## <a name="metrics"></a>Metriky

Úplný seznam metrik najdete v tématu [Spring Cloud Metrics](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

Chcete-li začít, povolte jednu z těchto služeb pro příjem dat. Další informace o konfiguraci Analýzy protokolů najdete [v tématu Začínáme s analýzou protokolů v Azure Monitoru](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurace nastavení diagnostiky

1. Na webu Azure Portal přejděte na instanci Azure Spring Cloud.
1. Vyberte možnost **Nastavení diagnostiky** a pak vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název nastavení a pak zvolte, kam chcete protokoly odeslat. Můžete vybrat libovolnou kombinaci následujících tří možností:
    * **Archivovat do účtu úložiště**
    * **Streamování do centra událostí**
    * **Odeslání do Log Analytics**

1. Zvolte kategorii protokolu a kategorii metriky, kterou chcete sledovat, a zadejte čas uchování (ve dnech). Doba uchovávání se vztahuje pouze na účet úložiště.
1. Vyberte **Uložit**.

> [!NOTE]
> Může existovat mezera až 15 minut mezi při vyzařování protokolů nebo metrik a když se zobrazí ve vašem účtu úložiště, v centru událostí nebo v Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Zobrazení protokolů a metrik
Existují různé metody pro zobrazení protokolů a metrik, jak je popsáno v následujících okružích.

### <a name="use-logs-blade"></a>Použít okno Protokoly

1. Na webu Azure Portal přejděte na instanci Azure Spring Cloud.
1. Chcete-li otevřít podokno **Hledání protokolu,** vyberte **možnost Protokoly**.
1. V poli **Hledání protokolu**
   * Chcete-li zobrazit protokoly, zadejte jednoduchý dotaz, například:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Chcete-li zobrazit metriky, zadejte jednoduchý dotaz, například:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Chcete-li zobrazit výsledek hledání, vyberte **spustit**.

### <a name="use-log-analytics"></a>Použití Log Analytics

1. Na webu Azure Portal v levém podokně vyberte **Log Analytics**.
1. Vyberte pracovní prostor Log Analytics, který jste zvolili při přidání nastavení diagnostiky.
1. Chcete-li otevřít podokno **Hledání protokolu,** vyberte **možnost Protokoly**.
1. V poli **Vyhledávání protokolu**
   * chcete-li zobrazit protokoly, zadejte jednoduchý dotaz, například:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * chcete-li zobrazit metriky, zadejte jednoduchý dotaz, například:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Chcete-li zobrazit výsledek hledání, vyberte **spustit**.
1. Protokoly konkrétní aplikace nebo instance můžete prohledávat nastavením podmínky filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`rozlišuje `=~` malá a velká písmena, ale není.

Další informace o dotazovacím jazyce, který se používá v Log Analytics, najdete v [tématu dotazy protokolu Azure Monitor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Použití účtu úložiště 

1. Na webu Azure Portal v levém podokně vyberte **Účty úložiště**.

1. Vyberte účet úložiště, který jste zvolili při přidání nastavení diagnostiky.
1. Chcete-li otevřít podokno **Kontejner objektů blob,** vyberte **objekty BLOB**.
1. Chcete-li zkontrolovat protokoly aplikací, vyhledejte kontejner s názvem **insights-logs-applicationconsole**.
1. Chcete-li zkontrolovat metriky aplikace, vyhledejte kontejner s názvem **insights-metrics-pt1m**.

Další informace o odesílání diagnostických informací do účtu úložiště najdete v tématu [Store a zobrazení diagnostických dat ve službě Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Použití centra událostí

1. Na webu Azure Portal v levém podokně vyberte **Centra událostí**.

1. Vyhledejte a vyberte centrum událostí, které jste zvolili při přidání nastavení diagnostiky.
1. Chcete-li otevřít podokno **Seznam centra událostí,** vyberte **centrum událostí**.
1. Chcete-li zkontrolovat protokoly aplikací, vyhledejte centrum událostí s názvem **insights-logs-applicationconsole**.
1. Chcete-li zkontrolovat metriky aplikace, vyhledejte centrum událostí s názvem **insights-metrics-pt1m**.

Další informace o odesílání diagnostických informací do centra událostí najdete [v tématu Streamování dat Azure Diagnostics v horké cestě pomocí centra událostí](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analyzovat protokoly

Azure Log Analytics běží s motorem Kusto, takže můžete dotazovat protokoly pro analýzu. Rychlý úvod k dotazování protokolů pomocí Kusto, přečtěte si [kurz Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Protokoly aplikací poskytují důležité informace a podrobné protokoly o stavu, výkonu a dalších aplikacích. V dalších částech jsou některé jednoduché dotazy, které vám pomohou pochopit aktuální a minulé stavy vaší aplikace.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Zobrazení protokolů aplikací z Azure Spring Cloud

Chcete-li zkontrolovat seznam protokolů aplikací z Azure Spring Cloud, seřazené podle času s nejnovější protokoly zobrazeny jako první, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Zobrazit položky protokolů obsahující chyby nebo výjimky

Chcete-li zkontrolovat neseřazené položky protokolu, které zmiňují chybu nebo výjimku, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Pomocí tohoto dotazu můžete vyhledat chyby nebo upravit termíny dotazu a vyhledat konkrétní kódy chyb nebo výjimky. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Zobrazit počet chyb a výjimek nahlášených vaší aplikací za poslední hodinu

Chcete-li vytvořit výsečový graf, který zobrazuje počet chyb a výjimek zaznamenaných aplikací za poslední hodinu, spusťte následující dotaz:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Další informace o dotazování protokolů aplikací

Azure Monitor poskytuje rozsáhlou podporu pro dotazování protokolů aplikací pomocí Log Analytics. Další informace o této službě najdete [v tématu Začínáme s dotazy protokolu v Azure Monitoru](../azure-monitor/log-query/get-started-queries.md). Další informace o vytváření dotazů k analýze protokolů aplikací najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](../azure-monitor/log-query/log-query-overview.md).
