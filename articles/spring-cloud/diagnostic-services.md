---
title: Analýza protokolů a metrik v Azure jaře cloudu | Microsoft Docs
description: Naučte se analyzovat diagnostická data v Azure jaře cloudu.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 11f5fcd74b228fa2d57658f5c268e3bebc3c7e93
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499522"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analýza protokolů a metrik pomocí nastavení diagnostiky

**Tento článek se týká:** ✔️ Java ✔️ C #

Pomocí diagnostických funkcí Azure jarního cloudu můžete analyzovat protokoly a metriky pomocí kterékoli z následujících služeb:

* Použijte službu Azure Log Analytics, do které se zapisují data do Azure Storage. Při exportu protokolů do Log Analytics dojde k prodlevě.
* Ukládat protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Můžete určit dobu uchování (ve dnech).
* Streamování protokolů do centra událostí pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu.

Vyberte kategorii protokolu a kategorii metrik, které chcete monitorovat.

> [!TIP]
> Chcete jenom zasílat streamování protokolů? Podívejte se na tento [příkaz rozhraní příkazového řádku Azure](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs).

## <a name="logs"></a>Protokoly

|Protokol | Popis |
|----|----|
| **ApplicationConsole** | Protokol konzoly všech zákaznických aplikací. |
| **SystemLogs** | V současné době se v této kategorii v této kategorii jenom protokoly [jarního cloudového konfiguračního serveru](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) . |

## <a name="metrics"></a>Metriky

Úplný seznam metrik najdete v tématu [metriky jarního cloudu](./spring-cloud-concept-metrics.md#user-metrics-options).

Chcete-li začít, povolte jednu z těchto služeb pro příjem dat. Další informace o konfiguraci Log Analytics najdete [v tématu Začínáme s Log Analytics v Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurace nastavení diagnostiky

1. V Azure Portal přejdete do své instance Azure jaře cloudu.
1. Vyberte možnost **nastavení diagnostiky** a pak vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název nastavení a pak zvolte, kam chcete protokoly odeslat. Můžete vybrat libovolnou kombinaci následujících tří možností:
    * **Archivace do účtu úložiště**
    * **Streamování do centra událostí**
    * **Odeslání do Log Analytics**

1. Zvolte, kterou kategorii protokolů a kategorii metrik chcete monitorovat, a pak zadejte dobu uchování (ve dnech). Doba uchování se vztahuje jenom na účet úložiště.
1. Vyberte **Uložit**.

> [!NOTE]
> 1. Může existovat mezera mezi až 15 minutami, kdy se generují protokoly nebo metriky, a když se objeví ve vašem účtu úložiště, v centru událostí nebo Log Analytics.
> 1. Pokud se instance jarního cloudu Azure odstraní nebo přesune, nebude se tato operace do prostředků **nastavení diagnostiky** kaskádovat. Prostředky **nastavení diagnostiky** je nutné odstranit ručně před operací proti její nadřazené položce, tj. instanci cloudu Azure pro jaře. V opačném případě, pokud je nová instance cloudové cloudové služby Azure zřízena se stejným ID prostředku jako Odstraněná, nebo pokud se instance Azure jaře cloudu přesune zpátky, předchozí zdroje **nastavení diagnostiky** ji budou rozšiřovat.

## <a name="view-the-logs-and-metrics"></a>Zobrazit protokoly a metriky
Existují různé způsoby, jak zobrazit protokoly a metriky, jak je popsáno v následujících nadpisech.

### <a name="use-the-logs-blade"></a>Použijte okno protokoly

1. V Azure Portal přejdete do své instance Azure jaře cloudu.
1. Chcete-li otevřít podokno **prohledávání protokolu** , vyberte možnost **protokoly**.
1. V poli hledání **tabulek**
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
1. Chcete-li zobrazit výsledek hledání, vyberte možnost **Spustit**.

### <a name="use-log-analytics"></a>Použití Log Analytics

1. V Azure Portal v levém podokně vyberte možnost **Log Analytics**.
1. Vyberte pracovní prostor Log Analytics, který jste zvolili při přidání nastavení diagnostiky.
1. Chcete-li otevřít podokno **prohledávání protokolu** , vyberte možnost **protokoly**.
1. V poli hledání **tabulek**
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

1. Chcete-li zobrazit výsledek hledání, vyberte možnost **Spustit**.
1. Nastavením podmínky filtru můžete vyhledat protokol určité aplikace nebo instance.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` rozlišuje velká a malá písmena, ale není `=~` .

Další informace o dotazovacím jazyku, který se používá v Log Analytics, najdete v tématu [Azure monitor protokolování dotazů](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Použití účtu úložiště

1. V Azure Portal vyhledejte **účty úložiště** v levém navigačním panelu nebo ve vyhledávacím poli.
1. Vyberte účet úložiště, který jste zvolili při přidávání nastavení diagnostiky.
1. Pokud chcete otevřít podokno **kontejneru objektů BLOB** , vyberte **objekty blob**.
1. Pokud chcete zkontrolovat protokoly aplikací, vyhledejte kontejner s názvem **Insights-logs-applicationconsole**.
1. Pokud chcete zkontrolovat metriky aplikace, vyhledejte kontejner s názvem **Insights – metriky – pt1m**.

Další informace o odesílání diagnostických informací do účtu úložiště najdete v tématu [ukládání a zobrazení diagnostických dat v Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Použití centra událostí

1. V Azure Portal vyhledejte **Event Hubs** v levém navigačním panelu nebo ve vyhledávacím poli.

1. Vyhledejte a vyberte centrum událostí, které jste si zvolili při přidávání nastavení diagnostiky.
1. Chcete-li otevřít podokno **seznam centra událostí** , vyberte možnost **Event Hubs**.
1. Pokud chcete zkontrolovat protokoly aplikací, vyhledejte centrum událostí s názvem **Insights-logs-applicationconsole**.
1. Pokud chcete zkontrolovat metriky aplikací, vyhledejte centrum událostí s názvem **Insights – metriky – pt1m**.

Další informace o odesílání diagnostických informací do centra událostí najdete v tématu [streamování Azure Diagnostics data v Hot Path pomocí Event Hubs](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analyzovat protokoly

Azure Log Analytics běží s modulem Kusto, takže se můžete dotazovat na své protokoly k analýze. Rychlý úvod k dotazování protokolů pomocí Kusto najdete v [kurzu Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).

Protokoly aplikací poskytují důležité informace a podrobné protokoly o stavu vaší aplikace, výkonu a dalších. V dalších částech najdete několik jednoduchých dotazů, které vám pomůžou pochopit aktuální a poslední stavy vaší aplikace.

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

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Jak převést víceřádkové trasování Java stacku na jeden řádek?

Je k dispozici alternativní řešení pro převod trasování víceřádkového zásobníku na jeden řádek. Výstup protokolu Java můžete upravit tak, aby přeformátoval zprávy o trasování zásobníku a nahradili znaky nového řádku tokenem. Pokud používáte knihovnu Java Logback Library, můžete přeformátovat zprávy trasování zásobníku přidáním `%replace(%ex){'[\r\n]+', '\\n'}%nopex` následujícím způsobem:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Pak můžete token nahradit znovu znaky nového řádku v Log Analytics následujícím způsobem:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Můžete použít stejnou strategii pro jiné knihovny protokolů jazyka Java.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře](spring-cloud-quickstart.md)