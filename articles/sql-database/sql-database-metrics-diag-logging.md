---
title: Azure SQL database metrik a protokolování diagnostiky | Microsoft Docs
description: Další informace o tom, jak nakonfigurovat databázi SQL Azure pro ukládání využití prostředků, připojení a statistik provádění dotazů.
services: sql-database
documentationcenter: ''
author: veljko-msft
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vvasic
ms.openlocfilehash: c9126080db4d8091b672a9250c68a5c5590e10c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650175"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metrik a protokolování diagnostiky 
Databáze SQL Azure můžete emitování metriky a diagnostické protokoly pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

* **Úložiště Azure**: používá k archivaci obrovské objemy telemetrie malé cenu.
* **Azure Event Hubs**: používá pro integraci telemetrie databáze SQL se vlastní řešení monitorování nebo aktivní kanály.
* **Azure Log Analytics**: používá pro řešení monitorování v se na pole s vytváření sestav, výstrahy a zmírnění možnosti. Tato funkce je součástí [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md).

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Povolit protokolování

Ve výchozím nastavení není povoleno metrik a protokolování diagnostiky. Můžete povolit a spravovat metriky a diagnostiku protokolování pomocí jedné z následujících metod:

- Azure Portal
- PowerShell
- Azure CLI
- Rozhraní API REST Azure monitorování 
- Šablona Azure Resource Manageru

Když povolíte metrik a protokolování diagnostiky, budete muset zadat prostředků Azure, kde se shromažďují vybraná data. Mezi dostupné možnosti patří:

- Log Analytics
- Event Hubs
- Úložiště 

Můžete zřídit nového prostředku Azure nebo vybrat existující prostředek. Po výběru prostředků úložiště, budete muset zadat shromažďovaných údajů. Mezi dostupné možnosti patří:

