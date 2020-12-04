---
title: Jak monitorovat analýzu synapse pomocí Azure Monitor
description: Naučte se monitorovat pracovní prostor služby synapse Analytics pomocí Azure Monitor metrik, výstrah a protokolů.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9032fcaf35265c791913f5b69fb0972bada6885f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602457"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Použití Azure Monitor s pracovním prostorem Azure synapse Analytics

Cloudové aplikace jsou komplexní a obsahují mnoho pohyblivých částí. Monitory poskytují data, která vám pomůžou zajistit, aby vaše aplikace zůstaly v dobrém stavu. Monitory také umožňují vyhnout se potenciálním problémům a řešit předchozí problémy. Data monitorování můžete využít k získání podrobných informací o vašich aplikacích. Tato znalostní báze vám pomůže zlepšit výkon a udržovatelnost aplikace. Pomáhá také automatizovat akce, které jinak vyžadují ruční zásah.

Azure Monitor poskytuje metriky infrastruktury na základní úrovni, výstrahy a protokoly pro většinu služeb Azure. Diagnostické protokoly Azure jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Azure synapse Analytics může zapisovat diagnostické protokoly v Azure Monitor.

Další informace najdete v tématu [přehled Azure monitor](../../azure-monitor/overview.md).

## <a name="metrics"></a>Metriky

S monitorováním můžete získat přehled o výkonu a stavu úloh Azure. Nejdůležitější typ dat monitorování je metrika, která se také označuje jako čítač výkonu. Metriky generuje většina prostředků Azure. Monitorování nabízí několik způsobů konfigurace a využívání těchto metrik pro monitorování a řešení potíží.

Pokud chcete získat přístup k těmto metrikám, postupujte podle pokynů v [Azure monitor datovou platformu](../../azure-monitor/platform/data-platform.md).

### <a name="workspace-level-metrics"></a>Metriky na úrovni pracovního prostoru

Tady jsou některé metriky vydávané pracovními prostory:

| **Metrika**                           | **Kategorie metriky, zobrazovaný název**                  | **Jednotka** | **Typy agregace** | **Popis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integrace, metrika spuštění aktivit                     | Počet    | Sum (výchozí), Count                | Celkový počet spuštění aktivit, ke kterým došlo/skončilo v rámci 1 minutového okna. </br></br> Pomocí výsledné dimenze této metriky můžete filtrovat podle úspěšného, neúspěšného nebo zrušeného konečného stavu.|
| IntegrationPipelineRunsEnded         | Integrace, metrika spuštění kanálu                     | Počet    | Sum (výchozí), Count                | Celkový počet spuštění kanálu, ke kterým došlo/skončilo v rámci 1 minutového okna. </br></br> Pomocí výsledné dimenze této metriky můžete filtrovat podle úspěšného, neúspěšného nebo zrušeného konečného stavu. |
| IntegrationTriggerRunsEnded          | Integrace, aktivační událost spustí metriku                      | Počet    | Sum (výchozí), Count                | Celkový počet spuštěných triggerů, které se vyskytly nebo skončily v rámci 1 minutového okna. </br></br> Pomocí výsledné dimenze této metriky můžete filtrovat podle úspěšného, neúspěšného nebo zrušeného konečného stavu. |
| BuiltinSqlPoolDataProcessedBytes     | Vestavěný fond SQL, zpracovaná data (bajty) | Byte | Sum (výchozí) | Množství dat zpracovaných integrovaným fondem SQL bez serveru. |
| BuiltinSqlPoolLoginAttempts          | Vestavěný fond SQL, pokusy o přihlášení | Počet | Sum (výchozí) | Počet pokusů o přihlášení pro integrovaný fond SQL bez serveru |
| BuiltinSqlPoolDataRequestsEnded      | Vestavěný fond SQL, požadavky ukončeny (bajty) | Počet | Sum (výchozí) | Počet ukončených požadavků SQL pro integrovaný fond SQL bez serveru </br></br> Pomocí výsledné dimenze této metriky můžete filtrovat podle konečného stavu. |

### <a name="dedicated-sql-pool-metrics"></a>Metriky vyhrazeného fondu SQL

