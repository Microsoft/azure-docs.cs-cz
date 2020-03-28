---
title: 'Kurz: Ingestování dat monitorování v Azure Data Explorer bez kódu'
description: V tomto kurzu se dozvíte, jak ingestovat data monitorování do Průzkumníka dat Azure bez jednoho řádku kódu a dotazna tato data.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198044"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Kurz: Ingestování a monitorování dotazů v Průzkumníku dat Azure 

Tento kurz vás naučí, jak ingestovat data z diagnostických protokolů a protokolů aktivit do clusteru Průzkumníka dat Azure bez psaní kódu. Pomocí této jednoduché metody ingestování můžete rychle začít dotazování Azure Data Explorer pro analýzu dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte tabulky a mapování ingestování v databázi Průzkumníka dat Azure.
> * Naformátujte přijatá data pomocí zásad aktualizace.
> * Vytvořte [centrum událostí](/azure/event-hubs/event-hubs-about) a připojte ho k Azure Data Exploreru.
> * Streamujte data do centra událostí z [diagnostických metrik Azure Monitoru a protokolů](/azure/azure-monitor/platform/diagnostic-settings) a [protokolů aktivit](/azure/azure-monitor/platform/activity-logs-overview).
> * Dotaz na přijatá data pomocí Průzkumníka dat Azure.

> [!NOTE]
> Vytvořte všechny prostředky ve stejném umístění nebo oblasti Azure. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Průzkumníka dat Azure](create-cluster-database-portal.md). V tomto kurzu je název databáze *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Zprostředkovatel dat Azure Monitor: diagnostické metriky a protokoly a protokoly aktivit

Zobrazit a pochopit data poskytovaná diagnostickými metrikami Azure Monitor a protokoly a protokoly aktivit níže. Na základě těchto datových schémat vytvoříte kanál ingestování. Všimněte si, že každá událost v protokolu má pole záznamů. Toto pole záznamů bude rozděleno později v kurzu.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Příklady diagnostických metrik a protokolů a protokolů aktivit

Diagnostické metriky Azure a protokoly a protokoly aktivit jsou emitovány službou Azure a poskytují data o provozu této služby. 

# <a name="diagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="example"></a>Příklad

Diagnostické metriky jsou agregovány s časovým zrnkem 1 minutu. Následuje příklad schématu událostí metriky Průzkumníka dat Azure pro dobu trvání dotazu:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="example"></a>Příklad