- [Všechny metriky](sql-database-metrics-diag-logging.md#all-metrics): procento DTU obsahuje omezení jednotek DTU, procento využití procesoru, fyzické číst procento, protokolu zapisovat procento, bylo úspěšné nebo neúspěšné/blokováno připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště a procento úložiště XTP.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): obsahuje informace o statistiku modulu runtime dotazu, jako je například doba trvání procesoru využití a dotazu.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): obsahuje informace o čekání Statistika dotazu, který vás informuje, co vaše dotazy čekali, jako je například CPU, LOG a ZAMKNUTÍ.
- [Chyby](sql-database-metrics-diag-logging.md#errors-dataset): obsahuje informace o chybách SQL, ke kterým došlo u této databáze.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): obsahuje informace o tom, kolik času databáze stráví čekání na typy jiné čekání.
- [Vypršení časových limitů](sql-database-metrics-diag-logging.md#time-outs-dataset): obsahuje informace o časových limitů, ke kterým došlo v databázi.
- [Blokování](sql-database-metrics-diag-logging.md#blockings-dataset): obsahuje informace o blokování události, ke kterým došlo v databázi.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): obsahuje inteligentního statistiky. [Další informace o statistice inteligentního](sql-database-intelligent-insights.md).
- **Audit** / **SQLSecurityAuditEvents**: momentálně není k dispozici.

Pokud vyberete Event Hubs nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraňuje data, která je starší než zvolené časové období. Pokud zadáte analýzy protokolů, zásady uchovávání informací, závisí na vybraná cenová úroveň. Další informace najdete v tématu [analýzy protokolů ceny](https://azure.microsoft.com/pricing/details/log-analytics/). 

Chcete-li zjistit, jak povolit protokolování a pochopit metriky a protokolu kategorií, které podporuje různé služby Azure, doporučujeme, abyste si přečetli: 

* [Přehled metriky v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Přehled Azure diagnostics protokolů](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

1. Chcete-li povolit metriky a Diagnostika shromažďování protokolů na portálu, přejděte k SQL Database nebo stránky elastického fondu a pak vyberte **nastavení diagnostiky**.

   ![Povolit na portálu Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Vytvořit nový nebo upravit existující nastavení diagnostiky výběrem cíl a telemetrii.

   ![Nastavení diagnostiky](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Metriky a diagnostiku pomocí prostředí PowerShell protokolování povolit, použijte následující příkazy:

- Pokud chcete povolit úložiště diagnostiky protokolů v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku pro účet úložiště, kde chcete odeslat protokoly.

- Pokud chcete povolit, streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla Azure Service Bus je řetězec s Tento formát:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- K povolení odesílání protokolů diagnostiky do pracovního prostoru analýzy protokolů, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovní prostor analýzy protokolů můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry povolení více možností výstupu můžete kombinovat.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurace více prostředků Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [povolit Azure prostředku metriky protokolování pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Zadejte ID prostředku prostoru &lt;$WSID&gt; jako parametr při provádění skriptu (Enable-AzureRMDiagnostics.ps1) k odesílání diagnostických dat z více zdrojů do pracovního prostoru. Chcete-li získat ID pracovního prostoru &lt;$WSID&gt; na který chcete poslat diagnostická data, nahraďte &lt;subID&gt; s ID předplatného, nahraďte &lt;RG_NAME&gt; s názvem skupiny prostředků, a nahraďte &lt;WS_NAME&gt; s název pracovního prostoru v následující skript.

- Chcete-li nakonfigurovat více prostředků Azure, použijte následující příkazy:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Chcete-li metrik a protokolování pomocí rozhraní příkazového řádku Azure diagnostics povolit, použijte následující příkazy:

- Pokud chcete povolit úložiště diagnostiky protokolů v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ID účtu úložiště je ID prostředku pro účet úložiště, kde chcete odeslat protokoly.

- Pokud chcete povolit, streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   ID pravidla Service Bus je řetězec s Tento formát:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- K povolení odesílání protokolů diagnostiky do pracovního prostoru analýzy protokolů, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Tyto parametry povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Přečtěte si informace o tom, jak [změnit nastavení diagnostiky pomocí rozhraní REST API Azure monitorování](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Přečtěte si informace o tom, jak [povolte nastavení diagnostiky při vytváření prostředků pomocí šablony Resource Manageru](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Datový proud do analýzy protokolů 
Databáze SQL metriky a diagnostické protokoly Streamovat do analýzy protokolů pomocí integrované **odeslat k analýze protokolů** možnost na portálu. Analýzy protokolů můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin prostředí PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API Azure monitorování.

### <a name="installation-overview"></a>Přehled instalace

Monitorování firemního vozového databáze SQL je jednoduchý analýzy protokolů. Vyžadují se tři kroky:

1. Vytvořte prostředek analýzy protokolů.

2. Konfigurovat databáze záznamů metriky a diagnostické protokoly do analýzy protokolů prostředek, který jste vytvořili.

3. Nainstalujte **Azure SQL Analytics** řešení z Galerie v analýzy protokolů.

### <a name="create-a-log-analytics-resource"></a>Vytvořte prostředek analýzy protokolů

1. Vyberte **vytvořit prostředek** v nabídce na levé straně.

2. Vyberte **monitorování + správu**.

3. Vyberte **Log Analytics**.

4. Vyplňte formulář analýzy protokolů společně s dalšími informacemi, které jsou potřeba: název pracovního prostoru, předplatné, skupinu prostředků, umístění a cenovou úroveň.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurovat databáze do záznamu protokolů metriky a Diagnostika

Nejjednodušší způsob, jak nakonfigurovat, kde databáze záznam jejich metriky je prostřednictvím portálu Azure. Na portálu, přejděte do databáze SQL zdroje a vyberte **nastavení diagnostiky**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Nainstalujte řešení analýzy SQL z Galerie

1. Po vytvoření prostředků analýzy protokolů a je do ní toku dat, nainstalujte řešení analýzy SQL. Na domovské stránce v nabídce straně vyberte **řešení Galerie**. V galerii, vyberte **Azure SQL Analytics** řešení a vyberte **přidat**.

   ![řešení monitorování](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na domovské stránce **Azure SQL Analytics** se zobrazí dlaždice. Vyberte tuto dlaždici otevřete řídící panel analýzy SQL.

### <a name="use-the-sql-analytics-solution"></a>Pomocí řešení analýzy SQL

SQL Analytics je hierarchická řídicí panel, který umožňuje přesunout prostřednictvím hierarchie prostředků databáze SQL. Naučte se používat řešení SQL analýzy, najdete v tématu [monitorování SQL Database pomocí řešení analýzy SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Datový proud do centra událostí

Databáze SQL metriky a diagnostické protokoly Streamovat do centra událostí pomocí integrované **datový proud do centra událostí** možnost na portálu. ID pravidla Service Bus můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin prostředí PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API Azure monitorování. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co dělat s metriky a Diagnostika přihlásí Event Hubs
Jakmile je pomocí datového proudu vysílána vybraná data do centra událostí, jste krok blíž ke povolení pokročilých scénářích monitorování. Služba Event Hubs slouží jako přední dveře pro kanál události. Po shromáždění dat do centra událostí můžete transformovat a uložené pomocí kteréhokoli poskytovatele služeb, analýzu v reálném čase nebo adaptérů pro dávkování či ukládání. Služba Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události. Tímto způsobem příjemci událostí můžete k událostem přistupovat podle svého vlastního plánu. Další informace o službě Event Hubs naleznete v tématu:

- [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Tady je několik způsobů, můžete použít streamování možnosti:

* **Zobrazit stav služby streamování data za provozu cesty k Power BI**. Pomocí služby Event Hubs, Stream Analytics a Power BI, můžete snadno transformovat data metriky a Diagnostika do téměř v reálném čase Statistika na služeb Azure. Přehled o tom, jak nastavit centra událostí najdete v tématu zpracování dat pomocí služby Stream Analytics a používat Power BI jako výstup, [Stream Analytics a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream protokoly do jiných datových proudů protokolování a telemetrie**. Pomocí vysílání datového proudu centra událostí můžete získat metriky a Diagnostika protokolů do jiné řešení třetí strany pro analýzu monitorování a protokolu. 

* **Vytvoření vlastní telemetrii a protokolování platformy**. Pokud už máte uživatelské telemetrie platformy nebo zvažují vytvoření jeden, vysoce škálovatelné publikování a odběru povaha Event Hubs umožňuje flexibilně ingestování diagnostické protokoly. V tématu [Dana Rosanova příručka k použití služby Event Hubs v globálním měřítku telemetrie platformě](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Datový proud do úložiště

Databáze SQL metriky a diagnostické protokoly mohou být uloženy v úložišti pomocí integrované **archivu do účtu úložiště** možnost na portálu. Úložiště můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin prostředí PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API Azure monitorování.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma metriky a Diagnostika protokolů v účtu úložiště

Po nastavení metriky a Diagnostika shromažďování protokolů kontejner úložiště se vytvoří v účtu úložiště, kterou jste vybrali při první řádky dat jsou k dispozici. Struktura tyto objekty BLOB je:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Nebo jednodušeji:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například může být název objektu blob pro všechny metriky:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Pokud chcete k zaznamenání dat z elastického fondu, název objektu blob se trochu liší:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Stažení metriky a protokoly ze služby Storage

Zjistěte, jak [stáhnout metriky a diagnostické protokoly z úložiště](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="metrics-and-logs-available"></a>Metriky a k dispozici protokoly.

### <a name="all-metrics"></a>Všechny metriky

|**Prostředek**|**Metriky**|
|---|---|
|Databáze|Procento DTU DTU používá, omezení jednotek DTU, procento využití procesoru, procento fyzické dat pro čtení, zápisu protokolu procento, bylo úspěšné nebo neúspěšné/blokováno připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště, XTP procento úložiště, a zablokování |
|Elastický fond|Procento eDTU eDTU používá, omezení eDTU, procento využití procesoru, fyzické čtení procento, protokolu zapisovat procento, procento relací, procento pracovních procesů, úložiště, procento úložiště, limit úložiště, XTP procento úložiště |
|||

### <a name="query-store-runtime-statistics"></a>Úložiště dotazů statistiku modulu runtime

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: QueryStoreRuntimeStatistics|
|OperationName|Název operace Vždy: QueryStoreRuntimeStatisticsEvent|
|Prostředek|Název prostředku.|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|query_hash_s|Dotaz na hodnotu hash.|
|query_plan_hash_s|Hodnota hash plán dotazu.|
|statement_sql_handle_s|Popisovač sql příkazu.|
|interval_start_time_d|Spuštění datetimeoffset intervalu v počtu rysky z 1900-1-1.|
|interval_end_time_d|Ukončení datetimeoffset intervalu v počtu rysky z 1900-1-1.|
|logical_io_writes_d|Celkový počet logických zápisech vstupně-výstupní operace.|
|max_logical_io_writes_d|Maximální počet logických vstupně-výstupní operace se zapíše na spuštění.|
|physical_io_reads_d|Celkový počet fyzických vstupně-výstupní operace čtení.|
|max_physical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění.|
|logical_io_reads_d|Celkový počet logických čtení vstupně-výstupní operace.|
|max_logical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění.|
|execution_type_d|Typ spouštění.|
|count_executions_d|Počet spuštění dotazu.|
|cpu_time_d|Celkový čas procesoru spotřebovávají dotazu v mikrosekundách.|
|max_cpu_time_d|Příjemce čas procesoru maximální podle jednoho spuštění v mikrosekundách.|
|dop_d|Součet stupně paralelního zpracování.|
|max_dop_d|Maximální počet stupně paralelního zpracování použít pro jednoho spuštění.|
|rowcount_d|Celkový počet vrácených řádků.|
|max_rowcount_d|Maximální počet řádků vrácených v jednoho spuštění.|
|query_max_used_memory_d|Celkové množství paměti v KB.|
|max_query_max_used_memory_d|Maximální množství paměti, které jednoho spuštění v KB.|
|duration_d|Celková doba provádění v mikrosekundách.|
|max_duration_d|Maximální doba provádění jednoho spuštění.|
|num_physical_io_reads_d|Celkový počet fyzických čtení.|
|max_num_physical_io_reads_d|Maximální počet fyzických čtení za spuštění.|
|log_bytes_used_d|Celkové množství bajtů, protokolu.|
|max_log_bytes_used_d|Maximální počet bajtů protokolu použitou na spuštění.|
|query_id_d|ID dotazu v úložišti dotazů.|
|plan_id_d|ID plánu v úložišti dotazů.|

Další informace o [úložiště dotazů modulu runtime statistiky dat](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Úložiště dotazů čekání statistiky

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: QueryStoreWaitStatistics|
|OperationName|Název operace Vždy: QueryStoreWaitStatisticsEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|wait_category_s|Kategorie čekání.|
|is_parameterizable_s|Dotaz je parametrizovat.|
|statement_type_s|Typ příkazu.|
|statement_key_hash_s|Příkaz hodnota hash klíče.|
|exec_type_d|Typ spouštění.|
|total_query_wait_time_ms_d|Celkový čekací čas dotazu na konkrétní čekání kategorii.|
|max_query_wait_time_ms_d|Maximální doba čekání dotazu v jednotlivých provádění na konkrétní čekání kategorii.|
|query_param_type_d|0|
|query_hash_s|Dotaz hash v úložišti dotazů.|
|query_plan_hash_s|Hodnota hash plán dotazu v úložišti dotazů.|
|statement_sql_handle_s|Popisovač příkazu v úložišti dotazů.|
|interval_start_time_d|Spuštění datetimeoffset intervalu v počtu rysky z 1900-1-1.|
|interval_end_time_d|Ukončení datetimeoffset intervalu v počtu rysky z 1900-1-1.|
|count_executions_d|Počet spuštěních dotazu.|
|query_id_d|ID dotazu v úložišti dotazů.|
|plan_id_d|ID plánu v úložišti dotazů.|

Další informace o [úložiště dotazů počkejte statistiky dat](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Chyby datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: chyby|
|OperationName|Název operace Vždy: ErrorEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|Zpráva|Chybová zpráva v prostém textu.|
|user_defined_b|Je bit chyby definované uživatelem.|
|error_number_d|Kód chyby.|
|Severity|Závažnost chyby.|
|state_d|Stav chyby.|
|query_hash_s|Hodnota hash dotazu se nezdařilo dotazu, pokud je k dispozici.|
|query_plan_hash_s|Hodnota hash plán dotazu se nezdařilo dotazu, pokud je k dispozici.|

Další informace o [chybových zpráv systému SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Databáze čekání statistiky datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: DatabaseWaitStatistics|
|OperationName|Název operace Vždy: DatabaseWaitStatisticsEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|wait_type_s|Název typu čekání.|
|start_utc_date_t [UTC]|Měří počáteční čas období.|
|end_utc_date_t [UTC]|Měří období koncový čas.|
|delta_max_wait_time_ms_d|Maximální počet čekali doba provádění|
|delta_signal_wait_time_ms_d|Celkový počet signál doba čekání.|
|delta_wait_time_ms_d|Celkový čekací čas v období.|
|delta_waiting_tasks_count_d|Počet úloh čekání.|

Další informace o [databáze statistiky čekání](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Vypršení časových limitů datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: vypršení časových limitů|
|OperationName|Název operace Vždy: TimeoutEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|error_state_d|Kód chyby stavu.|
|query_hash_s|Dotaz hash, pokud je k dispozici.|
|query_plan_hash_s|Dotaz hash plánu, pokud je k dispozici.|

### <a name="blockings-dataset"></a>Blokování datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID klienta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Při zaznamenávání protokolu časové razítko.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: bloky|
|OperationName|Název operace Vždy: BlockEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery nebo databází|
|SubscriptionId|Předplatné identifikátor GUID, které databáze patří.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, které databáze patří.|
|ElasticPoolName_s|Název elastického fondu, který je součástí databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|lock_mode_s|Režim zámku použitý v dotazu.|
|resource_owner_type_s|Vlastník zámku.|
|blocked_process_filtered_s|Blokované sestava procesu XML.|
|duration_d|Doba trvání zámku v mikrosekundách.|

### <a name="intelligent-insights-dataset"></a>Inteligentní datovou sadu statistiky
Další informace o [formát protokolu inteligentního Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak povolit protokolování a pochopit metriky a protokolu kategorií podporuje různé služby Azure, přečtěte si téma:

 * [Přehled metriky v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Přehled Azure diagnostics protokolů](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Další informace o službě Event Hubs, přečtěte si:

* [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Další informace o ukládání najdete v tématu Jak [stáhnout metriky a diagnostické protokoly z úložiště](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