Tady jsou některé metriky vydávané vyhrazenými fondy SQL:

| **Metrika**                           | **Zobrazovaný název**                  | **Jednotka** | **Typy agregace** | **Popis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU limit                       | Počet   | Max (výchozí), min, AVG | Konfigurovaná velikost fondu SQL |
| DWUUsed                             | DWU použito                        | Počet   | Max (výchozí), min, AVG | Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měřeno podle limitu DWU * procento DWU |
| DWUUsedPercent                      | Procento využitého DWU             | Procento | Max (výchozí), min, AVG | Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měří se tak, že se převezme maximum mezi procenty CPU a v/v. |
| ConnectionsBlockedByFirewall        | Připojení blokovaná bránou firewall | Počet   | Sum (výchozí)   | Počet připojení blokovaných pravidly brány firewall Přečtěte si zásady řízení přístupu pro váš fond SQL a sledujte tato připojení, pokud je počet vysoký. |
| AdaptiveCacheHitPercent             | Procento přístupů do adaptivní mezipaměti   | Procento | Max (výchozí), min, AVG | Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procentuální hodnoty přístupů do mezipaměti určete, jestli se má škálovat pro další kapacitu, nebo znovu spustit úlohy, aby se hydratea mezipaměť. |
| AdaptiveCacheUsedPercent            | Procento využití adaptivní mezipaměti  | Procento | Max (výchozí), min, AVG | Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procenta využití mezipaměti určete, jestli se má škálovat pro další kapacitu nebo znovu spustit úlohy, aby se hydratea mezipaměť. |
| LocalTempDBUsedPercent              | Procento využité místní databáze tempdb    | Procento | Max (výchozí), min, AVG | Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se vysílají každých pět minut. |
| MemoryUsedPercent                   | Procento využité paměti          | Procento | Max (výchozí), min, AVG | Využití paměti ve všech uzlech ve fondu SQL |
| CPUPercent                          | Procento využití procesoru             | Procento | Max (výchozí), min, AVG | Využití CPU ve všech uzlech ve fondu SQL |
| Připojení                         | Připojení                     | Počet   | Sum (výchozí)  | Celkový počet přihlášení ke fondu SQL |
| ActiveQueries                      | Aktivní dotazy                 | Počet   | Sum (výchozí)   | Aktivní dotazy. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému. |
| QueuedQueries                      | Dotazy ve frontě                  | Počet   | Sum (výchozí)   | Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti |
| WLGActiveQueries                   | Aktivní dotazy skupiny úloh   | Počet   | Sum (výchozí)   | Aktivní dotazy v rámci skupiny úloh. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému. |
| WLGActiveQueriesTimeouts           | Vypršení časového limitu dotazu skupiny úloh   | Počet   | Sum (výchozí)   | Dotazy na skupinu úloh, jejichž časový limit vypršel. Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky). |
| WLGQueuedQueries                   | Dotazování skupin úloh ve frontě   | Počet   | Sum (výchozí)   | Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti |
| WLGAllocationBySystemPercent        | Přidělení skupiny úloh podle systémových procent | Procento | Max (výchozí), min, AVG, Sum | Procento přidělení prostředků vzhledem k celému systému |
| WLGAllocationByEffectiveCapResourcePercent   | Přidělení skupiny úloh podle maximálního procenta prostředků | Procento | Max (výchozí), min, AVG | Zobrazuje procento přidělení prostředků vzhledem k procentuální hodnotě prostředku efektivního Cap na skupinu úloh. Tato metrika poskytuje efektivní využití skupiny úloh. |
| WLGEffectiveCapResourcePercent      | Procento efektivního Cap prostředku  | Procento | Max (výchozí), min, AVG | Procentuální hodnota prostředku efektivního Cap pro skupinu úloh. Pokud jsou k dispozici jiné skupiny úloh s min_percentage_resource > 0, effective_cap_percentage_resource se v poměru dolů. |
| WLGEffectiveMinResourcePercent      | Efektivní minimální procento prostředků  | Procento | Max (výchozí), min, AVG, Sum | Nastavení platné minimální procento prostředků s povoleným zvážením úrovně služby a nastavení skupiny úloh. Efektivní min_percentage_resource lze zvýšit na nižší úrovni služeb. |

