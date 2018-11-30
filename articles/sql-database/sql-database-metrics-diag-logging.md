---
title: Azure SQL database metrik a protokolování diagnostiky | Dokumentace Microsoftu
description: Další informace o tom, jak nakonfigurovat databázi SQL Azure pro ukládání využití prostředků, připojení a statistiky provádění dotazu.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: b903d0ddbccac8fe4fa8b251d409bd8addebb435
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425995"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metrik a protokolování diagnostiky 

Azure SQL Database, elastické fondy, Managed Instance a databáze v Managed Instance můžete generovat metriky a diagnostické protokoly pro snazší monitorování výkonu. Můžete nakonfigurovat databázi pro datový proud využití prostředků, pracovních procesů a relací a možností připojení do jednoho z těchto prostředků Azure:

* **Azure SQL Analytics**: použít jako integrované databáze Azure inteligentní výkon řešení s generování sestav, upozorňování a snížení rizik souvisejících s možností monitorování.
* **Azure Event Hubs**: použít pro integraci telemetrických dat služby SQL Database s vlastními řešeními monitorování nebo aktivními kanály.
* **Azure Storage**: používá pro archivaci obrovských objemů telemetrických dat za zlomek ceny.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

Vysvětlení metrik a protokolů kategorií, které podporují různé služby Azure, můžete chtít zvážit čtení:

