---
title: Azure SQL Database metriky a protokolování diagnostiky | Microsoft Docs
description: Naučte se, jak povolit diagnostiku v Azure SQL Database k ukládání informací o využití prostředků a statistikách spouštění dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/21/2019
ms.openlocfilehash: 1b35533eeb4c4a364588dbea11f74e8d6b76df3b
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998229"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metriky a protokolování diagnostiky

V tomto tématu se dozvíte, jak nakonfigurovat protokolování telemetrie diagnostiky pro Azure SQL Database přes Azure Portal, PowerShell, Azure CLI, Azure Monitor REST API a šablonu Azure Resource Manager. Tato diagnostika se dá použít k posouzení využití prostředků a statistik provádění dotazů.

Izolované databáze, sdružené databáze v elastických fondech a databáze instancí ve spravované instanci mohou streamovat metriky a diagnostické protokoly pro snazší monitorování výkonu. Databázi můžete nakonfigurovat tak, aby předávala využití prostředků, pracovní procesy a relace a připojení k jednomu z následujících prostředků Azure:

- **Azure SQL Analytics**: Získejte inteligentní monitorování vašich databází SQL Azure, včetně sestav o výkonu, výstrah a doporučení pro zmírnění rizik.
- **Azure Event Hubs**: pro integraci SQL Database telemetrie s vlastními řešeními monitorování nebo s aktivními kanály.
- **Azure Storage**: pro archivaci obrovského množství telemetrie za zlomek ceny.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

Další informace o metrikách a kategoriích protokolů podporovaných různými službami Azure najdete v těchto tématech:

