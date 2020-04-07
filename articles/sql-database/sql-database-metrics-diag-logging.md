---
title: Konfigurace exportu datových proudů metrik a protokolů prostředků
description: Zjistěte, jak nakonfigurovat export dat metrik a protokolů prostředků, včetně inteligentní diagnostické analýzy z Azure SQL Database do cíle podle vašeho výběru pro ukládání informací o využití prostředků a statistiky provádění dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 9c9f069ad38c65aa0bbfdcde9eef3fed32585d9e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756414"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Konfigurace exportu datového proudu diagnostické telemetrie databáze Azure SQL

V tomto článku se dozvíte o metriky výkonu a protokoly prostředků pro Azure SQL Database, které můžete exportovat do jednoho z několika cílů pro analýzu. Dozvíte se, jak nakonfigurovat export streamování této diagnostické telemetrie prostřednictvím portálu Azure, PowerShellu, rozhraní API Azure, rozhraní REST API a šablon Azure Resource Manager.

Dozvíte se také o cílech, do kterých můžete streamovat tuto diagnostickou telemetrii a jak si vybrat mezi těmito možnostmi. Mezi možnosti cíle patří:

- [Log Analytics a SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Diagnostická telemetrie pro export pro Azure SQL Database

Nejdůležitější mezi diagnostickou telemetrií, kterou můžete exportovat, je protokol Inteligentní přehledy (SQLInsights). [Inteligentní přehledy](sql-database-intelligent-insights.md) využívají integrovanou inteligenci k průběžnému sledování využití databáze prostřednictvím umělé inteligence a detekci rušivých událostí, které způsobují nízký výkon. Po zjištění se provede podrobná analýza, která generuje protokol Intelligent Insights s inteligentním vyhodnocením problému. Toto posouzení se skládá z analýzy hlavní příčiny problému s výkonem databáze a pokud je to možné, doporučení pro zlepšení výkonu. Chcete-li zobrazit jeho obsah, je třeba nakonfigurovat export datových proudů tohoto protokolu.

Kromě streamování exportu protokolu Inteligentní přehledy můžete také exportovat různé metriky výkonu a další protokoly databáze SQL. Následující tabulka popisuje metriky výkonu a protokoly prostředků, které můžete nakonfigurovat pro streamování exportu do jednoho z několika cílů. Tuto diagnostickou telemetrii lze nakonfigurovat pro jednotlivé databáze, elastické fondy a sdružené databáze a spravované instance a databáze instancí.

| Diagnostická telemetrie pro databáze | Podpora jedné databáze a sdružených databází | Podpora databáze spravované instance |
| :------------------- | ----- | ----- |
| [Základní metriky:](#basic-metrics)Obsahuje procento DTU/CPU, limit DTU/CPU, procento čtení fyzických dat, procento zápisu protokolu, úspěšné/neúspěšné/blokované připojením brány firewall, procento relací, procento pracovníků, procento úložiště, úložiště a procento úložiště XTP. | Ano | Ne |
| [Instance a App Advanced](#advanced-metrics): Obsahuje data databáze systému tempdb a velikost souboru protokolu a použitý soubor protokolu v procentech databáze tempdb. | Ano | Ne |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Obsahuje informace o statistikách za běhu dotazu, jako je například využití procesoru a statistika doby trvání dotazu. | Ano | Ano |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Obsahuje informace o dotazu čekat statistiky (jaké dotazy čekal na) jsou procesor, LOG a zamykání. | Ano | Ano |
| [Chyby](#errors-dataset): Obsahuje informace o chybách SQL v databázi. | Ano | Ano |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Obsahuje informace o tom, kolik času databáze strávil čekání na různé typy čekání. | Ano | Ne |
| [Časové osy](#time-outs-dataset): Obsahuje informace o časových časových časových opojcích v databázi. | Ano | Ne |
| [Bloky](#blockings-dataset): Obsahuje informace o blokování událostí v databázi. | Ano | Ne |
| [Zablokování](#deadlocks-dataset): Obsahuje informace o události vzájemné vzájemné zablokování v databázi. | Ano | Ne |
| [Automatické ladění](#automatic-tuning-dataset): Obsahuje informace o doporučení automatickéladění pro databázi. | Ano | Ne |
| [SQLInsights](#intelligent-insights-dataset): Obsahuje inteligentní přehledy výkonu databáze. Další informace najdete v [tématu Inteligentní přehledy](sql-database-intelligent-insights.md). | Ano | Ano |

> [!NOTE]
> Diagnostická nastavení nelze nakonfigurovat pro **systémové databáze**, například hlavní databáze, databáze msdb, modelu, prostředků a databáze tempdb.

## <a name="streaming-export-destinations"></a>Streamování cílů exportu

Tuto diagnostickou telemetrii lze streamovat do jednoho z následujících prostředků Azure pro analýzu.

- **[Pracovní prostor Log Analytics](#stream-into-sql-analytics)**:

  Data streamovaná do [pracovního prostoru Analýzy protokolů](../azure-monitor/platform/resource-logs-collect-workspace.md) může být spotřebovávána [službou SQL Analytics](../azure-monitor/insights/azure-sql.md). SQL Analytics je cloudové monitorování řešení, které poskytuje inteligentní monitorování vašich databází, které zahrnuje zprávy o výkonu, výstrahy a doporučení ke zmírnění rizik. Data streamovaná do pracovního prostoru Log Analytics lze analyzovat pomocí dalších shromážděných dat monitorování a také umožňuje využívat další funkce Azure Monitoru, jako jsou výstrahy a vizualizace
- **[Centra událostí Azure](#stream-into-event-hubs)**:

  Data streamovaná do [Centra událostí Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md)poskytují následující funkce:

  - **Streamujte protokoly do systémů protokolování a telemetrie třetích stran**: Streamujte všechny metriky a protokoly prostředků do jednoho centra událostí a můžete data protokolu do kanálu do nástroje SIEM nebo log analytics třetí strany.
  - **Vytvořte vlastní platformu telemetrie a protokolování:** Vysoce škálovatelná povaha publikování a odběru centra událostí umožňuje pružně ingestovat metriky a protokoly prostředků do vlastní telemetrické platformy. Podrobnosti najdete [v tématu Navrhování a dimenzování globální platformy telemetrie v azure event hubech.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
  - **Zobrazení stavu služeb pomocí streamování dat do Power BI**: Pomocí centra událostí, Stream Analytics a Power BI můžete převést diagnostická data na přehledy služeb Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v [tématech Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamování dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)
- **[Azure Storage](#stream-into-azure-storage)**:

  Data streamovaná do [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) vám umožní archivovat obrovské množství diagnostické telemetrie za zlomek nákladů na předchozí dvě možnosti streamování.

Tato diagnostická telemetrická data vysílaná do jednoho z těchto cílů lze použít k měření využití prostředků a statistiky provádění dotazů pro snadnější sledování výkonu.

![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Povolení a konfigurace exportu datového proudu diagnostické telemetrie

Metriky a protokolování diagnostických telemetrických dat můžete povolit a spravovat pomocí jedné z následujících metod:

- portál Azure
- PowerShell
- Azure CLI
- Rozhraní REST API služby Azure Monitor
- Šablona Azure Resource Manageru

> [!NOTE]
> Pokud chcete povolit streamování telemetrie protokolu auditu, přečtěte si část [Nastavení auditování databáze](sql-database-auditing.md#setup-auditing) a [protokolů auditování v protokolech Azure Monitora a v centru událostí Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurace exportu datového proudu diagnostické telemetrie

Pomocí nabídky **Nastavení diagnostiky** na webu Azure Portal můžete povolit a nakonfigurovat streamování diagnostické telemetrie. Kromě toho můžete ke konfiguraci streamování diagnostické telemetrie použít PowerShell, Azure CLI, [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)a šablony Správce [prostředků.](../azure-monitor/platform/diagnostic-settings-template.md) Můžete nastavit následující cíle pro streamování diagnostické telemetrie: Azure Storage, Azure Event Hubs a Azure Monitor protokoly.

> [!IMPORTANT]
> Export datových proudů diagnostické telemetrie není ve výchozím nastavení povolen.

Vyberte jednu z následujících karet pro podrobné pokyny pro konfiguraci streamování exportu diagnostické telemetrie na webu Azure portal a pro skripty pro provedení stejné s PowerShell a Azure CLI.

# <a name="azure-portal"></a>[portál Azure](#tab/azure-portal)

### <a name="elastic-pools"></a>Elastické fondy

Můžete nastavit prostředek elastického fondu pro shromažďování následujících diagnostických telemetrií:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Elastický bazén** | [Základní metriky](sql-database-metrics-diag-logging.md#basic-metrics) obsahují procento eDTU/CPU, limit eDTU/CPU, procento čtení fyzických dat, procento zápisu protokolu, procento relací, procento pracovníků, úložiště, procento úložiště, limit úložiště a procento úložiště XTP. |

Chcete-li konfigurovat streamování diagnostické telemetrie pro elastické fondy a sdružené databáze, je třeba samostatně konfigurovat každý zvlášť:

- Povolit streamování diagnostické telemetrie pro elastický fond
- Povolit streamování diagnostické telemetrie pro každou databázi v elastickém fondu

Kontejner elastického fondu má vlastní telemetrii oddělenou od telemetrie jednotlivých sdružených databází.

Chcete-li povolit streamování diagnostické telemetrie pro prostředek elastického fondu, postupujte takto:

1. Přejděte na prostředek **elastického fondu** na webu Azure Portal.
2. Vyberte **Nastavení diagnostiky**.
3. Vyberte **Zapnout diagnostiku,** pokud neexistují žádná předchozí nastavení, nebo vyberte **Upravit nastavení** pro úpravu předchozího nastavení.

   ![Povolení diagnostiky pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Zadejte název nastavení pro vlastní odkaz.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivovat do účtu úložiště**, **Streamovat do centra událostí**nebo **Odeslat do analýzy protokolů**.
6. Pro analýzu protokolů vyberte **Konfigurovat** a vytvořte nový pracovní prostor výběrem **+Vytvořit nový pracovní prostor**nebo vyberte existující pracovní prostor.
7. Zaškrtněte políčko pro telemetrii diagnostiky elastického fondu: **Základní** metriky.
   ![Konfigurace diagnostiky pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Vyberte **Uložit**.
9. Kromě toho nakonfigurujte streamování diagnostické telemetrie pro každou databázi v rámci elastického fondu, který chcete sledovat, pomocí následujících kroků popsaných v další části.

> [!IMPORTANT]
> Kromě konfigurace diagnostické telemetrie pro elastický fond je také nutné nakonfigurovat diagnostickou telemetrii pro každou databázi v elastickém fondu.

### <a name="single-or-pooled-database"></a>Jedna nebo sdružená databáze

Můžete nastavit jeden nebo sdružený databázový prostředek pro shromažďování následujících diagnostických telemetrií:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Jedna nebo sdružená databáze** | [Základní metriky](sql-database-metrics-diag-logging.md#basic-metrics) obsahuje procento DTU, použitý DTU, limit DTU, procento procesoru, procento čtení fyzických dat, procento zápisu protokolu, úspěšné/neúspěšné/blokované připojením brány firewall, procento relací, procento pracovníků, úložiště, procento úložiště, procento úložiště XTP a zablokování. |

Chcete-li povolit streamování diagnostické telemetrie pro jednu nebo sdruženou databázi, postupujte takto:

1. Přejděte na prostředek databáze Azure **SQL.**
2. Vyberte **Nastavení diagnostiky**.
3. Vyberte **Zapnout diagnostiku,** pokud neexistují žádná předchozí nastavení, nebo vyberte **Upravit nastavení** pro úpravu předchozího nastavení. Můžete vytvořit až tři paralelní připojení pro telemetrii datového proudu.
4. Vyberte **Přidat diagnostické nastavení** pro konfiguraci paralelního streamování diagnostických dat do více prostředků.

   ![Povolení diagnostiky pro jednu a sdruženou databázi](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Zadejte název nastavení pro vlastní odkaz.
6. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivovat do účtu úložiště**, **Streamovat do centra událostí**nebo **Odeslat do analýzy protokolů**.
7. Pro standardní prostředí monitorování založené na událostech zaškrtněte následující políčka pro telemetrii protokolu diagnostiky databáze: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**a **Locks**.
8. Pro pokročilé, jednominutové monitorování, zaškrtněte políčko pro **základní** metriky.

   ![Konfigurace diagnostiky pro jednu, sdruženou nebo instanci databází](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Vyberte **Uložit**.
10. Opakujte tyto kroky pro každou databázi, kterou chcete sledovat.

> [!TIP]
> Opakujte tyto kroky pro každou jednu a sdruženou databázi, kterou chcete sledovat.

### <a name="managed-instance"></a>Spravovaná instance

Můžete nastavit prostředek spravované instance pro shromažďování následujících diagnostických telemetrií:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Spravovaná instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) obsahuje počet virtuálních jader, průměrné procento procesoru, požadavky vstupně-up, počet bajtů pro čtení a zápis, vyhrazený úložný prostor a využitý úložný prostor. |

Chcete-li nakonfigurovat streamování diagnostické telemetrie pro spravované databáze instancí a instancí, budete muset samostatně konfigurovat každou:

- Povolit streamování diagnostické telemetrie pro spravovanou instanci
- Povolit streamování diagnostické telemetrie pro každou databázi instancí

Kontejner spravované instance má vlastní telemetrii oddělenou od telemetrie každé databáze instancí.

Chcete-li povolit streamování diagnostické telemetrie pro prostředek spravované instance, postupujte takto:

1. Přejděte na prostředek **spravované instance** na webu Azure Portal.
2. Vyberte **Nastavení diagnostiky**.
3. Vyberte **Zapnout diagnostiku,** pokud neexistují žádná předchozí nastavení, nebo vyberte **Upravit nastavení** pro úpravu předchozího nastavení.

   ![Povolení diagnostiky pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Zadejte název nastavení pro vlastní odkaz.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivovat do účtu úložiště**, **Streamovat do centra událostí**nebo **Odeslat do analýzy protokolů**.
6. Pro analýzu protokolů vyberte **Konfigurovat** a vytvořte nový pracovní prostor výběrem **+Vytvořit nový pracovní prostor**nebo použijte existující pracovní prostor.
7. Zaškrtněte políčko pro například diagnostickou telemetrii: **ResourceUsageStats**.

   ![Konfigurace diagnostiky pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Vyberte **Uložit**.
9. Kromě toho nakonfigurujte streamování diagnostické telemetrie pro každou databázi instancí v rámci spravované instance, kterou chcete sledovat, podle kroků popsaných v další části.

> [!IMPORTANT]
> Kromě konfigurace diagnostické telemetrie pro spravovanou instanci je také nutné nakonfigurovat diagnostickou telemetrii pro každou databázi instancí.

### <a name="instance-database"></a>Databáze instancí

Můžete nastavit prostředek databáze instance pro shromažďování následujících diagnostických telemetrií:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Databáze instancí** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) obsahuje počet virtuálních jader, průměrné procento procesoru, požadavky vstupně-up, počet bajtů pro čtení a zápis, vyhrazený úložný prostor a využitý úložný prostor. |

Chcete-li povolit streamování diagnostické telemetrie pro databázi instancí, postupujte takto:

1. Přejděte na **prostředek databáze instance** v rámci spravované instance.
2. Vyberte **Nastavení diagnostiky**.
3. Vyberte **Zapnout diagnostiku,** pokud neexistují žádná předchozí nastavení, nebo vyberte **Upravit nastavení** pro úpravu předchozího nastavení.
   - Můžete vytvořit až tři (3) paralelní připojení pro datové proudy diagnostické telemetrie.
   - Vyberte **+Přidat diagnostické nastavení** pro konfiguraci paralelního streamování diagnostických dat do více prostředků.

   ![Povolit diagnostiku pro databáze například](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Zadejte název nastavení pro vlastní odkaz.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivovat do účtu úložiště**, **Streamovat do centra událostí**nebo **Odeslat do analýzy protokolů**.
6. Zaškrtněte políčka pro telemetrii diagnostiky databáze: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**a **Errors**.
   ![Konfigurace diagnostiky pro databáze instancí](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Vyberte **Uložit**.
8. Opakujte tyto kroky pro každou databázi instancí, kterou chcete sledovat.

> [!TIP]
> Opakujte tyto kroky pro každou databázi instancí, kterou chcete sledovat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Protokolování metrik a diagnostiky můžete povolit pomocí prostředí PowerShell.

- Chcete-li povolit ukládání metrik a protokolů prostředků v účtu úložiště, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  ID účtu úložiště je ID prostředku pro účet cílového úložiště.

- Chcete-li povolit streamování metrik a protokolů prostředků do centra událostí, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Chcete-li povolit odesílání metrik a protokolů prostředků do pracovního prostoru Analýzy protokolů, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

**Konfigurace více prostředků Azure**

Pokud chcete podporovat více předplatných, použijte skript PowerShellu z [povolení protokolování metrik prostředků Azure pomocí PowerShellu](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Zadejte ID \<prostředku\> pracovního prostoru $WSID jako `Enable-AzureRMDiagnostics.ps1` parametr při provádění skriptu pro odeslání diagnostických dat z více prostředků do pracovního prostoru.

- Chcete-li získat \<ID\> pracovního prostoru $WSID cíle diagnostických dat, použijte následující skript:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  \<Nahraďte\> subID ID \<\> ID předplatného, RG_NAME \<\> názvem skupiny prostředků a WS_NAME s názvem pracovního prostoru.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Protokolování metrik a diagnostiky můžete povolit pomocí příkazového příkazového příkazu Azure.

> [!IMPORTANT]
> Skripty umožňující protokolování diagnostiky jsou podporovány pro Azure CLI v1.0. Azure CLI v2.0 není v tuto chvíli podporována.

- Chcete-li povolit ukládání metrik a protokolů prostředků v účtu úložiště, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  ID účtu úložiště je ID prostředku pro účet cílového úložiště.

- Chcete-li povolit streamování metrik a protokolů prostředků do centra událostí, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  ID pravidla služby Service Bus je řetězec s tímto formátem:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Chcete-li povolit odesílání metrik a protokolů prostředků do pracovního prostoru Analýzy protokolů, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

---

## <a name="stream-into-sql-analytics"></a>Streamování do sql analytics

Metriky sql database a protokoly prostředků, které jsou vysílané do pracovního prostoru Analýzy protokolů, můžou azure SQL Analytics spotřebovávat. Azure SQL Analytics je cloudové řešení, které monitoruje výkon jednotlivých databází, elastických fondů a sdružených databází a spravovaných instancí a databází instancí ve velkém měřítku a napříč několika předplatnými. Může vám pomoci shromažďovat a vizualizovat metriky výkonu Azure SQL Database a má integrovanou inteligenci pro řešení potíží s výkonem.

![Přehled Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Přehled instalace

Pomocí Azure SQL Analytics můžete sledovat kolekci databází Azure SQL pomocí následujících kroků:

1. Vytvořte řešení Azure SQL Analytics z Azure Marketplace.
2. Vytvořte pracovní prostor Analýzy protokolů v řešení.
3. Nakonfigurujte databáze pro streamování diagnostické telemetrie do pracovního prostoru.

Export streamování této diagnostické telemetrie můžete nakonfigurovat pomocí integrované možnosti **Odeslat do analýzy protokolů** na kartě Nastavení diagnostiky na webu Azure Portal. Streamování do pracovního prostoru Analýzy protokolů můžete také povolit pomocí nastavení diagnostiky pomocí [rutin prostředí PowerShell](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), [rozhraní API Azure](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), rozhraní API [monitoru Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)nebo šablon Správce [prostředků](../azure-monitor/platform/diagnostic-settings-template.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Vytvoření prostředku Azure SQL Analytics

1. Vyhledejte Azure SQL Analytics na Azure Marketplace a vyberte ho.

   ![Hledání Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Na obrazovce přehledu řešení vyberte **Vytvořit.**

3. Vyplňte formulář Azure SQL Analytics dalšími požadovanými informacemi: názvem pracovního prostoru, předplatným, skupinou prostředků, umístěním a cenovou úrovní.

   ![Konfigurace Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Vyberte **OK,** chcete-li potvrdit, a pak vyberte **Vytvořit**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurace prostředku pro záznam metrik a protokolů prostředků

Je třeba samostatně nakonfigurovat datové proudy diagnostických telemetrie pro jednu a sdruženou databázi, elastické fondy, spravované instance a databáze instancí. Nejjednodušší způsob, jak nakonfigurovat, kde je metrika záznamů o prostředcích pomocí portálu Azure. Podrobné kroky naleznete v [tématu Konfigurace exportu datových proudů diagnostické telemetrie](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Použití Azure SQL Analytics pro monitorování a upozorňování

Sql Analytics můžete použít jako hierarchický řídicí panel k zobrazení prostředků databáze SQL.

- Informace o tom, jak používat Azure SQL Analytics, najdete [v tématu Sledování databáze SQL pomocí SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Informace o tom, jak nastavit výstrahy v nástroji SQL Analytics, najdete v [tématu Vytváření výstrah pro databáze, elastické fondy a spravované instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Metriky a protokoly prostředků databáze SQL můžete streamovat do centra událostí pomocí integrovaného **streamu do** centra událostí na webu Azure Portal. ID pravidla služby Service Bus můžete také povolit pomocí nastavení diagnostiky prostřednictvím rutin prostředí PowerShell, rozhraní API Azure nebo rozhraní REST monitoru Azure.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Co dělat s metrikami a protokoly zdrojů v centru událostí

Po streamování vybraných dat do centra událostí jste o krok blíže k povolení pokročilých scénářů monitorování. Centra událostí slouží jako přední dveře pro kanál událostí. Po shromažďování dat do centra událostí je lze transformovat a uložit pomocí zprostředkovatele analýzy v reálném čase nebo adaptéru úložiště. Event Hubs odděluje produkci proudu událostí od spotřeby těchto událostí. Tímto způsobem mohou spotřebitelé událostí přistupovat k událostem podle vlastního plánu. Další informace o centru událostí najdete v tématu:

- [Co jsou Centra událostí Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Streamované metriky v centru událostí můžete použít k tomuto:

- **Zobrazení stavu služby streamováním dat s horkou cestou do Power BI**

  Pomocí Event Hubs, Stream Analytics a Power BI můžete snadno transformovat data metrik a diagnostiky na přehledy služeb Azure téměř v reálném čase. Přehled nastavení centra událostí, zpracování dat pomocí Stream Analytics a použití Power BI jako výstupu najdete v [článku Stream Analytics a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Datové proudy protokolů do protokolování třetích stran a telemetrických datových proudů**

  Pomocí streamování centra událostí můžete získat metriky a protokoly prostředků do různých řešení pro monitorování a analýzu protokolů třetích stran.

- **Vytvoření vlastní platformy pro telemetrii a protokolování**

  Máte již vlastní telemetrickou platformu nebo zvažujete její vytvoření? Vysoce škálovatelná povaha publikování a odběru centra událostí umožňuje flexibilně ingestovat metriky a protokoly prostředků. Podívejte se [na příručku Dana Rosanova k používání event hubů v globální telemetrické platformě](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Streamování do Azure Storage

Metriky a protokoly prostředků můžete ukládat ve službě Azure Storage pomocí integrované možnosti Archivu na webu **účtu úložiště.** Úložiště můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin prostředí PowerShell, rozhraní API Azure nebo rozhraní REST rozhraní Azure Monitor.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schéma metrik a protokolů prostředků v účtu úložiště

Po nastavení metriky a kolekce protokolů prostředků, kontejner úložiště se vytvoří v účtu úložiště, který jste vybrali, když jsou k dispozici první řádky dat. Struktura objektů BLOB je:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Nebo jednodušeji:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Název objektu blob pro základní metriky může být například:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Název objektu blob pro ukládání dat z elastického fondu vypadá takto:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Zásady uchovávání dat a ceny

Pokud vyberete Centra událostí nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraní data, která jsou starší než vybrané časové období. Pokud zadáte Log Analytics, zásady uchovávání informací závisí na vybrané cenové úrovni. V tomto případě mohou poskytované volné jednotky pro požití dat umožnit bezplatné sledování několika databází každý měsíc. Jakékoli spotřebě diagnostické telemetrie přesahující volné jednotky mohou vzniknout náklady.

> [!IMPORTANT]
> Aktivní databáze s těžší úlohy ingestovat více dat než nečinné databáze. Další informace naleznete v [tématu Log analytics pricing](https://azure.microsoft.com/pricing/details/monitor/).

Pokud používáte Azure SQL Analytics, můžete sledovat spotřebu dat tak, že vyberete **OMS Workspace** v navigační nabídce Azure SQL Analytics a pak vyberete **Využití** a **odhadované náklady**.

## <a name="metrics-and-logs-available"></a>Metriky a protokoly k dispozici

Monitorování telemetrie, která je k dispozici pro jednotlivé databáze, sdružené databáze, elastické fondy, spravované instance a databáze instancí je dokumentováno v této části článku. Shromážděná telemetrie monitorování v rámci služby SQL Analytics se dá použít pro vlastní analýzu a vývoj aplikací pomocí jazyka [dotazů protokolu Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)

### <a name="basic-metrics"></a>Základní metriky

Podrobnosti o základních metrikách podle prostředků naleznete v následujících tabulkách.

> [!NOTE]
> Základní metriky možnost byla dříve známá jako všechny metriky. Změna byla pouze na pojmenování a nedošlo k žádné změně sledovaných metrik. Tato změna byla zahájena, aby bylo možné v budoucnu zavést další kategorie metrik.

#### <a name="basic-metrics-for-elastic-pools"></a>Základní metriky pro elastické fondy

|**Zdrojů**|**Metriky**|
|---|---|
|Elastický fond|procento eDTU, použité eDTU, limit eDTU, procento procesoru, procento čtení fyzických dat, procento zápisu protokolu, procento relací, procento pracovníků, úložiště, procento úložiště, limit úložiště, procento úložiště XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Základní metriky pro jednu a sdruženou databázi

|**Zdrojů**|**Metriky**|
|---|---|
|Jednotná a sdružená databáze|Procento DTU, použité DTU, Limit DTU, Procento procesoru, procento čtení fyzických dat, procento zápisu protokolu, Úspěšné/Neúspěšné/Blokované připojením brány firewall, procento relací, procento pracovníků, procento úložiště, procento úložiště, procento úložiště XTP a zablokování |

### <a name="advanced-metrics"></a>Pokročilé metriky

Podrobnosti o rozšířených metrikách naleznete v následující tabulce.

|**Metrika**|**Zobrazovaný název metriky**|**Popis**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Procento jádra procesu SQL Serveru|Procento využití procesoru pro proces serveru SQL Server měřené operačním systémem.|
|sqlserver_process_memory_percent<sup>1.</sup> |Procento paměti procesu serveru SQL Server|Procento využití paměti pro proces serveru SQL Server měřeno operačním systémem.|
|<sup>2</sup> tempdb_data_size.| Velikost datového souboru Tempdb kilobajty |Velikost datového souboru Tempdb kilobajty.|
|<sup>2</sup> tempdb_log_size.| Velikost souboru protokolu databáze Tempdb kilobajty |Velikost souboru protokolu databáze Tempdb kilobajtů.|
|<sup>2.</sup> tempdb_log_used_percent| Použitý protokol procenta databáze dat |Použitý protokol procenta databáze.|

<sup>1</sup> Tato metrika je k dispozici pro databáze pomocí nákupního modelu virtuálních jader se 2 virtuálními jádry a vyššími nebo 200 DTU a vyššími pro nákupní modely založené na DTU. 

<sup>2</sup> Tato metrika je k dispozici pro databáze pomocí nákupního modelu virtuálních jader se 2 virtuálními jádry a vyššími nebo 200 DTU a vyššími pro nákupní modely založené na DTU. Tato metrika není aktuálně k dispozici pro hyperškálovatné databáze nebo datové sklady.

### <a name="basic-logs"></a>Základní protokoly

Podrobnosti o telemetrii, které jsou k dispozici pro všechny protokoly, jsou popsány v následujících tabulkách. Viz [podporované diagnostické telemetrie](#diagnostic-telemetry-for-export-for-azure-sql-database) pochopit, které protokoly jsou podporovány pro konkrétní databázi flavor – Azure SQL single, sdružené nebo instance databáze.

#### <a name="resource-usage-stats-for-managed-instances"></a>Statistiky využití prostředků pro spravované instance

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: ResourceUsageStats |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: MANAGEDINSTANCES |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název spravované instance |
|ResourceId|Identifikátor URI prostředku |
|SKU_s|Skladová položka produktu spravované instance |
|virtual_core_count_s|Počet dostupných virtuálních jader |
|avg_cpu_percent_s|Průměrné procento procesoru |
|reserved_storage_mb_s|Rezervovaná kapacita úložiště ve spravované instanci |
|storage_space_used_mb_s|Použité úložiště ve spravované instanci |
|io_requests_s|Počet vops |
|io_bytes_read_s|Čtení bajtů VOPS |
|io_bytes_written_s|Zapsané bajty VOPS |

#### <a name="query-store-runtime-statistics"></a>Statistiky zaběhu úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: QueryStoreRuntimeStatistics |
|OperationName|Název operace. Vždy: QueryStoreRuntimeStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|query_hash_s|Hodnota hash dotazu |
|query_plan_hash_s|Hodnota hash plánu dotazů |
|statement_sql_handle_s|Popisovač sql příkazu |
|interval_start_time_d|Počáteční časový posun intervalu v počtu značek od 1900-1-1 |
|interval_end_time_d|Konec datetimeoffset intervalu v počtu značek od 1900-1-1 |
|logical_io_writes_d|Celkový počet logických zápisů vo |
|max_logical_io_writes_d|Maximální počet logických zápisů vi na spuštění |
|physical_io_reads_d|Celkový počet fyzických čtení vstupně-ti. |
|max_physical_io_reads_d|Maximální počet čtení logických vstupně-iO na spuštění |
|logical_io_reads_d|Celkový počet čtení logických vstupně-ti, vstupně-li |
|max_logical_io_reads_d|Maximální počet čtení logických vstupně-iO na spuštění |
|execution_type_d|Typ spuštění |
|count_executions_d|Počet spuštění dotazu |
|cpu_time_d|Celkový čas procesoru spotřebovaný dotazem v mikrosekundách |
|max_cpu_time_d|Maximální čas procesoru spotřebitele jedním spuštěním v mikrosekundách |
|dop_d|Součet stupňů paralelismu |
|max_dop_d|Maximální stupeň paralelismu použitý pro jedno spuštění |
|rowcount_d|Celkový počet vrácených řádků |
|max_rowcount_d|Maximální počet řádků vrácených v jednom spuštění |
|query_max_used_memory_d|Celkové množství paměti použité v KB |
|max_query_max_used_memory_d|Maximální množství paměti používané jedním spuštěním v kb |
|duration_d|Celková doba provádění v mikrosekundách |
|max_duration_d|Maximální doba provádění jednoho spuštění |
|num_physical_io_reads_d|Celkový počet fyzických čtení |
|max_num_physical_io_reads_d|Maximální počet fyzických čtení na spuštění |
|log_bytes_used_d|Celkový počet použitých bajtů protokolu |
|max_log_bytes_used_d|Maximální počet bajtů protokolu použitých na spuštění |
|query_id_d|ID dotazu v úložišti dotazů |
|plan_id_d|ID plánu v úložišti dotazů |

Další informace o [datech statistik y runtime úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Statistiky čekání úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: QueryStoreWaitStatistics |
|OperationName|Název operace. Vždy: QueryStoreWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_category_s|Kategorie čekání |
|is_parameterizable_s|Je parametrizovatelný dotaz |
|statement_type_s|Typ příkazu |
|statement_key_hash_s|Hash klíče příkazu |
|exec_type_d|Typ provedení |
|total_query_wait_time_ms_d|Celková čekací doba dotazu v konkrétní kategorii čekání |
|max_query_wait_time_ms_d|Maximální čekací doba dotazu v jednotlivých spuštěních v konkrétní kategorii čekání |
|query_param_type_d|0 |
|query_hash_s|Hodnota hash dotazu v úložišti dotazů |
|query_plan_hash_s|Hash plánu dotazů v úložišti dotazů |
|statement_sql_handle_s|Popisovač výkazu v úložišti dotazů |
|interval_start_time_d|Počáteční časový posun intervalu v počtu značek od 1900-1-1 |
|interval_end_time_d|Konec datetimeoffset intervalu v počtu značek od 1900-1-1 |
|count_executions_d|Počet spuštění dotazu |
|query_id_d|ID dotazu v úložišti dotazů |
|plan_id_d|ID plánu v úložišti dotazů |

Další informace o [datech statistik y čekání v úložišti dotazů](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Datová sada chyb

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: Chyby |
|OperationName|Název operace. Vždy: ErrorEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|Zpráva|Chybová zpráva ve formátu prostého textu |
|user_defined_b|Je chyba uživatelem definovaná bit |
|error_number_d|Kód chyby |
|Severity|Závažnost chyby |
|state_d|Stav chyby |
|query_hash_s|Hodnota hash dotazu na neúspěšný dotaz, pokud je k dispozici |
|query_plan_hash_s|Hodnota hash plánu dotazu na neúspěšný dotaz, pokud je k dispozici |

Další informace o [chybových zprávách serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Datová sada statistiky čekání databáze

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: DatabaseWaitStatistics |
|OperationName|Název operace. Vždy: DatabaseWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_type_s|Název typu čekání |
|start_utc_date_t [UTC]|Čas zahájení měřeného období |
|end_utc_date_t [UTC]|Měřený čas ukončení období |
|delta_max_wait_time_ms_d|Maximální doba čekání na provedení |
|delta_signal_wait_time_ms_d|Celkový počet čekacích dob signálů |
|delta_wait_time_ms_d|Celková čekací doba v období |
|delta_waiting_tasks_count_d|Počet čekajících úkolů |

Další informace o [statistikách čekání v databázi](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Datová sada časového očasech

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: Časové osy |
|OperationName|Název operace. Vždy: TimeoutEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|error_state_d|Kód stavu chyby |
|query_hash_s|Hash dotazu, je-li k dispozici |
|query_plan_hash_s|Hash plánu dotazu, je-li k dispozici |

#### <a name="blockings-dataset"></a>Blokuje datovou sadu

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: Bloky |
|OperationName|Název operace. Vždy: BlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|lock_mode_s|Režim uzamčení používaný dotazem |
|resource_owner_type_s|Vlastník zámku |
|blocked_process_filtered_s|Xml sestavy blokovaného procesu |
|duration_d|Doba trvání zámku v mikrosekundách |

#### <a name="deadlocks-dataset"></a>Datová sada zablokování

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC] |Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: Zablokování |
|OperationName|Název operace. Vždy: DeadlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|deadlock_xml_s|Xml sestavy zablokování |

#### <a name="automatic-tuning-dataset"></a>Datová sada automatického ladění

|Vlastnost|Popis|
|---|---|
|TenantId|ID vašeho klienta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko při zaznamenání protokolu |
|Typ|Vždy: AzureDiagnostics |
|ResourceProvider|Název zprostředkovatele prostředků. Vždy: MICROSOFT. Sql |
|Kategorie|Název kategorie. Vždy: Automatické ladění |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|LogicalDatabaseName_s|Název databáze |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|RecommendationHash_s|Unikátní hash automatického ladění doporučení |
|OptionName_s|Automatický provoz ladění |
|Schema_s|Schéma databáze |
|Table_s|Ovlivněná tabulka |
|IndexName_s|Název indexu |
|IndexColumns_s|Název sloupce |
|IncludedColumns_s|Zahrnuté sloupce |
|EstimatedImpact_s|Odhadovaný dopad doporučení automatického ladění JSON |
|Event_s|Typ události automatického ladění |
|Timestamp_t|Poslední aktualizované časové razítko |

#### <a name="intelligent-insights-dataset"></a>Datová sada Inteligentní přehledy

Přečtěte si další informace o [formátu protokolu Inteligentní přehledy](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak povolit protokolování a porozumět kategoriím metrik a protokolů podporovaným různými službami Azure, najdete v následujících tématech:

- [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Přehled protokolů platformy Azure](../azure-monitor/platform/platform-logs-overview.md)

Další informace o centru událostí najdete v:

- [Co je služba Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Informace o tom, jak nastavit výstrahy na základě telemetrie z analýzy protokolů, najdete v těchto tématech:

- [Vytváření výstrah pro databázi SQL a spravovanou instanci](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