Následuje příklad [protokolu diagnostického ingestování](using-diagnostic-logs.md#diagnostic-logs-schema)aplikace Azure Data Explorer :

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="example"></a>Příklad

Protokoly aktivit Azure jsou protokoly na úrovni předplatného, které poskytují přehled o operacích prováděných s prostředky ve vašem předplatném. Následuje příklad události protokolu aktivit pro kontrolu přístupu:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Nastavení kanálu ingestování v Azure Data Exploreru

Nastavení kanálu Průzkumníka dat Azure zahrnuje několik kroků, jako je [například vytváření tabulek a ingestování dat](/azure/data-explorer/ingest-sample-data#ingest-data). Můžete také manipulovat, mapovat a aktualizovat data.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Připojení k webovému uživatelskému rozhraní Průzkumníka dat Azure

V databázi Azure Data Explorer *TestDatabase* vyberte **Dotaz,** abyste otevřeli webové uživatelské rozhraní Azure Data Explorer.

![Stránka dotazu](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Vytvoření cílových tabulek

Struktura protokolů Azure Monitor není tabulkový. Budete manipulovat s daty a rozbalíte každou událost na jeden nebo více záznamů. Nezpracovaná data budou ingestována do zprostředkující tabulky s názvem *ActivityLogsRawRecords* pro protokoly aktivit a *DiagnosticRawRecords* pro diagnostické metriky a protokoly. V té době budou data manipulována a rozšířena. Pomocí zásad aktualizace budou rozšířená data poté ingestována do tabulky *ActivityLogs* pro protokoly aktivit, *DiagnosticMetrics* pro diagnostické metriky a *DiagnosticLogs* pro diagnostické protokoly. To znamená, že budete muset vytvořit dvě samostatné tabulky pro ingestování protokolů aktivit a tři samostatné tabulky pro ingestování diagnostických metrik a protokolů.

Pomocí webového uživatelského rozhraní Průzkumníka dat Azure vytvořte cílové tabulky v databázi Průzkumníka dat Azure.

# <a name="diagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Vytvoření tabulek pro diagnostické metriky

1. V databázi *TestDatabase* vytvořte tabulku s názvem *DiagnosticMetrics* pro uložení záznamů diagnostických metrik. Použijte následující `.create table` řídicí příkaz:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Chcete-li tabulku vytvořit, vyberte **spustit.**

    ![Spuštění dotazu](media/ingest-data-no-code/run-query.png)

1. Vytvořte zprostředkující tabulku dat s názvem *DiagnosticRawRecords* v *databázi TestDatabase* pro manipulaci s daty pomocí následujícího dotazu. Chcete-li tabulku vytvořit, vyberte **spustit.**

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nastavte [nulové zásady uchovávání informací](/azure/kusto/management/retention-policy) pro zprostředkující tabulku:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Vytvoření tabulek pro diagnostické protokoly 

1. V databázi *TestDatabase* vytvořte tabulku s názvem DiagnosticLogs pro uložení záznamů protokolu *diagnostiky.* Použijte následující `.create table` řídicí příkaz:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Chcete-li tabulku vytvořit, vyberte **spustit.**

1. Vytvořte zprostředkující tabulku dat s názvem *DiagnosticRawRecords* v *databázi TestDatabase* pro manipulaci s daty pomocí následujícího dotazu. Chcete-li tabulku vytvořit, vyberte **spustit.**

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nastavte [nulové zásady uchovávání informací](/azure/kusto/management/retention-policy) pro zprostředkující tabulku:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Vytvoření tabulek pro protokoly aktivit 

1. Vytvořte tabulku s názvem *ActivityLogs* v *databázi TestDatabase* pro příjem záznamů protokolu aktivit. Pokud chcete tabulku vytvořit, spusťte následující dotaz Průzkumníka dat Azure:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Vytvořte zprostředkující tabulku dat s názvem *ActivityLogsRawRecords* v *databázi TestDatabase* pro manipulaci s daty:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Nastavte [nulové zásady uchovávání informací](/azure/kusto/management/retention-policy) pro zprostředkující tabulku:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Vytvoření mapování tabulek

 Vzhledem k `json`tomu, že formát dat je , je vyžadováno mapování dat. Mapování `json` mapuje každou cestu json na název sloupce tabulky.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostické metriky / Diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapování diagnostických metrik a protokolů do tabulky

Chcete-li namapovat diagnostickou metriku a data protokolu do tabulky, použijte následující dotaz:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapování protokolů aktivit do tabulky

Chcete-li namapovat data protokolu aktivit do tabulky, použijte následující dotaz:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Vytvoření zásad aktualizace pro metrická data a data protokolu

# <a name="diagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Vytvoření zásad aktualizace dat pro metriky diagnostiky

1. Vytvořte [funkci,](/azure/kusto/management/functions) která rozšiřuje kolekci záznamů diagnostické metriky tak, aby každá hodnota v kolekci obdrží samostatný řádek. Použijte [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátor:
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Přidejte [zásady aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově požitá data v tabulce zprostředkujících dat *DiagnosticRawRecords* a výsledky bude ingestována do tabulky *DiagnosticMetrics:*

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Vytvořit zásady aktualizace dat pro protokoly diagnostiky

1. Vytvořte [funkci,](/azure/kusto/management/functions) která rozšiřuje kolekci záznamů diagnostických protokolů tak, aby každá hodnota v kolekci obdržela samostatný řádek. Povolíte protokoly ingestování v clusteru Azure Data Explorer a [použijete schéma protokolů ingestování](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Vytvoříte jednu tabulku pro úspěšné a neúspěšné požití, zatímco některá pole budou prázdná pro úspěšné požití (například ErrorCode). Použijte [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátor:

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Přidejte [zásady aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově ingemovaná data v tabulce zprostředkujících dat *DiagnosticRawRecords* a výsledky bude ingestována do tabulky *DiagnosticLogs:*

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Vytvořit zásady aktualizace dat pro protokoly aktivit

1. Vytvořte [funkci,](/azure/kusto/management/functions) která rozšiřuje kolekci záznamů protokolu aktivit tak, aby každá hodnota v kolekci obdrží samostatný řádek. Použijte [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátor:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Přidejte [zásady aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově ingestovaná data v tabulce zprostředkujících dat *ActivityLogsRawRecords* a výsledky ingestuje do tabulky *ActivityLogs:*

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Vytvoření oboru názvů Centra událostí Azure

Nastavení diagnostiky Azure umožňují export metrik a protokolů do účtu úložiště nebo do centra událostí. V tomto kurzu budeme směrovat metriky a protokoly prostřednictvím centra událostí. Vytvoříte obor názvů Event Hubs a centrum událostí pro diagnostické metriky a protokoly v následujících krocích. Azure Monitor vytvoří přehledy centra událostí *- provozní protokoly* pro protokoly aktivit.

1. Vytvořte centrum událostí pomocí šablony Azure Resource Manager u na webu Azure Portal. Chcete-li postupovat podle zbývajících kroků v tomto článku, klikněte pravým tlačítkem myši na tlačítko **Nasadit do Azure** a pak vyberte **Otevřít v novém okně**. Tlačítko **Nasadit do Azure** vás přenese na portál Azure.

    [![Tlačítko Nasazení do Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Vytvořte obor názvů Event Hubs a centrum událostí pro diagnostické protokoly.

    ![Vytvoření centra událostí](media/ingest-data-no-code/event-hub.png)

1. Do formuláře zadejte následující informace. Pro všechna nastavení, která nejsou uvedena v následující tabulce, použijte výchozí hodnoty.

    **Nastavení** | **Navrhovaná hodnota** | **Popis**
    |---|---|---|
    | **Předplatné** | *Vaše předplatné* | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | **Skupina prostředků** | *skupina testovacích prostředků* | Vytvořte novou skupinu prostředků. |
    | **Umístění** | Vyberte oblast, která nejlépe vyhovuje vašim potřebám. | Vytvořte obor názvů Event Hubs ve stejném umístění jako ostatní prostředky.
    | **Název oboru názvů** | *AzureMonitoringData* | Zvolte jedinečný název, který identifikuje váš obor názvů.
    | **Název centra událostí** | *DiagnosticData* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. |
    | **Název skupiny uživatelů** | *adxpipeline* | Vytvořte název skupiny příjemce. Skupiny uživatelů umožňují, aby měla každá z aplikací samostatné zobrazení streamu událostí. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Připojení metrik a protokolů Azure Monitoru k centru událostí

Teď je potřeba připojit diagnostické metriky a protokoly a protokoly aktivit do centra událostí.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostické metriky / Diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Připojení diagnostických metrik a protokolů k centru událostí

Vyberte zdroj, ze kterého chcete exportovat metriky. Export diagnostických dat podporuje několik typů prostředků, včetně oboru názvů Event Hubs, Azure Key Vault, Azure IoT Hub a clusterů Azure Data Explorer. V tomto kurzu použijeme cluster Azure Data Explorer jako náš prostředek, zkontrolujeme metriky výkonu dotazu a protokoly výsledků ingestování.

1. Vyberte cluster Kusto na webu Azure Portal.
1. Vyberte **Nastavení diagnostiky**a pak vyberte odkaz **Zapnout diagnostiku.** 

    ![Nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings.png)

1. Otevře se podokno **Nastavení diagnostiky.** Proveďte následující kroky:
   1. Pojmenujte diagnostická data protokolu názvem *ADXExportedData*.
   1. V **části LOG**zaškrtněte **políčka SucceededIngestion** i **FailedIngestion** .
   1. V **části METRIC**zaškrtněte políčko Výkon **dotazu.**
   1. Zaškrtněte **políčko Stream do centra událostí.**
   1. Vyberte **Konfigurovat**.

      ![Podokno nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings-window.png)

1. V podokně **Vybrat centrum událostí** nakonfigurujte způsob exportu dat z diagnostických protokolů do centra událostí, které jste vytvořili:
    1. V seznamu **oborů názvů centra událostí Select** vyberte *AzureMonitoringData*.
    1. V seznamu **Vybrat název centra událostí** vyberte *DiagnosticData*.
    1. V seznamu **Zásad vybrat centrum událostí** vyberte **RootManagerSharedAccessKey**.
    1. Vyberte **OK**.

1. Vyberte **Uložit**.

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Připojení protokolů aktivit k centru událostí

1. V levé nabídce portálu Azure vyberte **protokol aktivit**.
1. Otevře se okno **protokolu aktivit.** Vyberte **Exportovat do centra událostí**.

    ![Okno protokolu aktivit](media/ingest-data-no-code/activity-log.png)

1. Otevře se okno **Protokol aktivit exportu:**
 
    ![Okno protokolu exportu aktivit](media/ingest-data-no-code/export-activity-log.png)

1. V okně **Exportovat protokol aktivit** postupujte takto:
      1. Vyberte své předplatné.
      1. V seznamu **Oblasti** zvolte **Vybrat vše**.
      1. Zaškrtněte **políčko Exportovat do centra událostí.**
      1. Zvolte **Vybrat obor názvů sběrnice,** chcete-li otevřít podokno **Vybrat rozbočovač událostí.**
      1. V podokně **Vybrat centrum událostí** vyberte předplatné.
      1. V seznamu **oborů názvů centra událostí Select** vyberte *AzureMonitoringData*.
      1. V seznamu **Vybrat název zásad centra událostí** vyberte výchozí název zásad centra událostí.
      1. Vyberte **OK**.
      1. V levém horním rohu okna vyberte **Uložit**.
   Centrum událostí s názvem *insights-provozní protokoly* budou vytvořeny.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Zobrazení dat přitočené do centra událostí

1. Počkejte několik minut, dokud není definováno připojení a export protokolu aktivit do centra událostí je dokončen. Přejděte do oboru názvů Centra událostí a podívejte se na centra událostí, která jste vytvořili.

    ![Vytvořená centra událostí](media/ingest-data-no-code/event-hubs-created.png)

1. Podívejte se na data, která proudí do centra událostí:

    ![Data centra událostí](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Připojení centra událostí k Azure Data Exploreru

Nyní je třeba vytvořit datová připojení pro diagnostické metriky a protokoly a protokoly aktivit.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Vytvoření datového připojení pro diagnostické metriky a protokoly a protokoly aktivit

1. V clusteru Průzkumníka dat Azure s názvem *kustodocs*vyberte **databáze** v levé nabídce.
1. V okně **Databáze** vyberte *databázi TestDatabase.*
1. V levé nabídce vyberte **položku Sdílení dat**.
1. V okně **Přijím dat** klikněte na **+ Přidat datové připojení**.
1. V okně **Datové připojení** zadejte následující informace:

    ![Datové připojení centra událostí](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostické metriky / Diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 

1. V okně Datové **připojení** použijte následující nastavení:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *DiagnosticsLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centrum událostí** | *DiagnosticData* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *adxpipeline* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. V tomto kurzu budete používat statické směrování (výchozí), kde zadáte název tabulky, formát dat a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Table** | *DiagnosticrawRecords* | Tabulka, kterou jste vytvořili v databázi *TestDatabase.* |
    | **Formát dat** | *JSON* | Formát použitý v tabulce. |
    | **Mapování sloupců** | *DiagnosticRawRecordsMapping* | Mapování, které jste vytvořili v databázi *TestDatabase,* která mapuje příchozí data JSON na názvy sloupců a datové typy tabulky *DiagnosticRawRecords.*|
    | | |

1. Vyberte **Vytvořit**.  

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)

1. V okně Datové **připojení** použijte následující nastavení:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *ActivityLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centrum událostí** | *postřehy- provozní protokoly* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *$Default* | Výchozí skupina spotřebitelů. V případě potřeby můžete vytvořit jinou skupinu spotřebitelů. |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. V tomto kurzu budete používat statické směrování (výchozí), kde zadáte název tabulky, formát dat a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | Tabulka, kterou jste vytvořili v databázi *TestDatabase.* |
    | **Formát dat** | *JSON* | Formát použitý v tabulce. |
    | **Mapování sloupců** | *ActivityLogsRawRecordsMapping* | Mapování, které jste vytvořili v databázi *TestDatabase,* která mapuje příchozí data JSON na názvy sloupců a datové typy tabulky *ActivityLogsRawRecords.*|
    | | |

1. Vyberte **Vytvořit**.  
---

## <a name="query-the-new-tables"></a>Dotaz na nové tabulky

Nyní máte kanál s tokem dat. Příjem přes cluster trvá ve výchozím nastavení 5 minut, proto před zahájením dotazování povolte několik minut tok dat.

# <a name="diagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Dotaz na tabulku diagnostických metrik

Následující dotaz analyzuje data doby trvání dotazu ze záznamů diagnostických metrik v Aplikaci Azure Data Explorer:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Dotaz na tabulku diagnostických protokolů

Tento kanál vytváří ingestování prostřednictvím centra událostí. Zkontrolujete výsledky těchto požití.
Následující dotaz analyzuje, kolik ingestování naběhl za minutu, `Database`včetně `Table` `IngestionSourcePath` vzorku , a pro každý interval:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticrawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Dotaz na tabulku protokolů aktivit

Následující dotaz analyzuje data ze záznamů protokolu aktivit v Průzkumníku dat Azure:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Další kroky

* Naučte se psát mnoho dalších dotazů na data, která jste extrahovali z Průzkumníka dat Azure, pomocí [dotazů pro zápis pro Azure Data Explorer](write-queries.md).
* [Monitorování operací ingestování Průzkumníka dat Azure pomocí diagnostických protokolů](using-diagnostic-logs.md)
* [Použití metrik k monitorování stavu clusterů](using-metrics.md)