- [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Přehled diagnostických protokolů Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Tento článek poskytuje pokyny, které vám pomůžou povolit telemetrii diagnostiky pro databáze SQL Azure, elastické fondy a spravované instance. Může vám také porozumět tomu, jak nakonfigurovat Azure SQL Analytics jako monitorovací nástroj pro zobrazení telemetrie diagnostiky databáze.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Povolit protokolování telemetrie diagnostiky

Můžete povolit a spravovat metriky a protokolování telemetrie diagnostiky pomocí jedné z následujících metod:

- portál Azure
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Šablona Azure Resource Manageru

Když povolíte metriky a protokolování diagnostiky, musíte zadat cíl prostředku Azure pro shromažďování telemetrie diagnostiky. K dispozici jsou tyto možnosti:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Můžete zřídit nový prostředek Azure nebo vybrat existující prostředek. Po výběru prostředku pomocí možnosti **nastavení diagnostiky** určete, která data se mají shromažďovat.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Podporované protokolování diagnostiky pro databáze SQL Azure a databáze instancí

Povolit metriky a protokolování diagnostiky pro databáze SQL – nejsou ve výchozím nastavení povolené.

Můžete nastavit databáze Azure SQL a databáze instancí pro shromažďování následujících diagnostických telemetrie:

| Monitorování telemetrie pro databáze | Podpora jedné databáze a databáze ve fondu | Podpora databáze instancí |
| :------------------- | ----- | ----- |
| [Základní metriky](#basic-metrics): Obsahuje procento DTU/CPU, DTU/procesor, procento fyzického načtení dat, procento zápisu protokolu, úspěšné/neúspěšné/blokované připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště a procento XTP úložiště. | Ano | Ne |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Obsahuje informace o statistikách modulu runtime dotazu, jako je například využití procesoru a statistika doby trvání dotazu. | Ano | Ano |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Obsahuje informace o statistice čekání na dotaz (co vaše dotazy čekaly), jako je například CPU, protokol a UZAMYKÁNÍ. | Ano | Ano |
| [Chyby](#errors-dataset): Obsahuje informace o chybách SQL v databázi. | Ano | Ano |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Obsahuje informace o tom, kolik času databáze strávila čekáním na různé typy čekání. | Ano | Ne |
| [Časové limity](#time-outs-dataset): Obsahuje informace o časových limitech v databázi. | Ano | Ne |
| [Bloky](#blockings-dataset): Obsahuje informace o blokujících událostech v databázi. | Ano | Ne |
| [](#deadlocks-dataset)Zablokování: Obsahuje informace o událostech zablokování v databázi. | Ano | Ne |
| [AutomaticTuning](#automatic-tuning-dataset): Obsahuje informace o automatickém ladění doporučení pro databázi. | Ano | Ne |
| [SQLInsights](#intelligent-insights-dataset): Obsahuje Intelligent Insights k výkonu databáze. Další informace najdete v tématu [Intelligent Insights](sql-database-intelligent-insights.md). | Ano | Ano |

> [!IMPORTANT]
> Elastické fondy a spravované instance mají svou vlastní samostatnou diagnostické telemetrie z databází, které obsahují. Je důležité si uvědomit, že se pro každý z těchto prostředků nakonfiguruje telemetrie diagnostiky samostatně, jak je uvedeno níže.

> [!NOTE]
> Protokoly auditu zabezpečení a protokoly SQLSecurityAuditEvents nelze povolit z nastavení diagnostiky databáze (i když se zobrazuje na obrazovce). Pokud chcete povolit streamování protokolů auditu, přečtěte si téma [nastavení auditování pro vaši databázi](sql-database-auditing.md#subheading-2)a [protokoly auditování v Azure monitor protokoly a Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>portál Azure

V nabídce **nastavení diagnostiky** můžete pro každou jednotlivou, sdruženou nebo instancinou databázi v Azure Portal nakonfigurovat streamování diagnostické telemetrie. Kromě toho je možné pro kontejnery databáze samostatně nakonfigurovat také diagnostiku telemetrie: elastické fondy a spravované instance. Můžete nastavit následující cíle pro streamování diagnostické telemetrie: Protokoly Azure Storage, Azure Event Hubs a Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurace streamování diagnostické telemetrie pro elastické fondy

   ![Ikona elastického fondu](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Prostředek elastického fondu můžete nastavit tak, aby shromáždil následující telemetrii diagnostiky:

| Resource | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Elastický fond** | [Základní metriky](sql-database-metrics-diag-logging.md#basic-metrics) obsahují procento z EDTU/CPU, limit EDTU/procesor, procento fyzického načtení dat, procento zápisu protokolu, procento relací, procento pracovních podílů, úložiště, procento úložiště, limit úložiště a procento XTP úložiště. |

Chcete-li nakonfigurovat streamování diagnostiky diagnostiky pro elastické fondy a databáze v elastických fondech, bude nutné samostatně nakonfigurovat **obě** z těchto možností:

- Povolí streamování diagnostiky pro elastický fond **a**
- Povolit streamování diagnostiky pro každou databázi v elastickém fondu

Důvodem je to, že elastický fond je databázový kontejner se svou vlastní telemetrie, který je oddělený od jednotlivých telemetrie databáze.

Pokud chcete povolit streamování diagnostické telemetrie pro prostředek elastického fondu, postupujte takto:

1. V Azure Portal přejít na prostředek **elastického fondu** .
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.

   ![Povolit diagnostiku pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Zadejte název nastavení vlastní reference.
1. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
1. V případě Log Analytics vyberte možnost **Konfigurovat** a vytvořit nový pracovní prostor výběrem možnosti **+ vytvořit nový pracovní prostor**nebo vyberte existující pracovní prostor.
1. Zaškrtněte políčko pro telemetrii diagnostiky elastického fondu: **Základní** metriky.
   ![Konfigurace diagnostiky pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Vyberte **Uložit**.
1. Kromě toho nakonfigurujte streamování diagnostiky pro každou databázi v elastickém fondu, který chcete monitorovat pomocí následujících kroků popsaných v následující části.

> [!IMPORTANT]
> Kromě konfigurace telemetrie diagnostiky pro elastický fond musíte také nakonfigurovat telemetrii diagnostiky pro každou databázi v elastickém fondu, jak je uvedeno níže.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Konfigurace streamování diagnostické telemetrie pro izolovanou databázi nebo databáze v elastickém fondu

   ![Ikona SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Pokud chcete povolit streamování diagnostické telemetrie pro databáze s jednou nebo ve fondu, postupujte takto:

1. Přejít na prostředek služby Azure **SQL Database** .
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.
   - Můžete vytvořit až tři paralelní připojení ke streamování diagnostiky diagnostiky.
   - Vyberte **+ Přidat nastavení diagnostiky** a nakonfigurujte paralelní streamování diagnostických dat na více prostředků.

   ![Povolit diagnostiku pro databáze s jedním, sdruženým nebo instancí](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Zadejte název nastavení vlastní reference.
1. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
1. U standardních možností monitorování založeného na událostech zaškrtněte následující políčka pro telemetrii protokolu diagnostiky databáze: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **chyby**, **DatabaseWaitStatistics**, **časové limity**, **bloky**a zablokování.
1. V případě pokročilých možností monitorování na základě minut zaškrtněte políčko pro **základní** metriky.
   ![Konfigurace diagnostiky pro databáze s jedním, sdruženým nebo instancí](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Vyberte **Uložit**.
1. Opakujte tyto kroky pro každou databázi, kterou chcete monitorovat.

> [!NOTE]
> Protokoly auditu zabezpečení a protokoly SQLSecurityAuditEvents se nedají povolit z nastavení diagnostiky databáze (i když se zobrazují na obrazovce). Pokud chcete povolit streamování protokolů auditu, přečtěte si téma [nastavení auditování pro vaši databázi](sql-database-auditing.md#subheading-2)a [protokoly auditování v Azure monitor protokoly a Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Opakujte tyto kroky pro každý Azure SQL Database, který chcete monitorovat.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Konfigurace streamování diagnostické telemetrie pro spravované instance

   ![Ikona spravované instance](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Prostředek spravované instance můžete nastavit tak, aby shromáždil následující telemetrii diagnostiky:

| Resource | Monitorování telemetrie |
| :------------------- | ------------------- |
| **Spravovaná instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) obsahuje počet virtuální jádra, průměrné procento procesoru, vstupně-výstupní požadavky, přečtené/zapsané bajty, vyhrazený prostor úložiště a využitý prostor úložiště. |

Chcete-li nakonfigurovat streamování diagnostické telemetrie pro databáze spravované instance a instance, bude nutné samostatně nakonfigurovat **obě** následující:

- Povolte streamování diagnostické telemetrie pro spravovanou instanci **a**
- Povolit streamování diagnostiky pro každou databázi instance

Je to proto, že spravovaná instance je databázový kontejner se svou vlastní telemetrie, oddělený od telemetrie databáze jednotlivých instancí.

Chcete-li povolit streamování diagnostické telemetrie pro prostředek spravované instance, postupujte takto:

1. V Azure Portal přejít na prostředek **spravované instance** .
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.

   ![Povolit diagnostiku pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Zadejte název nastavení vlastní reference.
1. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
1. V případě Log Analytics vyberte **Konfigurovat** a vytvořte nový pracovní prostor tak, že vyberete **+ vytvořit nový pracovní prostor**nebo použijete existující pracovní prostor.
1. Zaškrtněte políčko pro telemetrii diagnostiky instance: **ResourceUsageStats**.
   ![Konfigurace diagnostiky pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Vyberte **Uložit**.
1. Kromě toho nakonfigurujte streamování diagnostiky pro každou databázi instancí v rámci spravované instance, kterou chcete monitorovat, podle postupu popsaného v následující části.

> [!IMPORTANT]
> Kromě konfigurace telemetrie diagnostiky pro spravovanou instanci musíte také nakonfigurovat telemetrii diagnostiky pro každou databázi instance, jak je uvedeno níže.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Konfigurace streamování diagnostické telemetrie pro databáze instancí

   ![Ikona databáze instancí v Managed instance](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Pokud chcete povolit streamování diagnostické telemetrie pro databáze instancí, postupujte podle těchto kroků:

1. Přejít na **instanci databázového** prostředku v rámci spravované instance.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** , pokud neexistuje žádné předchozí nastavení, nebo vyberte **Upravit nastavení** a upravte předchozí nastavení.
   - Můžete vytvořit až tři (3) paralelní připojení ke streamování diagnostiky diagnostiky.
   - Vyberte **+ Přidat nastavení diagnostiky** a nakonfigurujte paralelní streamování diagnostických dat na více prostředků.

   ![Povolit diagnostiku pro databáze instancí](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Zadejte název nastavení vlastní reference.
1. Vyberte cílový prostředek pro data diagnostiky streamování: **Archivujte do účtu úložiště**, **streamujte do centra událostí**nebo **Log Analytics odeslat**.
1. Zaškrtněte políčka pro telemetrii diagnostiky databáze: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** a **Errors**.
   ![Konfigurace diagnostiky pro databáze instancí](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Vyberte **Uložit**.
1. Opakujte tyto kroky pro každou databázi instance, kterou chcete monitorovat.

> [!TIP]
> Opakujte tyto kroky pro každou databázi instance, kterou chcete monitorovat.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Metriky a protokolování diagnostiky můžete povolit pomocí prostředí PowerShell.

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku cílového účtu úložiště.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Povolení odesílání protokolů diagnostiky k pracovnímu prostoru Log Analytics, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurace více prostředků Azure

Pokud chcete podporovat víc předplatných, použijte skript PowerShellu z části [Povolení protokolování metrik prostředků Azure pomocí PowerShellu](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Zadejte ID \<prostředku pracovního prostoru $WSID\> jako parametr při spouštění skriptu `Enable-AzureRMDiagnostics.ps1` pro odesílání diagnostických dat z několika prostředků do pracovního prostoru.

- Chcete-li získat ID \<pracovního\> prostoru $WSID cíle pro diagnostická data, použijte následující skript:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   \<Nahraďte\> subid ID předplatného \<,\> RG_NAME s názvem skupiny prostředků a \<WS_NAME\> názvem pracovního prostoru.

### <a name="azure-cli"></a>Azure CLI

Metriky a protokolování diagnostiky můžete povolit pomocí rozhraní příkazového řádku Azure CLI.

> [!NOTE]
> Skripty pro povolení protokolování diagnostiky se podporují pro Azure CLI v 1.0. Počítejte s tím, že rozhraní příkazového řádku v 2.0 není v současnosti podporováno.

- Pokud chcete povolit úložiště diagnostických protokolů v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ID účtu úložiště je ID prostředku cílového účtu úložiště.

- Pokud chcete povolit streamování diagnostických protokolů do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   ID pravidla Service Bus je řetězec s tímto formátem:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru Log Analytics, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Přečtěte si o tom, jak [změnit nastavení diagnostiky pomocí REST API Azure monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Přečtěte si informace o tom, jak [Povolit nastavení diagnostiky při vytváření prostředků pomocí šablony Správce prostředků](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Streamování do Azure SQL Analytics

Azure SQL Analytics je cloudové řešení, které monitoruje výkon databází SQL Azure, elastických fondů a spravovaných instancí ve velkém rozsahu a mezi několika předplatnými. Může vám to usnadnit shromažďování a vizualizace Azure SQL Database metriky výkonu a obsahuje integrované inteligentní funkce pro řešení potíží s výkonem.

![Přehled služby Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database metriky a diagnostické protokoly je možné streamovat do Azure SQL Analytics pomocí možnosti zabudované **Odeslat do Log Analytics** na kartě nastavení diagnostiky na portálu. Službu Log Analytics můžete taky povolit pomocí nastavení diagnostiky prostřednictvím rutin PowerShellu, rozhraní příkazového řádku Azure nebo Azure Monitor REST API.

### <a name="installation-overview"></a>Přehled instalace

SQL Database loďstva můžete monitorovat pomocí Azure SQL Analytics. Proveďte následující kroky:

1. Vytvořte Azure SQL Analytics řešení z Azure Marketplace.
2. Vytvořte pracovní prostor monitorování v řešení.
3. Nakonfigurujte databáze pro streamování diagnostiky diagnostiky do pracovního prostoru.

Pokud používáte elastické fondy nebo spravované instance, musíte taky nakonfigurovat streamování diagnostiky z těchto prostředků.

### <a name="create-azure-sql-analytics-resource"></a>Vytvořit prostředek Azure SQL Analytics

1. Vyhledejte Azure SQL Analytics v Azure Marketplace a vyberte ji.

   ![Hledat Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Na obrazovce s přehledem řešení vyberte **vytvořit** .

3. Do formuláře Azure SQL Analytics zadejte další požadované informace: název pracovního prostoru, předplatné, skupina prostředků, umístění a cenová úroveň.

   ![Konfigurace Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Kliknutím na **OK** potvrďte a pak vyberte **vytvořit**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurace databází pro zaznamenávání metrik a diagnostických protokolů

Nejjednodušší způsob, jak nakonfigurovat, kde databáze zaznamenávají metriky, je použití Azure Portal. Jak je popsáno výše, v Azure Portal klikněte na prostředek SQL Database a vyberte **nastavení diagnostiky**.

Pokud používáte elastické fondy nebo spravované instance, budete také muset nakonfigurovat nastavení diagnostiky v těchto zdrojích, aby mohla diagnostická Diagnostika umožňovat streamování do pracovního prostoru.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Použití řešení SQL Analytics pro monitorování a upozorňování

SQL Analytics můžete použít jako hierarchický řídicí panel k zobrazení svých SQL Databasech prostředků.

- Informace o použití řešení SQL Analytics najdete v tématu [monitorování SQL Database pomocí řešení SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Informace o tom, jak nastavit výstrahy pro SQL Database a spravovanou instanci založené na analýze SQL, najdete v tématu [vytváření výstrah pro SQL Database a spravovanou instanci](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Můžete streamovat SQL Database metriky a diagnostické protokoly do Event Hubs pomocí integrovaného **datového proudu pro možnost centra událostí** v Azure Portal. ID pravidla Service Bus můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin PowerShellu, rozhraní příkazového řádku Azure nebo REST API Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co dělat s protokoly metrik a diagnostikami v Event Hubs

Jakmile se vybraná data streamují do Event Hubs, budete mít jeden krok blíž k povolení rozšířených scénářů monitorování. Event Hubs funguje jako přední dveře pro kanál událostí. Po shromáždění dat do centra událostí je možné je transformovat a ukládat pomocí poskytovatele analýz v reálném čase nebo adaptéru úložiště. Event Hubs odpojí produkci proudu událostí od spotřeby těchto událostí. Tímto způsobem můžou příjemci události získat přístup k událostem podle vlastního plánu. Další informace o Event Hubs najdete v tématech:

- [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

V Event Hubs můžete použít streamované metriky pro:

- **Zobrazení stavu služby streamování dat Hot-Path do Power BI**. Pomocí Event Hubs, Stream Analytics a Power BI můžete snadno transformovat metriky a diagnostická data na vaše služby Azure téměř v reálném čase. Přehled nastavení centra událostí, zpracování dat pomocí Stream Analytics a použití Power BI jako výstupu najdete v tématu [Stream Analytics a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Streamování protokolů od jiných výrobců a datových proudů telemetrie**. Pomocí služby Event Hubs streaming můžete získat své metriky a diagnostické protokoly do různých řešení pro monitorování a analýzu protokolů třetích stran.

- **Sestavte vlastní telemetrii a platformu protokolování**. Už máte vlastní platformu telemetrie nebo se domnívají, že ji vytváříte? Vysoce škálovatelná povaha publikování a odběru Event Hubs umožňuje flexibilní příjem protokolů diagnostiky. Přečtěte si [průvodce Event Hubs Dan na používání v globální platformě telemetrie](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Streamování do úložiště

SQL Database metriky a diagnostické protokoly můžete v Azure Storage ukládat pomocí integrovaného archivu v možnosti **účtu úložiště** v Azure Portal. Úložiště můžete taky povolit pomocí nastavení diagnostiky prostřednictvím rutin PowerShellu, rozhraní příkazového řádku Azure nebo Azure Monitor REST API.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma metrik a protokolů diagnostiky v účtu úložiště

Po nastavení metrik a shromažďování diagnostických protokolů se kontejner úložiště vytvoří v účtu úložiště, který jste vybrali, když jsou k dispozici první řádky dat. Struktura objektů BLOB je:

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

Pokud vyberete Event Hubs nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraní data, která jsou starší než vybrané časové období. Pokud zadáte Log Analytics, zásady uchovávání informací závisí na vybrané cenové úrovni. V takovém případě může dodané volné jednotky příjmu dat umožnit bezplatné monitorování několika databází každý měsíc. Jakákoli spotřeba diagnostické telemetrie nad rámec volných jednotek může mít za následek náklady. Mějte na paměti, že aktivní databáze s těžšími úlohami ingestují více dat než nečinné databáze. Další informace najdete v tématu [ceny Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pokud používáte Azure SQL Analytics, můžete monitorovat spotřebu příjmu dat v řešení zvolením možnosti **pracovní prostor OMS** v navigační nabídce Azure SQL Analytics a následným výběrem možnosti **využití** a **Odhadované náklady**.

## <a name="metrics-and-logs-available"></a>Dostupné metriky a protokoly

Azure SQL Database, elastické fondy a spravovaná instance jsou popsány níže v části monitorování telemetrie. Shromážděná telemetrie monitorování v rámci služby SQL Analytics lze použít pro vlastní analýzu a vývoj aplikací pomocí jazyka [Azure Monitorch dotazů protokolu](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) .

## <a name="basic-metrics"></a>Základní metriky

Podrobnosti o základních metrikách podle prostředků najdete v následujících tabulkách.

> [!NOTE]
> Možnost základních metrik se dřív jmenovala jako všechny metriky. Změna byla provedena pouze pro pojmenování a na monitorované metriky se nezměnily žádné změny. Tato změna byla zahájena, aby bylo možné v budoucnu zavést další kategorie metrik.

### <a name="basic-metrics-for-elastic-pools"></a>Základní metriky pro elastické fondy

|**Prostředek**|**Metriky**|
|---|---|
|Elastický fond|procento eDTU, využité eDTU, limit pro eDTU, procento využití procesoru, procento čtení fyzických dat, procentuální hodnota zápisu protokolu, procento relací, procento pracovních procesů, úložiště, procento úložiště, omezení úložiště, procento XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Základní metriky pro databáze SQL Azure

|**Prostředek**|**Metriky**|
|---|---|
|Databáze SQL Azure|Procento DTU, využité DTU, limit DTU, procento využití procesoru, procento přečtených fyzických dat, procentuální hodnota zápisu protokolu, úspěšná/neúspěšná/zablokovaná připojeními brány firewall, procento relací, procento pracovních podílů, úložiště, procentuální hodnota úložiště, procento XTP úložiště a zablokování |

## <a name="basic-logs"></a>Základní protokoly

Podrobnosti o telemetrie dostupných pro všechny protokoly jsou popsány v následujících tabulkách. Informace o tom, které protokoly jsou podporovány pro konkrétní charakter databáze, najdete v tématu [podporované diagnostické protokolování](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) – Azure SQL Single, Pooled nebo instance Database.

### <a name="resource-usage-stats-for-managed-instance"></a>Statistika využití prostředků pro spravovanou instanci

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure|
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál ResourceUsageStats |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál MANAGEDINSTANCES |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název spravované instance |
|Prostředku|Identifikátor URI prostředku |
|SKU_s|SKU produktu Managed instance |
|virtual_core_count_s|Počet dostupných virtuální jádra |
|avg_cpu_percent_s|Průměrné procento procesoru |
|reserved_storage_mb_s|Rezervovaná kapacita úložiště na spravované instanci |
|storage_space_used_mb_s|Použito úložiště na spravované instanci |
|io_requests_s|Počet IOPS |
|io_bytes_read_s|Přečtené bajty IOPS |
|io_bytes_written_s|Zapsané bajty IOPS |

### <a name="query-store-runtime-statistics"></a>Statistiky za běhu úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál QueryStoreRuntimeStatistics |
|OperationName|Název operace Stál QueryStoreRuntimeStatisticsEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
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

### <a name="query-store-wait-statistics"></a>Statistika čekání na úložiště dotazů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál QueryStoreWaitStatistics |
|OperationName|Název operace Stál QueryStoreWaitStatisticsEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
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

### <a name="errors-dataset"></a>Datová sada chyb

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQ |
|Kategorie|Název kategorie Stál Chyby |
|OperationName|Název operace Stál ErrorEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
|Message|Chybová zpráva v prostém textu |
|user_defined_b|Je chybově definovaný bit uživatelem |
|error_number_d|Kód chyby |
|severity|Závažnost chyby |
|state_d|Stav chyby |
|query_hash_s|Hodnota hash dotazu neúspěšného dotazu, pokud je k dispozici |
|query_plan_hash_s|Hodnota hash plánu dotazu neúspěšného dotazu, je-li k dispozici |

Přečtěte si další informace o [SQL serverch chybových zprávách](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Datová sada statistiky čekání databáze

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál DatabaseWaitStatistics |
|OperationName|Název operace Stál DatabaseWaitStatisticsEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
|wait_type_s|Název typu čekání |
|start_utc_date_t [UTC]|Čas zahájení měřeného období |
|end_utc_date_t [UTC]|Čas ukončení měřeného období |
|delta_max_wait_time_ms_d|Maximální doba čekání na spuštění |
|delta_signal_wait_time_ms_d|Doba čekání na celkový počet signálů |
|delta_wait_time_ms_d|Celková doba čekání v období |
|delta_waiting_tasks_count_d|Počet čekajících úloh |

Přečtěte si další informace o [statistice čekání databáze](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Datová sada časových limitů

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál Vypršení časových limitů |
|OperationName|Název operace Stál TimeoutEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
|error_state_d|Chybový kód stavu |
|query_hash_s|Hodnota hash dotazu, je-li k dispozici |
|query_plan_hash_s|Hodnota hash plánu dotazu, je-li k dispozici |

### <a name="blockings-dataset"></a>Datová sada bloků

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál Čeká |
|OperationName|Název operace Stál BlockEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
|lock_mode_s|Režim zámku použitý dotazem |
|resource_owner_type_s|Vlastník zámku |
|blocked_process_filtered_s|KÓD XML sestavy blokovaného procesu |
|duration_d|Doba trvání zámku v mikrosekundách |

### <a name="deadlocks-dataset"></a>Datová sada zablokování

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC] |Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál Zablokování |
|OperationName|Název operace Stál DeadlockEvent |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
|deadlock_xml_s|XML sestavy zablokování |

### <a name="automatic-tuning-dataset"></a>Datová sada automatického ladění

|Vlastnost|Popis|
|---|---|
|TenantId|ID tenanta |
|SourceSystem|Stál Azure |
|TimeGenerated [UTC]|Časové razítko záznamu protokolu |
|type|Stál AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků Stál MICROSOFT.SQL |
|Kategorie|Název kategorie Stál AutomaticTuning |
|Resource|Název zdroje |
|Typ prostředku|Název typu prostředku Stál SERVERY/DATABÁZE |
|SubscriptionId|Identifikátor GUID předplatného pro databázi |
|Skupina prostředků|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|LogicalDatabaseName_s|Název databáze |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|Prostředku|Identifikátor URI prostředku |
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

### <a name="intelligent-insights-dataset"></a>Intelligent Insights datová sada

Přečtěte si další informace o [formátu protokolu Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další postup

Informace o tom, jak povolit protokolování a pochopit kategorie metrik a protokolů podporované různými službami Azure, najdete v těchto tématech:

- [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Přehled diagnostických protokolů Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Pokud se chcete dozvědět o Event Hubs, přečtěte si:

- [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Informace o tom, jak nastavit výstrahy na základě telemetrie z Log Analytics, najdete v těchto tématech:

- [Vytváření výstrah pro SQL Database a spravovanou instanci](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
