---
title: Konfigurace exportu metrik a protokolů prostředků do streamování
description: Přečtěte si, jak nakonfigurovat streamování exportu metrik a protokolů prostředků, včetně inteligentní analýzy diagnostiky z Azure SQL Database a Azure SQL Managed instance, do cíle, kde můžete ukládat informace o využití prostředků a statistikách spouštění dotazů.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1442ca7957a458e1458c4815033bf5e79c67c32a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448919"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Konfigurace exportu streamování Azure SQL Database a diagnostiky diagnostické telemetrie SQL spravované instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto článku se dozvíte o metrikách výkonu a protokolech prostředků pro Azure SQL Database, které můžete exportovat do jednoho z několika cílů pro účely analýzy. Naučíte se, jak nakonfigurovat export této diagnostické telemetrie pomocí streamování prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, REST API a Azure Resource Manager šablon.

Dozvíte se také o cílových umístěních, do kterých můžete zasílat streamování této diagnostické telemetrie a jak si vybrat z těchto možností. Mezi možnosti cíle patří:

- [Analýzy Log Analytics a SQL](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Diagnostická telemetrie pro export

Nejdůležitější mezi diagnostickou telemetrii, kterou můžete exportovat, je protokol Intelligent Insights (SQLInsights). [Intelligent Insights](intelligent-insights-overview.md) pomocí integrovaných inteligentních funkcí průběžně monitoruje využití databáze prostřednictvím umělých inteligentních funkcí a detekuje rušivé události, které způsobují špatný výkon. Po zjištění se provede podrobná analýza, která generuje protokol Intelligent Insights s inteligentním vyhodnocením problému. Toto posouzení se skládá z analýzy hlavní příčiny problémů s výkonem databáze a tam, kde je to možné, doporučení pro zlepšení výkonu. Aby bylo možné zobrazit obsah, je třeba pro tento protokol nakonfigurovat export streamování.

Kromě streamování exportu Intelligent Insightsho protokolu můžete také exportovat nejrůznější metriky výkonu a další protokoly databáze. Následující tabulka popisuje metriky výkonu a protokoly prostředků, které můžete nakonfigurovat pro export streamování do jednoho z několika míst. Tuto diagnostiku telemetrie je možné nakonfigurovat pro izolované databáze, elastické fondy a databáze ve fondu a spravované instance a databáze instancí.

| Diagnostická telemetrie pro databáze | Podpora Azure SQL Database | Podpora spravované instance Azure SQL |
| :------------------- | ----- | ----- |
| [Základní metriky](#basic-metrics): obsahuje hodnoty DTU/CPU, DTU/CPU, procentuální podíl fyzického data, procento zápisu protokolu, úspěšné/neúspěšné/blokované připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště a procento XTP úložiště. | Yes | No |
| [Rozšířená instance a aplikace](#advanced-metrics): obsahuje data systémové databáze tempdb a velikost souboru protokolu a soubor protokolu tempdb%. | Yes | No |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): obsahuje informace o statistice za běhu dotazu, jako je například využití procesoru a statistika doby trvání dotazu. | Yes | Yes |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): obsahuje informace o statistice čekání na dotaz (co vaše dotazy čekaly), jako je například CPU, protokol a uzamykání. | Yes | Yes |
| [Chyby](#errors-dataset): obsahuje informace o chybách SQL v databázi. | Yes | Yes |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): obsahuje informace o tom, kolik času databáze strávila čekáním na různé typy čekání. | Yes | No |
| [Timeout](#time-outs-dataset): obsahuje informace o časových limitech v databázi. | Yes | No |
| [Bloky](#blockings-dataset): obsahuje informace o blokujících událostech v databázi. | Yes | No |
| [Zablokování](#deadlocks-dataset): obsahuje informace o událostech zablokování v databázi. | Yes | No |
| [AutomaticTuning](#automatic-tuning-dataset): obsahuje informace o automatickém ladění doporučení pro databázi. | Yes | No |
| [SQLInsights](#intelligent-insights-dataset): obsahuje Intelligent Insights do výkonu pro databázi. Další informace najdete v tématu [Intelligent Insights](intelligent-insights-overview.md). | Yes | Yes |

> [!NOTE]
> Nastavení diagnostiky nelze konfigurovat pro **systémové databáze**, jako jsou hlavní databáze, databáze msdb, model, prostředky a databáze tempdb.

## <a name="streaming-export-destinations"></a>Cíle exportu pro streamování

Tato diagnostická telemetrie se dá streamovat do jednoho z následujících prostředků Azure pro účely analýzy.

- **[Log Analytics pracovní prostor](#stream-into-sql-analytics)**:

  Data streamovaná do [pracovního prostoru Log Analytics](../../azure-monitor/platform/resource-logs-collect-workspace.md) můžou využívat [SQL Analytics](../../azure-monitor/insights/azure-sql.md). SQL Analytics je cloudové řešení monitorování, které poskytuje inteligentní monitorování vašich databází, které zahrnuje sestavy výkonu, výstrahy a doporučení pro zmírnění rizik. Data streamovaná do pracovního prostoru Log Analytics lze analyzovat pomocí dalších shromažďovaných dat monitorování a také vám umožní využít jiné funkce Azure Monitor, jako jsou výstrahy a vizualizace.
- **[Event Hubs Azure](#stream-into-event-hubs)**:

  Data streamovaná do [centra událostí Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)poskytují následující funkce:

  - **Streamování protokolů na systémy protokolování a telemetrie od jiných výrobců**: Streamujte všechny metriky a protokoly prostředků do jednoho centra událostí, do kterého se budou odesílat data protokolu pro Siem nebo nástroj Log Analytics od jiného výrobce.
  - **Vytvoření vlastní telemetrie a protokolovací platformy**: vysoce škálovatelná povaha pro publikování a odběr centra událostí umožňuje pružně ingestovat metriky a protokoly prostředků do vlastní platformy telemetrie. Podrobnosti najdete v tématu [navrhování a změna velikosti platformy telemetrie s globálním škálováním na platformě Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - **Zobrazení stavu služby podle streamování dat do Power BI**: pomocí Event Hubs, Stream Analytics a Power BI můžete transformovat diagnostická data na vaše služby Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v tématu [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .
- **[Azure Storage](#stream-into-azure-storage)**:

  Data streamovaná do [Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md) umožňují archivovat velké množství diagnostické telemetrie za zlomek nákladů na předchozí dvě možnosti streamování.

Tato diagnostická telemetrie k jednomu z těchto cílů se dá použít k posouzení využití prostředků a statistikám provádění dotazů pro snazší monitorování výkonu.

![Diagram znázorňuje mnoho databází a databází SQL ve spravovaných instancích, které odesílají telemetrii do Azure Diagnostics, která předávají informace do Azure SQL Analytics, centra událostí a úložiště.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Povolení a konfigurace exportu diagnostické telemetrie pro streamování

Můžete povolit a spravovat metriky a protokolování diagnostiky telemetrie pomocí jedné z následujících metod:

- portál Azure
- PowerShell
- Azure CLI
- Rozhraní REST API služby Azure Monitor
- Šablona Azure Resource Manageru

> [!NOTE]
> Pokud chcete povolit streamování protokolu auditu z telemetrie zabezpečení, přečtěte si téma [nastavení auditování pro databázi](../../sql-database/sql-database-auditing.md#setup-auditing) a [protokoly auditování v Azure monitor protokoly a Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurace exportu diagnostické telemetrie do streamování

Pomocí nabídky **nastavení diagnostiky** v Azure Portal můžete povolit a nakonfigurovat streamování diagnostické telemetrie. Kromě toho můžete pomocí PowerShellu, Azure CLI, [REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)a [Správce prostředků šablony](../../azure-monitor/platform/diagnostic-settings-template.md) nakonfigurovat streamování diagnostické telemetrie. Můžete nastavit následující cíle pro streamování diagnostické telemetrie: Azure Storage, Azure Event Hubs a protokoly Azure Monitor.

> [!IMPORTANT]
> Ve výchozím nastavení není povolený export diagnostické telemetrie do streamování.

Vyberte jednu z následujících karet, kde najdete podrobné pokyny pro konfiguraci exportu diagnostické telemetrie do streamování v Azure Portal a pro skripty pro dosažení stejného prostředí pomocí PowerShellu a rozhraní příkazového řádku Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Elastické fondy v Azure SQL Database

Prostředek elastického fondu můžete nastavit tak, aby shromáždil následující diagnostickou telemetrii:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Elastický fond** | [Základní metriky](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) obsahují procento z EDTU/CPU, limit EDTU/procesor, procento fyzického načtení dat, procento zápisu protokolu, procento relací, procento pracovních podílů, úložiště, procento úložiště, limit úložiště a procento XTP úložiště. |

Pokud chcete nakonfigurovat streamování diagnostické telemetrie pro elastické fondy a databáze ve fondu, musíte každou samostatně nakonfigurovat samostatně:

- Povolit streamování diagnostické telemetrie pro elastický fond
- Povolit streamování diagnostické telemetrie pro každou databázi v elastickém fondu

Kontejner elastického fondu má svou vlastní telemetrii oddělenou od jednotlivých telemetrie jednotlivých databází ve fondu.

Pokud chcete povolit streamování diagnostické telemetrie pro prostředek elastického fondu, postupujte takto:

1. V Azure Portal přejít na prostředek **elastického fondu** .
2. Vyberte **nastavení diagnostiky**.
3. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.

   ![Povolit diagnostiku pro elastické fondy](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Zadejte název nastavení vlastní reference.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
6. V případě Log Analytics vyberte možnost **Konfigurovat** a vytvořit nový pracovní prostor výběrem možnosti **+ vytvořit nový pracovní prostor**nebo vyberte existující pracovní prostor.
7. Zaškrtněte políčko pro telemetrii diagnostiky elastického fondu: **základní** metriky.
   ![Konfigurace diagnostiky pro elastické fondy](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Vyberte **Uložit**.
9. Kromě toho nakonfigurujte streamování diagnostické telemetrie pro každou databázi v elastickém fondu, který chcete monitorovat, podle kroků popsaných v následující části.

> [!IMPORTANT]
> Kromě konfigurace diagnostické telemetrie pro elastický fond musíte také nakonfigurovat diagnostiku diagnostiky pro každou databázi v elastickém fondu.

### <a name="databases-in-azure-sql-database"></a>Databáze v Azure SQL Database

Můžete nastavit prostředek databáze pro shromáždění následující diagnostické telemetrie:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Jedna nebo ve fondu databáze** | [Základní metriky](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) obsahují procento DTU, použitou DTU, limit DTU, procento využití procesoru, procento čtení fyzických dat, procento zápisu protokolu, úspěšné/neúspěšné/blokované v případě připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento velikosti XTP a zablokování. |

Pokud chcete povolit streamování diagnostické telemetrie pro jednu nebo databázi ve fondu, postupujte takto:

1. Přejít na prostředek služby Azure **SQL Database** .
2. Vyberte **nastavení diagnostiky**.
3. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení. Můžete vytvořit až tři paralelní připojení ke streamování diagnostické telemetrie.
4. Pokud chcete nakonfigurovat paralelní streamování diagnostických dat na více prostředků, vyberte **Přidat nastavení diagnostiky** .

   ![Povolit diagnostiku pro databáze s jednou a fondem](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Zadejte název nastavení vlastní reference.
6. Vyberte cílový prostředek pro data diagnostiky streamování: **archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
7. V případě standardního prostředí monitorování založeného na událostech zaškrtněte následující políčka pro telemetrii protokolu diagnostiky databáze: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **chyby**, **DatabaseWaitStatistics**, **časové limity**, **bloky**a **zablokování**.
8. V případě pokročilých možností monitorování na základě minut zaškrtněte políčko pro **základní** metriky.

   ![Konfigurace diagnostiky pro Azure SQL Database](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Vyberte **Uložit**.
10. Opakujte tyto kroky pro každou databázi, kterou chcete monitorovat.

> [!TIP]
> Opakujte tyto kroky pro každou jednotlivou a sdruženou databázi, kterou chcete monitorovat.

### <a name="instances-in-azure-sql-managed-instance"></a>Instance ve spravované instanci Azure SQL

Prostředek spravované instance můžete nastavit tak, aby shromáždil následující diagnostickou telemetrii:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Spravovaná instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) obsahuje počet virtuální jádra, průměrné procento procesoru, vstupně-výstupní požadavky, přečtené/zapsané bajty, vyhrazený prostor úložiště a využitý prostor úložiště. |

Chcete-li nakonfigurovat streamování diagnostické telemetrie pro databáze spravované instance a instance, bude nutné každou nakonfigurovat samostatně:

- Povolit streamování diagnostické telemetrie pro spravovanou instanci
- Povolit streamování diagnostické telemetrie pro každou databázi instance

Kontejner spravované instance má svou vlastní telemetrii oddělenou od telemetrie každé instance databáze.

Pokud chcete povolit streamování diagnostické telemetrie pro prostředek spravované instance, postupujte takto:

1. V Azure Portal přejít na prostředek **spravované instance** .
2. Vyberte **nastavení diagnostiky**.
3. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.

   ![Povolit diagnostiku pro spravovanou instanci](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Zadejte název nastavení vlastní reference.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
6. V případě Log Analytics vyberte **Konfigurovat** a vytvořte nový pracovní prostor tak, že vyberete **+ vytvořit nový pracovní prostor**nebo použijete existující pracovní prostor.
7. Zaškrtněte políčko instance telemetrie diagnostiky: **ResourceUsageStats**.

   ![Konfigurace diagnostiky pro spravovanou instanci](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Vyberte **Uložit**.
9. Kromě toho nakonfigurujte streamování diagnostické telemetrie pro každou databázi instancí v rámci spravované instance, kterou chcete monitorovat, podle postupu popsaného v následující části.

> [!IMPORTANT]
> Kromě konfigurace diagnostické telemetrie pro spravovanou instanci musíte také nakonfigurovat diagnostiku diagnostiky pro každou databázi instance.

### <a name="databases-in-azure-sql-managed-instance"></a>Databáze ve spravované instanci Azure SQL

Můžete nastavit prostředek databáze instance pro shromáždění následující diagnostické telemetrie:

| Prostředek | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Databáze instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) obsahuje počet virtuální jádra, průměrné procento procesoru, vstupně-výstupní požadavky, přečtené/zapsané bajty, vyhrazený prostor úložiště a využitý prostor úložiště. |

Pokud chcete povolit streamování diagnostické telemetrie pro instanci databáze, postupujte takto:

1. Přejít na **instanci databázového** prostředku v rámci spravované instance.
2. Vyberte **nastavení diagnostiky**.
3. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.
   - Můžete vytvořit až tři (3) paralelní připojení ke streamování diagnostické telemetrie.
   - Vyberte **+ Přidat nastavení diagnostiky** a nakonfigurujte paralelní streamování diagnostických dat na více prostředků.

   ![Povolit diagnostiku pro databáze instancí](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Zadejte název nastavení vlastní reference.
5. Vyberte cílový prostředek pro data diagnostiky streamování: **archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
6. Zaškrtněte políčka pro telemetrii diagnostiky databáze: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**a **Errors**.
   ![Konfigurace diagnostiky pro databáze instancí](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Vyberte **Uložit**.
8. Opakujte tyto kroky pro každou databázi instance, kterou chcete monitorovat.

> [!TIP]
> Opakujte tyto kroky pro každou databázi instance, kterou chcete monitorovat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Metriky a protokolování diagnostiky můžete povolit pomocí prostředí PowerShell.

- Pokud chcete v účtu úložiště povolit ukládání metrik a protokolů prostředků, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  ID účtu úložiště je ID prostředku cílového účtu úložiště.

- Pokud chcete povolit streamování metrik a protokolů prostředků do centra událostí, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Pokud chcete povolit odesílání metrik a protokolů prostředků do Log Analytics pracovního prostoru, použijte tento příkaz:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

**Konfigurace více prostředků Azure**

Pokud chcete podporovat víc předplatných, použijte skript PowerShellu z části [Povolení protokolování metrik prostředků Azure pomocí PowerShellu](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Zadejte ID prostředku pracovního prostoru \<$WSID\> jako parametr při spouštění skriptu `Enable-AzureRMDiagnostics.ps1` pro odesílání diagnostických dat z několika prostředků do pracovního prostoru.

- Chcete-li získat ID \<$WSID\> cílového prostoru pro diagnostická data, použijte následující skript:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Nahraďte \<subID\> ID předplatného, \<RG_NAME\> názvem skupiny prostředků a \<WS_NAME\> názvem pracovního prostoru.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Metriky a protokolování diagnostiky můžete povolit pomocí rozhraní příkazového řádku Azure CLI.

> [!IMPORTANT]
> Skripty pro povolení protokolování diagnostiky se podporují pro Azure CLI v 1.0. Rozhraní příkazového řádku Azure CLI v 2.0 se v tuto chvíli nepodporuje.

- Pokud chcete povolit úložiště metrik a protokolů prostředků v účtu úložiště, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  ID účtu úložiště je ID prostředku cílového účtu úložiště.

- Pokud chcete povolit streamování metrik a protokolů prostředků do centra událostí, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  ID pravidla Service Bus je řetězec s tímto formátem:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Pokud chcete povolit odesílání metrik a protokolů prostředků do Log Analytics pracovního prostoru, použijte tento příkaz:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

---

## <a name="stream-into-sql-analytics"></a>Streamování do SQL Analytics

Azure SQL Database a metriky spravované instance služby Azure SQL a protokoly prostředků, které jsou streamované do pracovního prostoru Log Analytics, můžou být využívány Azure SQL Analytics. Azure SQL Analytics je cloudové řešení, které monitoruje výkon izolovaných databází, elastických fondů a databází ve fondu a spravovaných instancí a databází instancí ve velkém rozsahu a v různých předplatných. Může vám to usnadnit shromažďování a vizualizace metrik výkonu a obsahuje integrované inteligentní funkce pro řešení potíží s výkonem.

![Přehled Azure SQL Analytics](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Přehled instalace

Kolekci databází a kolekcí databází můžete monitorovat pomocí Azure SQL Analytics provedením následujících kroků:

1. Vytvořte Azure SQL Analytics řešení z Azure Marketplace.
2. V řešení vytvořte pracovní prostor Log Analytics.
3. Nakonfigurujte databáze pro streamování diagnostické telemetrie do pracovního prostoru.

Můžete nakonfigurovat export streamování této diagnostické telemetrie pomocí možnosti zabudované **Odeslat do Log Analytics** na kartě nastavení diagnostiky v Azure Portal. Můžete také povolit streamování do Log Analytics pracovního prostoru pomocí nastavení diagnostiky prostřednictvím [rutin PowerShellu](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), rozhraní příkazového [řádku Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), [Azure monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)nebo [šablon Správce prostředků](../../azure-monitor/platform/diagnostic-settings-template.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Vytvoření prostředku Azure SQL Analytics

1. Vyhledejte Azure SQL Analytics v Azure Marketplace a vyberte ji.

   ![Hledat Azure SQL Analytics na portálu](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Na obrazovce s přehledem řešení vyberte **vytvořit** .

3. Do formuláře Azure SQL Analytics zadejte další požadované informace: název pracovního prostoru, předplatné, skupina prostředků, umístění a cenová úroveň.

   ![Konfigurace Azure SQL Analytics na portálu](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Kliknutím na **OK** potvrďte a pak vyberte **vytvořit**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurace prostředku pro záznam metrik a protokolů prostředků

Je potřeba samostatně nakonfigurovat streamování diagnostiky pro jednotlivé a sdružené databáze, elastické fondy, spravované instance a databáze instancí. Nejjednodušší způsob, jak nakonfigurovat, kde je metrika záznamů prostředků, je pomocí Azure Portal. Podrobný postup najdete v tématu [Konfigurace exportu diagnostické telemetrie s datovým proudem](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Použití Azure SQL Analytics pro monitorování a upozorňování

K zobrazení databázových prostředků můžete použít SQL Analytics jako hierarchický řídicí panel.

- Další informace o použití Azure SQL Analytics najdete v tématu [monitorování pomocí SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Informace o nastavení výstrah pro v nástroji SQL Analytics najdete v tématu [vytváření výstrah pro databázi, elastické fondy a spravované instance](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Můžete streamovat Azure SQL Database a metriky spravované instance Azure SQL a protokoly prostředků do Event Hubs pomocí integrovaného **streamu do možnosti centra událostí** v Azure Portal. ID pravidla Service Bus můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin PowerShellu, rozhraní příkazového řádku Azure nebo REST API Azure Monitor. Ujistěte se, že centrum událostí je ve stejné oblasti jako databáze a Server.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Co dělat s metrikami a protokoly prostředků v Event Hubs

Jakmile se vybraná data streamují do Event Hubs, budete mít jeden krok blíž k povolení rozšířených scénářů monitorování. Event Hubs funguje jako přední dveře pro kanál událostí. Po shromáždění dat do centra událostí je možné je transformovat a ukládat pomocí poskytovatele analýz v reálném čase nebo adaptéru úložiště. Event Hubs odpojí produkci proudu událostí od spotřeby těchto událostí. Tímto způsobem můžou příjemci události získat přístup k událostem podle vlastního plánu. Další informace o Event Hubs najdete v tématech:

- [Co je Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

V Event Hubs můžete použít streamované metriky pro:

- **Zobrazení stavu služby streamování dat Hot-Path do Power BI**

  Pomocí Event Hubs, Stream Analytics a Power BI můžete snadno transformovat metriky a diagnostická data na vaše služby Azure téměř v reálném čase. Přehled nastavení centra událostí, zpracování dat pomocí Stream Analytics a použití Power BI jako výstupu najdete v tématu [Stream Analytics a Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Streamování protokolů a datových proudů telemetrie od třetích stran**

  Pomocí služby Event Hubs streaming můžete získat metriky a protokoly prostředků do různých řešení monitorování a analýzy protokolů třetích stran.

- **Sestavení vlastní telemetrie a protokolovací platformy**

  Už máte vlastní platformu telemetrie nebo se domnívají, že ji vytváříte? Vysoce škálovatelná povaha publikování a odběru Event Hubs umožňuje flexibilní přijímání metrik a protokolů prostředků. Přečtěte si [průvodce Event Hubs Dan na používání v globální platformě telemetrie](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Streamování do Azure Storage

Metriky a protokoly prostředků můžete ukládat v Azure Storage pomocí integrované **archivace na účet úložiště** v Azure Portal. Úložiště můžete taky povolit pomocí nastavení diagnostiky prostřednictvím rutin PowerShellu, rozhraní příkazového řádku Azure nebo Azure Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schéma metrik a protokolů prostředků v účtu úložiště

Po nastavení metrik a shromažďování protokolů prostředků se kontejner úložiště vytvoří v účtu úložiště, který jste vybrali, když jsou k dispozici první řádky dat. Struktura objektů BLOB je:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Nebo jednoduše:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například název objektu BLOB pro základní metriky může být:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Název objektu BLOB pro ukládání dat z elastického fondu vypadá takto:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Zásady uchovávání dat a ceny

Pokud vyberete Event Hubs nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraní data, která jsou starší než vybrané časové období. Pokud zadáte Log Analytics, zásady uchovávání informací závisí na vybrané cenové úrovni. V takovém případě může dodané volné jednotky příjmu dat umožnit bezplatné monitorování několika databází každý měsíc. Jakákoli spotřeba diagnostické telemetrie nad rámec volných jednotek může mít za následek náklady.

> [!IMPORTANT]
> Aktivní databáze s těžšími úlohami ingestují více dat než nečinné databáze. Další informace najdete v tématu [ceny Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pokud používáte Azure SQL Analytics, můžete monitorovat spotřebu příjmu dat tak, že v navigační nabídce Azure SQL Analytics vyberete **pracovní prostor OMS** a pak vyberete **využití** a **Odhadované náklady**.

## <a name="metrics-and-logs-available"></a>Dostupné metriky a protokoly

V této části článku jsou popsány dostupné telemetrie pro jednotlivé databáze, databáze ve fondu, elastické fondy, spravovaná instance a databáze instancí. Shromážděná telemetrie monitorování v rámci služby SQL Analytics lze použít pro vlastní analýzu a vývoj aplikací pomocí jazyka [Azure Monitorch dotazů protokolu](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) .

### <a name="basic-metrics"></a>Základní metriky

Podrobnosti o základních metrikách podle prostředků najdete v následujících tabulkách.

> [!NOTE]
> Možnost základních metrik se dřív jmenovala jako všechny metriky. Změna byla provedena pouze pro pojmenování a na monitorované metriky se nezměnily žádné změny. Tato změna byla zahájena, aby bylo možné v budoucnu zavést další kategorie metrik.

#### <a name="basic-metrics-for-elastic-pools"></a>Základní metriky pro elastické fondy

|**Prostředek**|**Metriky**|
|---|---|
|Elastický fond|procento eDTU, využité eDTU, limit pro eDTU, procento využití procesoru, procento čtení fyzických dat, procentuální hodnota zápisu protokolu, procento relací, procento pracovních procesů, úložiště, procento úložiště, omezení úložiště, procento XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Základní metriky pro databáze s jednou a fondem

|**Prostředek**|**Metriky**|
|---|---|
|Databáze s jednou a fondem|Procento DTU, využité DTU, limit DTU, procento využití procesoru, procento čtení fyzických dat, procentuální hodnota zápisu protokolu, úspěšná/neúspěšná/zablokovaná připojeními brány firewall, procento relací, procento pracovních procesů, úložiště, procentuální hodnota úložiště, procento velikosti XTP a zablokování |

### <a name="advanced-metrics"></a>Pokročilé metriky

Podrobnosti o rozšířených metrikách najdete v následující tabulce.

|**Metrika**|**Zobrazovaný název metriky**|**Popis**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Základní procento procesu SQL|Procento využití procesoru pro proces SQL, jak je měřené operačním systémem.|
|sqlserver_process_memory_percent<sup>1</sup> |Procentuální hodnota paměti procesu SQL|Procento využití paměti pro proces SQL, jak je měřené operačním systémem.|
|tempdb_data_size<sup>2</sup>| Velikost datového souboru tempdb v kilobajtech |Velikost datového souboru tempdb v kilobajtech|
|tempdb_log_size<sup>2</sup>| Velikost souboru protokolu tempdb v kilobajtech |Velikost souboru protokolu tempdb v kilobajtech|
|tempdb_log_used_percent<sup>2</sup>| Použit protokol tempdb v procentech |Byl použit protokol tempdb Percent.|

<sup>1</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU.

<sup>2</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU. Tato metrika není aktuálně k dispozici pro databáze nebo datové sklady ve škálovatelném měřítku.

### <a name="basic-logs"></a>Základní protokoly

Podrobnosti o telemetrie dostupných pro všechny protokoly jsou popsány v následujících tabulkách. Další informace najdete v tématu [podporovaná diagnostická telemetrie](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Statistika využití prostředků pro spravované instance

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure|
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: ResourceUsageStats |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Always: MANAGEDINSTANCES |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název spravované instance |
|ResourceId|Identifikátor URI prostředku |
|SKU_s|SKU produktu spravované instance SQL |
|virtual_core_count_s|Počet dostupných virtuální jádra |
|avg_cpu_percent_s|Průměrné procento procesoru |
|reserved_storage_mb_s|Rezervovaná kapacita úložiště na spravované instanci |
|storage_space_used_mb_s|Použito úložiště na spravované instanci |
|io_requests_s|Počet IOPS |
|io_bytes_read_s|Přečtené bajty IOPS |
|io_bytes_written_s|Zapsané bajty IOPS |

#### <a name="query-store-runtime-statistics"></a>Statistiky za běhu úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: QueryStoreRuntimeStatistics |
|OperationName|Název operace Always: QueryStoreRuntimeStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|query_hash_s|Hodnota hash dotazu |
|query_plan_hash_s|Hodnota hash plánu dotazů |
|statement_sql_handle_s|Obslužná rutina příkazu SQL |
|interval_start_time_d|Počáteční hodnota DateTimeOffset intervalu v počtu taktů od 1900-1-1 |
|interval_end_time_d|Koncový DateTimeOffset intervalu v počtu taktů od 1900-1-1 |
|logical_io_writes_d|Celkový počet logických zápisů v/v |
|max_logical_io_writes_d|Maximální počet logických zápisů v/v za spuštění |
|physical_io_reads_d|Celkový počet fyzických čtení v/v |
|max_physical_io_reads_d|Maximální počet logických čtení v/v za spuštění |
|logical_io_reads_d|Celkový počet logických čtení v/v |
|max_logical_io_reads_d|Maximální počet logických čtení v/v za spuštění |
|execution_type_d|Typ spuštění |
|count_executions_d|Počet spuštění dotazu |
|cpu_time_d|Celkový čas procesoru spotřebovaný dotazem v mikrosekundách |
|max_cpu_time_d|Maximální čas procesoru u jednoho spuštění v mikrosekundách |
|dop_d|Součet stupňů paralelismu |
|max_dop_d|Maximální stupeň paralelismu používaných pro jedno spuštění |
|rowcount_d|Celkový počet vrácených řádků |
|max_rowcount_d|Maximální počet řádků vrácených v jednom spuštění |
|query_max_used_memory_d|Celková velikost paměti využitá v KB |
|max_query_max_used_memory_d|Maximální velikost paměti, kterou používá jedno spuštění v KB |
|duration_d|Celková doba provádění v mikrosekundách |
|max_duration_d|Maximální doba provádění jednoho spuštění |
|num_physical_io_reads_d|Celkový počet fyzických čtení |
|max_num_physical_io_reads_d|Maximální počet fyzických čtení na jedno spuštění |
|log_bytes_used_d|Celková velikost použitých bajtů protokolu |
|max_log_bytes_used_d|Maximální množství bajtů protokolu použitých na spuštění |
|query_id_d|ID dotazu v úložišti dotazů |
|plan_id_d|ID plánu v úložišti dotazů |

Další informace o [datech statistiky modulu runtime úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Statistika čekání na úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: QueryStoreWaitStatistics |
|OperationName|Název operace Always: QueryStoreWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_category_s|Kategorie čekání |
|is_parameterizable_s|Je dotaz parametrizovat |
|statement_type_s|Typ příkazu |
|statement_key_hash_s|Hodnota hash klíče příkazu |
|exec_type_d|Typ spuštění |
|total_query_wait_time_ms_d|Celková čekací doba dotazu v konkrétní kategorii čekání |
|max_query_wait_time_ms_d|Maximální čekací doba dotazu v individuálním spuštění v konkrétní kategorii čekání |
|query_param_type_d|0 |
|query_hash_s|Hodnota hash dotazu v úložišti dotazů |
|query_plan_hash_s|Hodnota hash plánu dotazů v úložišti dotazů |
|statement_sql_handle_s|Popisovač příkazu v úložišti dotazů |
|interval_start_time_d|Počáteční hodnota DateTimeOffset intervalu v počtu taktů od 1900-1-1 |
|interval_end_time_d|Koncový DateTimeOffset intervalu v počtu taktů od 1900-1-1 |
|count_executions_d|Počet spuštění dotazu |
|query_id_d|ID dotazu v úložišti dotazů |
|plan_id_d|ID plánu v úložišti dotazů |

Další informace o [datech statistiky čekání na úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Datová sada chyb

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Vždycky: Chyby |
|OperationName|Název operace Always: ErrorEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|Zpráva|Chybová zpráva v prostém textu |
|user_defined_b|Je chybově definovaný bit uživatelem |
|error_number_d|Kód chyby |
|Závažnost|Závažnost chyby |
|state_d|Stav chyby |
|query_hash_s|Hodnota hash dotazu neúspěšného dotazu, pokud je k dispozici |
|query_plan_hash_s|Hodnota hash plánu dotazu neúspěšného dotazu, je-li k dispozici |

Přečtěte si další informace o [chybových zprávách SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Datová sada statistiky čekání databáze

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: DatabaseWaitStatistics |
|OperationName|Název operace Always: DatabaseWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_type_s|Název typu čekání |
|start_utc_date_t [UTC]|Čas zahájení měřeného období |
|end_utc_date_t [UTC]|Čas ukončení měřeného období |
|delta_max_wait_time_ms_d|Maximální doba čekání na spuštění |
|delta_signal_wait_time_ms_d|Doba čekání na celkový počet signálů |
|delta_wait_time_ms_d|Celková doba čekání v období |
|delta_waiting_tasks_count_d|Počet čekajících úloh |

Přečtěte si další informace o [statistice čekání databáze](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Datová sada časových limitů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Vždycky: časové limity |
|OperationName|Název operace Always: TimeoutEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|error_state_d|Chybový kód stavu |
|query_hash_s|Hodnota hash dotazu, je-li k dispozici |
|query_plan_hash_s|Hodnota hash plánu dotazu, je-li k dispozici |

#### <a name="blockings-dataset"></a>Datová sada bloků

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: bloky |
|OperationName|Název operace Always: BlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|lock_mode_s|Režim zámku použitý dotazem |
|resource_owner_type_s|Vlastník zámku |
|blocked_process_filtered_s|KÓD XML sestavy blokovaného procesu |
|duration_d|Doba trvání zámku v mikrosekundách |

#### <a name="deadlocks-dataset"></a>Datová sada zablokování

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC] |Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Vždycky: zablokování |
|OperationName|Název operace Always: DeadlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|deadlock_xml_s|XML sestavy zablokování |

#### <a name="automatic-tuning-dataset"></a>Datová sada automatického ladění

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Vždycky: Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Always: MICROSOFT. SQL |
|Kategorie|Název kategorie Always: AutomaticTuning |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku Vždy: servery/databáze |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|LogicalDatabaseName_s|Název databáze |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|RecommendationHash_s|Jedinečná hodnota hash pro automatické ladění – doporučení |
|OptionName_s|Operace automatického ladění |
|Schema_s|Schéma databáze |
|Table_s|Ovlivněná tabulka |
|IndexName_s|Název indexu |
|IndexColumns_s|Název sloupce |
|IncludedColumns_s|Zahrnuté sloupce |
|EstimatedImpact_s|Odhadovaný dopad automatického ladění s doporučením JSON |
|Event_s|Typ události automatického ladění |
|Timestamp_t|Poslední aktualizované časové razítko |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights datová sada

Přečtěte si další informace o [formátu protokolu Intelligent Insights](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak povolit protokolování a pochopit kategorie metrik a protokolů podporované různými službami Azure, najdete v těchto tématech:

- [Přehled metrik v Microsoft Azure](../../azure-monitor/platform/data-platform.md)
- [Přehled protokolů platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)

Pokud se chcete dozvědět o Event Hubs, přečtěte si:

- [Co je služba Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Informace o tom, jak nastavit výstrahy na základě telemetrie z Log Analytics, najdete v těchto tématech:

- [Vytváření výstrah pro Azure SQL Database a Azure SQL Managed instance](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