* [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Přehled protokoly diagnostiky Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>Povolit protokolování diagnostiky telemetrii

Povolit telemetrická data diagnostiky pro databáze a druhá část dokumentu, který má povolení telemetrická data diagnostiky pro elastické fondy nebo spravované instance pomocí první část tohoto dokumentu. Použijte ke konfiguraci Azure SQL Analytics jako nástroj pro sledování pro zobrazení telemetrická data diagnostiky streamovaná databáze v dalších částech tohoto dokumentu.

> [!NOTE]
> V případě, že používáte elastických fondů nebo spravovaných instancí, kromě povolení telemetrická data diagnostiky pro databáze, jsou také doporučujeme povolit telemetrická data diagnostiky pro i tyto prostředky. Důvodem je, že elastických fondů a spravovaných instancí role databáze kontejnery mají svůj vlastní telemetrická data diagnostiky, který je nezávislý jednotlivých databází diagnostickou telemetrii. 
>

Můžete povolit a spravovat protokolování telemetrická data diagnostiky a metriky pomocí jedné z následujících metod:

- portál Azure
- PowerShell
- Azure CLI
- Rozhraní REST API služby Azure Monitor 
- Šablona Azure Resource Manageru

Když povolíte metrik a protokolování diagnostiky, je třeba zadat cílový prostředků Azure, kde budou shromažďovány vybraná data. Dostupné možnosti zahrnují:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Můžete zřídit nového prostředku Azure nebo si vybrat existující prostředek. Po výběru prostředků, pomocí možnosti nastavení diagnostiky, musíte zadat shromažďovaných údajů.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Povolení protokolování pro Azure SQL Database nebo databází do spravované Instance

Ve výchozím nastavení nejsou povolené protokolování na SQL Database a databází ve spravované instanci Diagnostika a metriky.

Je k dispozici pro kolekci pro Azure SQL Database a databáze ve spravované instanci následující telemetrická data diagnostiky:

| Monitorování telemetrických dat pro databáze | Podpora Azure SQL Database | Databáze v podpoře Managed Instance |
| :------------------- | ------------------- | ------------------- |
| [Všechny metriky](sql-database-metrics-diag-logging.md#all-metrics): obsahuje DTU a procesoru procento DTU a procesoru omezit, fyzické data načtená procento, procento zápisu protokolu, úspěšné nebo neúspěšné/zablokovaný připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště, a Procento úložiště XTP. | Ano | Ne |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): obsahuje informace o statistiku modulu runtime dotazu, jako je využití procesoru a dotazování Statistika trvání. | Ano | Ano |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): obsahuje informace o statistiky čekání dotazu, který vás informuje, co vaše dotazy čekat, jako jsou procesor, protokol a UZAMČENÍ. | Ano | Ano |
| [Chyby](sql-database-metrics-diag-logging.md#errors-dataset): obsahuje informace o chybách SQL, ke kterým došlo u této databáze. | Ano | Ne |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): obsahuje informace o tom, kolik času databázi strávený čeká čekání různých typů. | Ano | Ne |
| [Vypršení časových limitů](sql-database-metrics-diag-logging.md#time-outs-dataset): obsahuje informace o časových limitů, ke které došlo v databázi. | Ano | Ne |
| [Bloky](sql-database-metrics-diag-logging.md#blockings-dataset): obsahuje informace o blokování události, ke kterým došlo v databázi. | Ano | Ne |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): obsahuje užitečné přehledy o výkonu. [Další informace o inteligentních přehledů](sql-database-intelligent-insights.md). | Ano | Ano |

### <a name="azure-portal"></a>portál Azure

Vysílání datového proudu telemetrická data diagnostiky pro Azure SQL Database a databází ve spravované instanci do cíle Azure storage, služby event hubs nebo Log Analytics nakonfigurovaný pomocí nabídky nastavení diagnostiky pro všechny databáze na webu Azure portal.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Konfigurace datové proudy telemetrická data diagnostiky pro Azure SQL Database

   ![Ikona SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Chcete povolit streamování telemetrická data diagnostiky pro **Azure SQL Database**, postupujte podle těchto kroků:

1. Přejít k prostředku Azure SQL Database
2. Vyberte **nastavení diagnostiky**
3. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení
- Je možné vytvořit až tři (3) paralelní připojení do služby stream diagnostickou telemetrii. Pokud chcete nakonfigurovat více paralelních streamování diagnostická data do více zdrojů, vyberte **+ přidat nastavení diagnostiky** vytvoření další nastavení.

   ![Povolit diagnostiku pro službu SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. Zadejte název pro nastavení – pro vlastní potřebu
5. Vyberte, na který prostředek se má datový proud diagnostická data z databáze: **archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**
6. Standardní možnosti monitorování, vyberte zaškrtávací políčka pro databáze telemetrii protokolů diagnostiky: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics** , **QueryStoreWaitStatistics**, **chyby**, **DatabaseWaitStatistics**, **vypršení časových limitů**, **bloky** , **Zablokování**. Tato telemetrie je událost a poskytuje standardní možností monitorování.
7. Rozšířené možnosti monitorování, zaškrtněte políčko pro **AllMetrics**. Toto je 1 minutu na základě telemetrických dat pro databázi telemetrická data diagnostiky, jak je popsáno výše. 
8. Klikněte na **Uložit**.

   ![Konfigurace diagnostiky pro SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Protokoly auditu zabezpečení není možné z databáze nastavení diagnostiky. Pokud chcete povolit streamování protokolů auditu, naleznete v tématu [nastavení auditování databáze](sql-database-auditing.md#subheading-2)a také zobrazit [protokoly auditu SQL v Azure Log Analytics a Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
>

> [!TIP]
> Opakujte předchozí postup pro každý Azure SQL Database, které chcete monitorovat. 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Konfigurace datové proudy telemetrická data diagnostiky pro databáze ve spravované instanci

   ![Databáze spravované Instance ikona](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Chcete povolit streamování telemetrická data diagnostiky pro **databází ve spravované instanci**, postupujte podle těchto kroků:

1. Přejděte k vaší databázi v Managed Instance
2. Vyberte **nastavení diagnostiky**
3. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení
- Je možné vytvořit až tři (3) paralelní připojení do služby stream diagnostickou telemetrii. Pokud chcete nakonfigurovat více paralelních streamování diagnostická data do více zdrojů, vyberte **+ přidat nastavení diagnostiky** vytvoření další nastavení.

   ![Povolit diagnostiku pro Managed Instance databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Zadejte název pro nastavení – pro vlastní potřebu
5. Vyberte, na který prostředek se má datový proud diagnostická data z databáze: **archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**
6. Vyberte zaškrtávací políčka pro databáze diagnostickou telemetrii: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** a **chyby**
7. Klikněte na **Uložit**.

   ![Konfigurovat diagnostiku pro Managed Instance databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Opakujte předchozí postup pro každou databázi ve spravované instanci, kterou chcete monitorovat.
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Povolení protokolování pro elastické fondy nebo spravované Instance

Elastické fondy a spravovaných instancí jako kontejnery pro databáze mají svůj vlastní telemetrická data diagnostiky oddělené od databáze. Tato telemetrická data diagnostiky není standardně povolená. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurace datové proudy telemetrická data diagnostiky pro elastické fondy

   ![Ikona elastického fondu](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Následující telemetrická data diagnostiky je k dispozici pro kolekci, pro elastické fondy prostředků:

| Prostředek | Monitorování telemetrických dat |
| :------------------- | ------------------- |
| **Elastický fond** | [Všechny metriky](sql-database-metrics-diag-logging.md#all-metrics) obsahuje procento eDTU a využití procesoru, limit eDTU/procesoru, fyzických čtení dat procento, protokolu zapisovat, procento, procento relací, procento pracovních procesů, úložiště, procento úložiště, limit úložiště a XTP úložiště. |

Chcete povolit streamování telemetrická data diagnostiky pro **prostředků elastického fondu**, postupujte podle těchto kroků:

1. Přejít k prostředku elastického fondu na webu Azure portal
2. Vyberte **nastavení diagnostiky**
3. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení

   ![Povolit diagnostiku pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Zadejte název pro nastavení – pro vlastní potřebu
5. Vyberte, na který prostředek se má datový proud diagnostická data z elastického fondu: **archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**
6. V případě, že je vybraná Log Analytics, vyberte **konfigurovat** a vytvořte nový pracovní prostor tak, že vyberete **+ vytvořit nový pracovní prostor**, nebo vyberte existující pracovní prostor
7. Zaškrtněte políčko pro elastický fond diagnostickou telemetrii **AllMetrics**
8. Klikněte na **Uložit**.

   ![Konfigurovat diagnostiku pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Opakujte předchozí postup pro každý elastický fond, který chcete monitorovat.
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Konfigurace datové proudy telemetrická data diagnostiky pro Managed Instance

   ![Ikona spravované Instance](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Následující telemetrická data diagnostiky je k dispozici pro kolekci pro Managed Instance prostředků:

| Prostředek | Monitorování telemetrických dat |
| :------------------- | ------------------- |
| **Spravovaná Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) obsahuje počet virtuálních jader, průměrné procento využití procesoru, vstupně-výstupní požadavky, načtených/zapsaných bajtů, vyhrazený úložný prostor, používá prostor úložiště. |

Chcete povolit streamování telemetrická data diagnostiky pro **Managed Instance prostředku**, postupujte podle těchto kroků:

1. Přejít k Managed Instance prostředku na webu Azure portal
2. Vyberte **nastavení diagnostiky**
3. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení

   ![Povolit diagnostiku pro Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Zadejte název pro nastavení – pro vlastní potřebu
5. Vyberte, na který prostředek se má datový proud diagnostická data z elastického fondu: **archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**
6. V případě, že je vybraná Log Analytics, vytvořte nebo použijte existující pracovní prostor
7. Zaškrtněte políčko pro instanci telemetrická data diagnostiky **ResourceUsageStats**
8. Klikněte na **Uložit**.

   ![Konfigurace diagnostiky pro Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Opakujte předchozí postup pro každý Managed Instance, které chcete monitorovat.
>

### <a name="powershell"></a>PowerShell

Chcete-li povolit protokolování pomocí prostředí PowerShell Diagnostika a metriky, použijte následující příkazy:

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku účtu úložiště, ve které chcete odeslat protokoly.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Povolení odesílání protokolů diagnostiky k pracovnímu prostoru Log Analytics, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurace několika prostředků Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [protokolování metrik prostředku povolit Azure pomocí Powershellu](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Zadat ID prostředku pracovního prostoru &lt;$WSID&gt; jako parametr při spuštění skriptu (Enable-AzureRMDiagnostics.ps1) k odesílání diagnostických dat z více zdrojů do pracovního prostoru. Chcete-li získat ID pracovního prostoru &lt;$WSID&gt; do které chcete posílat diagnostická data, nahraďte &lt;subID&gt; ID předplatného nahraďte &lt;RG_NAME&gt; s názvem skupiny prostředků a nahraďte &lt;WS_NAME&gt; s název pracovního prostoru v následujícím skriptu.

- Pokud chcete nakonfigurovat několik prostředků Azure, použijte následující příkazy:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Pokud chcete povolit protokolování pomocí rozhraní příkazového řádku Azure Diagnostika a metriky, použijte následující příkazy:

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ID účtu úložiště je ID prostředku účtu úložiště, ve které chcete odeslat protokoly.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   ID pravidla služby Service Bus je řetězec v tomto formátu:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Povolení odesílání protokolů diagnostiky k pracovnímu prostoru Log Analytics, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Přečtěte si informace o tom, jak [změnit nastavení diagnostiky pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Přečtěte si informace o tom, jak [povolení nastavení diagnostiky při vytváření prostředků pomocí šablony Resource Manageru](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-azure-sql-analytics"></a>Stream do Azure SQL Analytics 

Azure SQL Analytics je Cloudová řešení pro monitorování výkonu databáze, elastické fondy a spravovaných instancí Azure SQL ve velkém měřítku a napříč několika předplatnými prostřednictvím podokně ze skla. Díky monitorování. Shromažďuje a vizualizuje důležité metriky výkonu databáze SQL Azure s integrovanými inteligentními funkcemi pro řešení potíží s výkonem.

![Přehled služby Azure SQL Analytics](../log-analytics/media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Metriky a diagnostické protokoly SQL Database můžete streamovali do služby Azure SQL Analytics s využitím integrovaného **odesílat do Log Analytics** možnost v okně nastavení diagnostiky na portálu. Log Analytics můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor.

### <a name="installation-overview"></a>Přehled instalace

Monitorování fleet databáze SQL je jednoduchý s Azure SQL Analytics. Vyžadují se tři kroky:

1. Vytvoření Azure SQL Analytics řešení z Azure Marketplace
2. Vytvoření pracovního prostoru monitorování v řešení
3. Konfigurovat databáze datového proudu diagnostickou telemetrii do pracovního prostoru, který jste vytvořili.

V případě, že používáte elastických fondů nebo spravovaných instancí, kromě konfigurace telemetrická data diagnostiky databáze, konfigurace, vysílání datového proudu diagnostickou telemetrii z těchto prostředků a také.

### <a name="create-azure-sql-analytics-resource"></a>Vytvořit prostředek služby Azure SQL Analytics

1. Azure SQL Analytics v Azure Marketplace vyhledejte a vyberte ho

   ![Vyhledávání pro Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. Vyberte **vytvořit** na obrazovce Přehled řešení

3. Vyplňte formulář Azure SQL Analytics s dodatečnými informacemi, které je nutné: název pracovního prostoru, předplatné, skupinu prostředků, umístění a cenovou úroveň.
 
   ![Konfigurovat Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Potvrďte výběrem **OK**a dokončit tak, že vyberete **Create**

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurovat databáze záznamu metriky a diagnostické protokoly

Nejjednodušší způsob, jak nakonfigurovat, kdy databáze zaznamenávat jejich metriky je na webu Azure portal – jak je popsáno výše. Na portálu přejděte na váš prostředek databáze SQL a vyberte **nastavení diagnostiky**.

V případě, že používáte elastických fondů nebo spravovaných instancí, budete také muset nakonfigurovat nastavení diagnostiky v tyto prostředky i ke streamování vlastní telemetrická data diagnostiky do pracovního prostoru, který jste vytvořili.

### <a name="use-the-sql-analytics-solution"></a>Použití řešení SQL Analytics

SQL Analytics je hierarchická řídicí panel, který vám umožní přesunout prostřednictvím hierarchie prostředků SQL Database. Další informace o použití řešení SQL Analytics, najdete v článku [monitorování SQL Database s použitím řešení SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Metriky a diagnostické protokoly SQL Database můžete Streamovat do služby Event Hubs pomocí integrované **Stream do centra událostí** možnosti na portálu. ID pravidla služby Service Bus můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co dělat s Diagnostika a metriky protokolů ve službě Event Hubs
Po streamuje vybraná data do služby Event Hubs, jste zase o krok blíž k povolení pokročilých scénářích monitorování. Služba Event Hubs slouží jako branou pro kanál události. Po shromáždění dat do centra událostí můžete transformovat a uložit pomocí libovolného zprostředkovatele datové analýzy v reálném čase nebo adaptérů dávkování/úložišť. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události. Tímto způsobem dostanete příjemci událostí události podle svého vlastního plánu. Další informace o Event Hubs najdete v tématu:

- [Co jsou Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Tady je několik způsobů, můžete použít funkci streamování:

* **Zobrazení stavu služby podle streamovaných dat horká cesta k Power BI**. Pomocí služby Event Hubs, Stream Analytics a Power BI můžete snadno transformovat data metrik a diagnostických nástrojů do téměř v reálném čase na služby Azure. Přehled o tom, jak vytvořit Centrum událostí, zpracování dat pomocí Stream Analytics a pomocí Power BI jako výstup, naleznete v tématu [Stream Analytics a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream protokolů do datových proudů telemetrie a protokolování třetích stran**. Pomocí služby Event Hubs streamování můžete získat metriky a diagnostické protokoly do různých výrobců sledování a protokolů analytická řešení. 

* **Vytvářejte vlastní telemetrii a protokolování platformy**. Pokud už máte platformu vlastními silami sestavených telemetrická data nebo zvažuje vytvoříte, vysoce škálovatelné publikování a odběru povaha služby Event Hubs umožňuje flexibilně ingestovat diagnostické protokoly. Zobrazit [Dan Rosanova návod k použití služby Event Hubs v globálním měřítku telemetrie platformě](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream do služby Storage

SQL Database metriky a diagnostické protokoly mohou být uloženy v úložišti pomocí integrované **archivovat do účtu úložiště** možnosti na portálu. Úložiště můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma metriky a diagnostické protokoly v účtu úložiště

Po nastavení metriky a diagnostické protokoly kolekce se vytvoří kontejner úložiště v účtu úložiště, které jste vybrali při první řádky dat, jsou k dispozici. Struktura těchto objektů blob je:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Nebo jednoduše:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například může být název objektu blob pro všechny metriky:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Pokud chcete zaznamenat data z elastického fondu, název objektu blob se trochu liší:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Stažení metrik a protokolů ze služby Storage

Zjistěte, jak [stažení Diagnostika a metriky protokolů ze služby Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Zásady uchovávání dat a ceny

Pokud vyberete služby Event Hubs nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraňuje data, která je starší než vybrané časové období. Pokud chcete zadat Log Analytics, zásady uchovávání informací, závisí na vybrané cenové úrovně. Spotřeba telemetrická data diagnostiky nad volných jednotek ingestování přidělené každý měsíc se vztahuje. Volných jednotek příjem dat, které jsou k dispozici povolit bezplatné sledování několik databází každý měsíc. Všimněte si, že aktivní databáze s větším zatížení bude ingestovat další data a nečinné databáze. Další informace najdete v tématu [přehledu cen Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Pokud používáte Azure SQL Analytics, můžou snadno monitorovat spotřebu příjem dat v řešení vyberete pracovní prostor OMS v navigační nabídce Azure SQL Analytics, a následným výběrem využití a odhadované náklady.

## <a name="metrics-and-logs-available"></a>Metriky a protokoly, které jsou k dispozici

Shromážděné telemetrické monitorování lze použít pro vlastní **vlastní analýza** a **vývoj aplikací** pomocí [jazyk SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). Struktura shromážděná data, metriky a protokoly, jsou uvedené níže.

## <a name="all-metrics"></a>Všechny metriky

### <a name="all-metrics-for-elastic-pools"></a>Všechny metriky pro elastické fondy

|**Prostředek**|**Metriky**|
|---|---|
|Elastický fond|Procento eDTU eDTU používá, omezení eDTU, procentuální využití procesoru, procentuální čtení fyzické, protokolu zapisovat, procento, procento relací, procento pracovních procesů, úložiště, procento úložiště, limit úložiště, XTP procento úložiště |

### <a name="all-metrics-for-azure-sql-database"></a>Všechny metriky pro službu Azure SQL Database

|**Prostředek**|**Metriky**|
|---|---|
|Azure SQL Database|Procento DTU DTU použít, omezení jednotek DTU, procentuální využití procesoru, procento fyzických datových čtení, zápisu protokolu procento, úspěšné nebo neúspěšné/zablokovaný připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště, procento XTP úložiště, a zablokování |

## <a name="logs"></a>Logs

### <a name="logs-for-managed-instance"></a>Protokoly pro spravovanou instanci

### <a name="resource-usage-stats"></a>Statistika využití prostředků

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: ResourceUsageStats|
|Prostředek|Název prostředku.|
|ResourceType|Název typu prostředku. Vždy: MANAGEDINSTANCES|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název spravované instance.|
|ID prostředku|Identifikátor URI prostředku.|
|SKU_s|SKU produktu spravované Instance|
|virtual_core_count_s|Numver dostupná virtuální jádra|
|avg_cpu_percent_s|Průměrné procento CPU|
|reserved_storage_mb_s|Kapacita vyhrazeného úložiště na Managed Instance|
|storage_space_used_mb_s|Využité úložiště na Managed Instance|
|io_requests_s|Počet vstupně-výstupních operací|
|io_bytes_read_s|Přečtené bajty vstupně-výstupních operací|
|io_bytes_written_s|Zapsané bajty vstupně-výstupních operací|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Protokoly pro databázi Azure SQL Database a Managed Instance

### <a name="query-store-runtime-statistics"></a>Statistické údaje o Query Store

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: QueryStoreRuntimeStatistics|
|OperationName|Název operace Vždy: QueryStoreRuntimeStatisticsEvent|
|Prostředek|Název prostředku.|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|query_hash_s|Hodnota hash dotazu|
|query_plan_hash_s|Hodnota hash plán dotazu.|
|statement_sql_handle_s|Popisovač sql příkazu.|
|interval_start_time_d|Začněte datetimeoffset intervalu v počtu taktů z 1900-1-1.|
|interval_end_time_d|Ukončit datetimeoffset intervalu v počtu taktů z 1900-1-1.|
|logical_io_writes_d|Celkový počet logických vstupně-výstupních operací zápisu.|
|max_logical_io_writes_d|Maximální počet logických vstupně-výstupní operace zapíše za spuštění.|
|physical_io_reads_d|Celkový počet fyzických vstupně-výstupní operace čtení.|
|max_physical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění.|
|logical_io_reads_d|Celkový počet logických čtení vstupně-výstupních operací.|
|max_logical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění.|
|execution_type_d|Typ spuštění.|
|count_executions_d|Počet spuštění dotazu.|
|cpu_time_d|Celkový čas procesoru používané dotazu v mikrosekundách.|
|max_cpu_time_d|Maximální počet procesorů čas spotřebitelem jedno provedení v mikrosekundách.|
|dop_d|Součet stupeň paralelismu.|
|max_dop_d|Maximální míra paralelismu používá pro jedno provedení.|
|rowcount_d|Celkový počet řádků vrácených.|
|max_rowcount_d|Maximální počet řádků vrácených v jedno provedení.|
|query_max_used_memory_d|Celkové množství použité v článku KB paměti.|
|max_query_max_used_memory_d|Maximální množství paměti používané jedno provedení v KB.|
|duration_d|Celková doba spuštění v mikrosekundách.|
|max_duration_d|Maximální doba spuštění jedno provedení.|
|num_physical_io_reads_d|Celkový počet fyzických čtení.|
|max_num_physical_io_reads_d|Maximální počet fyzických čtení za spuštění.|
|log_bytes_used_d|Celkové množství bajtů protokolu.|
|max_log_bytes_used_d|Maximální počet bajtů protokolu použitou na spuštění.|
|query_id_d|ID dotazu v dotazu Store.|
|plan_id_d|ID plánu v Query Store.|

Další informace o [Query Store runtime statistická data](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiky čekání Query Store

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: QueryStoreWaitStatistics|
|OperationName|Název operace Vždy: QueryStoreWaitStatisticsEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|wait_category_s|Kategorie čekání.|
|is_parameterizable_s|Dotaz je parametrizovat.|
|statement_type_s|Typ příkazu.|
|statement_key_hash_s|Příkaz hodnota hash klíče.|
|exec_type_d|Typ spuštění.|
|total_query_wait_time_ms_d|Celkový čas čekání dotazu na kategorie konkrétní čekání.|
|max_query_wait_time_ms_d|Maximální doba čekání dotazu v jednotlivých spuštění na kategorii konkrétní čekání.|
|query_param_type_d|0|
|query_hash_s|Dotazování hash v Query Store.|
|query_plan_hash_s|Hodnota hash plánu dotazu v dotazu Store.|
|statement_sql_handle_s|Popisovač příkazu v Query Store.|
|interval_start_time_d|Začněte datetimeoffset intervalu v počtu taktů z 1900-1-1.|
|interval_end_time_d|Ukončit datetimeoffset intervalu v počtu taktů z 1900-1-1.|
|count_executions_d|Počet spuštění dotazu.|
|query_id_d|ID dotazu v dotazu Store.|
|plan_id_d|ID plánu v Query Store.|

Další informace o [Query Store počkejte statistická data](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Chyby datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: chyby|
|OperationName|Název operace Vždy: ErrorEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|Zpráva|Chybovou zprávu ve formátu prostého textu.|
|user_defined_b|Je ta pasáž, uživatelem definované chybové.|
|error_number_d|Kód chyby.|
|Severity|Závažnost chyby.|
|state_d|Stav chyby.|
|query_hash_s|Hodnota hash dotazu se nezdařilo dotazu, pokud je k dispozici.|
|query_plan_hash_s|Hodnota hash plán dotazu se nezdařilo dotazu, pokud je k dispozici.|

Další informace o [chybových zpráv systému SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Datová sada statistiky čekání databáze

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: DatabaseWaitStatistics|
|OperationName|Název operace Vždy: DatabaseWaitStatisticsEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|wait_type_s|Název typu čekání.|
|start_utc_date_t [UTC]|Měří počáteční čas období.|
|end_utc_date_t [UTC]|Měří období koncový čas.|
|delta_max_wait_time_ms_d|Čekalo se maximální doba spuštění|
|delta_signal_wait_time_ms_d|Signál celková doba čekání.|
|delta_wait_time_ms_d|Celkový čas čekání v období.|
|delta_waiting_tasks_count_d|Počet čekající úlohy.|

Další informace o [databáze statistiky čekání](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Datová sada časové limity

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: vypršení časových limitů|
|OperationName|Název operace Vždy: TimeoutEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|error_state_d|Kód chyby stavu.|
|query_hash_s|Hodnota hash, dotazu, pokud je k dispozici.|
|query_plan_hash_s|Dotazování hash plánu, pokud je k dispozici.|

### <a name="blockings-dataset"></a>Blokování datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: bloky|
|OperationName|Název operace Vždy: BlockEvent|
|Prostředek|Název zdroje|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|lock_mode_s|Režim zámku používán dotazem.|
|resource_owner_type_s|Vlastník zámku.|
|blocked_process_filtered_s|Zablokuje sestava procesu XML.|
|duration_d|Doba trvání uzamčení v mikrosekundách.|

### <a name="deadlocks-dataset"></a>Zablokování datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC] |Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: zablokování|
|OperationName|Název operace Vždy: DeadlockEvent|
|Prostředek|Název prostředku.|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze. |
|ID prostředku|Identifikátor URI prostředku.|
|deadlock_xml_s|Vzájemné zablokování sestavy jazyka XML.|

### <a name="automatic-tuning-dataset"></a>Automatické vyladění datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho tenanta.|
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu.|
|Typ|Vždy: AzureDiagnostics|
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT. SQL|
|Kategorie|Název kategorie. Vždy: AutomaticTuning|
|Prostředek|Název prostředku.|
|ResourceType|Název typu prostředku. Vždy: Servery a databáze|
|SubscriptionId|GUID, které databáze patří do předplatného.|
|ResourceGroup|Název skupiny prostředků, které databáze patří.|
|LogicalServerName_s|Název serveru, který patří do databáze.|
|LogicalDatabaseName_s|Název databáze.|
|ElasticPoolName_s|Název elastického fondu, které patří do databáze, pokud existuje.|
|DatabaseName_s|Název databáze.|
|ID prostředku|Identifikátor URI prostředku.|
|RecommendationHash_s|Jedinečná hodnota hash doporučení automatického ladění.|
|OptionName_s|Operace automatického ladění.|
|Schema_s|Schéma databáze.|
|Table_s|Tabulka vliv.|
|IndexName_s|Název indexu.|
|IndexColumns_s|Název sloupce|
|IncludedColumns_s|Zahrnuté sloupce.|
|EstimatedImpact_s|Odhadovaný dopad doporučení automatického ladění JSON.|
|Event_s|Typ události automatického ladění.|
|Timestamp_t|Časové razítko poslední aktualizace.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights datové sady
Další informace o [formát protokolu Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další postup

Informace o povolení protokolování a pochopení kategorie metrik a protokolů, podporuje různé služby Azure, přečtěte si:

 * [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Přehled protokoly diagnostiky Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Další informace o službě Event Hubs, přečtěte si:

* [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Další informace o službě Storage najdete v tématu Jak [stažení Diagnostika a metriky protokolů ze služby Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