### <a name="apache-spark-pool-metrics"></a>Metriky fondu Apache Spark

Tady jsou některé metriky vydávané Apache Spark fondy:

| **Metrika**                           | **Kategorie metriky, zobrazovaný název**                  | **Jednotka** | **Typy agregace** | **Popis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Ukončené aplikace Apache Spark  | Počet | Sum (výchozí) | Počet ukončených aplikací fondu Apache Spark |
| BigDataPoolAllocatedCores     | Počet virtuální jádra přidělených fondu Apache Spark                 | Počet | Max (výchozí), min, AVG | Přidělená virtuální jádra pro fond Apache Spark |
| BigDataPoolAllocatedMemory    | Množství paměti (GB) přidělené Apache Spark fondu            | Počet | Max (výchozí), min, AVG | Přidělená paměť pro fond Apache Spark (GB) |
| BigDataPoolApplicationsActive | Aktivní aplikace Apache Spark | Počet | Max (výchozí), min, AVG | Počet aktivních aplikací fondu Apache Spark |

## <a name="alerts"></a>Výstrahy

Přihlaste se k Azure Portal a vyberte **monitorování**  >  **výstrah** a vytvořte upozornění.

### <a name="create-alerts"></a>Vytváření výstrah

1. Vyberte **+ nové pravidlo výstrahy** pro vytvoření nové výstrahy.

1. Definujte **podmínku upozornění** , která určuje, kdy se má výstraha aktivovat.

    > [!NOTE]
    > V rozevíracím seznamu **filtrovat podle typu prostředku** nezapomeňte vybrat **vše** .

1. Zadejte **Podrobnosti výstrahy** , abyste dále určili, jak by měla být tato výstraha konfigurována.

1. Definujte **skupinu akcí** , která určí, kdo má dostávat výstrahy (a jak).

## <a name="logs"></a>Protokoly

### <a name="workspace-level-logs"></a>Protokoly na úrovni pracovního prostoru

Tady jsou protokoly emitované pracovními prostory Azure synapse Analytics:

| Název Log Analytics tabulky | Název kategorie protokolu                 | Description |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Žádosti rozhraní API služby Azure synapse Gateway. |
| SynapseRbacOperations         | SynapseRbacOperations          | Operace řízení přístupu založeného na rolích Azure synapse (SRBAC). |

### <a name="dedicated-sql-pool-logs"></a>Vyhrazené protokoly fondů SQL

Tady jsou protokoly emitované vyhrazené fondy SQL:

| Název Log Analytics tabulky        | Název kategorie protokolu             | Description |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informace o požadavcích a dotazech SQL ve vyhrazeném fondu SQL ve službě Azure synapse.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informace o pracovních procesech, které dokončují kroky DMS ve vyhrazeném fondu SQL ve službě Azure synapse.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informace o krocích, které tvoří daný požadavek nebo dotaz SQL ve vyhrazeném fondu SQL Azure synapse.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Informace o distribuci dotazů v rámci požadavků SQL na vyhrazené synapse fondu SQL ve službě Azure.
| SynapseSqlPoolWaits         | Čeká        | Informace o stavech čekání zjištěných během provádění požadavku nebo dotazu SQL ve vyhrazeném fondu SQL synapse Azure, včetně zámků a čekání na přenosové fronty.

Další informace o těchto protokolech najdete v následujících informacích:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="apache-spark-pool-log"></a>Protokol fondu Apache Spark

Tady je protokol vysílaný Apache Spark fondy:

| Název Log Analytics tabulky               | Název kategorie protokolu              | Description                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informace o ukončených Apache Sparkch aplikacích |

### <a name="diagnostic-settings"></a>Nastavení diagnostiky

Použijte nastavení diagnostiky ke konfiguraci diagnostických protokolů pro prostředky, které nejsou výpočetními prostředky. Nastavení pro ovládací prvek prostředků má následující funkce:

* Určují, kde jsou odesílány diagnostické protokoly. Mezi příklady patří účet Azure Storage, centrum událostí Azure nebo monitorování protokolů.
* Určují, které kategorie protokolů se odesílají.
* Určují, jak dlouho by měla být každá kategorie protokolů udržována v účtu úložiště.
* Nastavení doby uchovávání na 0 dní znamená, že se protokoly uchovávají trvale. V opačném případě může být hodnota libovolný počet dní od 1 do 2 147 483 647.
* Pokud jsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázané, zásady uchovávání dat nemají žádný vliv. K tomuto stavu může dojít například v případě, že jsou vybrány pouze možnosti Event Hubs nebo monitorovat protokoly.
* Zásady uchovávání informací se používají za den. Hranice mezi dny probíhá po půlnoci koordinovaného světového času (UTC). Na konci dne se odpouštějí i protokoly ze dní, které jsou mimo zásady uchovávání informací. Například pokud máte zásady uchovávání informací jeden den, na začátku dnešního dne se odpouštějí protokoly od dřív než včera.

Pomocí Azure Monitor nastavení diagnostiky můžete směrovat diagnostické protokoly pro analýzu do několika různých cílů.

* **Účet úložiště**: uložte diagnostické protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Nastavení diagnostiky můžete použít k určení doby uchování ve dnech.
* **Centrum událostí**: Streamujte protokoly do Azure Event Hubs. Protokoly se stanou vstupem do řešení partner Service nebo vlastní analýzy, jako je Power BI.
* **Log Analytics pracovní prostor**: Analýza protokolů pomocí Log Analytics. Integrace Azure synapse s Log Analytics je užitečná v následujících scénářích:
  * Chcete zapisovat komplexní dotazy na bohatou sadu metrik, která je publikovaná pomocí Azure synapse, do Log Analytics. Můžete vytvářet vlastní výstrahy na těchto dotazech prostřednictvím Azure Monitor.
  * Chcete monitorovat napříč pracovními prostory. Data můžete směrovat z několika pracovních prostorů do jednoho Log Analytics pracovního prostoru.

Můžete použít také účet úložiště nebo obor názvů centra událostí, který není v předplatném prostředku, který vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít k oběma předplatným odpovídající přístup na základě role Azure (Azure RBAC).

#### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Vytvořte nebo přidejte nastavení diagnostiky pro váš pracovní prostor, vyhrazený fond SQL nebo fond Apache Spark.

1. Na portálu klikněte na monitorování. Vyberte **Nastavení**  >  **diagnostiky**.

1. Vyberte pracovní prostor synapse, vyhrazený fond SQL nebo fond Apache Spark, pro který chcete vytvořit nastavení diagnostiky.

1. Pokud ve vybraném pracovním prostoru neexistuje žádné nastavení diagnostiky, zobrazí se výzva k vytvoření nastavení. Vyberte **zapnout diagnostiku**.

   Pokud v pracovním prostoru existují nějaká nastavení diagnostiky, zobrazí se seznam nastavení již nakonfigurovaných pro daný prostředek. Vyberte **Přidat nastavení diagnostiky**.

1. Zadejte název nastavení, vyberte **Odeslat do Log Analytics** a pak vyberte pracovní prostor z **pracovního prostoru Log Analytics**.

    > [!NOTE]
    > Vzhledem k tomu, že tabulka protokolů Azure nemůže mít více než 500 sloupců, **důrazně doporučujeme** vybrat _režim specifický pro daný prostředek_. Další informace najdete v tématu [Log Analytics známá omezení](../../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Vyberte **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro váš pracovní prostor, vyhrazený fond SQL nebo fond Apache Spark. Diagnostické protokoly se do tohoto pracovního prostoru streamují ihned po vygenerování nových dat událostí. Mezi při vygenerování události a jejím zobrazením v Log Analytics může uplynout až 15 minut.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování běhu kanálu v synapse studiu](how-to-monitor-pipeline-runs.md) . 

Další informace o monitorování Apache Sparkch aplikací najdete v článku [monitorování Apache Spark aplikace v synapse studiu](apache-spark-applications.md) .

Další informace o monitorování požadavků SQL najdete v článku [monitorování požadavků SQL v synapse studiu](how-to-monitor-sql-requests.md) .
