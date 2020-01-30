---
title: 'Kurz: ingestování dat monitorování v Azure Průzkumník dat bez jednoho řádku kódu'
description: V tomto kurzu se naučíte ingestovat data monitorování do Azure Průzkumník dat bez jednoho řádku kódu a dotazovat se na tato data.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: c160f04ef7120a6c90991d8e6ecdf98b2f0d348e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836555"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Kurz: ingestování a dotazování dat monitorování v Azure Průzkumník dat 

V tomto kurzu se dozvíte, jak ingestovat data z protokolů diagnostiky a aktivit do clusteru Azure Průzkumník dat bez psaní kódu. Díky této jednoduché metodě příjmu můžete rychle začít dotazovat se na Azure Průzkumník dat pro analýzu dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření tabulek a mapování přijímání v databázi Azure Průzkumník dat.
> * Naformátujte ingestovaná data pomocí zásad aktualizace.
> * Vytvořte [centrum událostí](/azure/event-hubs/event-hubs-about) a připojte ho k Azure Průzkumník dat.
> * Streamujte data do centra událostí z Azure Monitor [diagnostické metriky a](/azure/azure-monitor/platform/diagnostic-settings) [protokoly aktivit](/azure/azure-monitor/platform/activity-logs-overview).
> * Dotazování na ingestovaná data pomocí Azure Průzkumník dat.

> [!NOTE]
> Vytvoří všechny prostředky ve stejném umístění Azure nebo oblasti. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md). V tomto kurzu je název databáze *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Zprostředkovatel dat Azure Monitor: diagnostické metriky a protokoly aktivit

Zobrazení a pochopení dat poskytovaných diagnostikou Azure Monitor diagnostické metriky a protokolů aktivit níže. Kanál pro příjem dat vytvoříte na základě těchto schémat dat. Všimněte si, že každá událost v protokolu má pole záznamů. Toto pole záznamů bude v tomto kurzu rozděleno později.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Příklady diagnostických metrik a protokolů aktivit

Diagnostické metriky Azure a protokoly aktivit jsou vydávány službou Azure a poskytují údaje o provozu této služby. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="example"></a>Příklad:

Diagnostické metriky jsou agregované s časovým intervalem 1 minuty. Následuje příklad schématu události služby Azure Průzkumník dat metrika při dobu trvání dotazu:

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

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="example"></a>Příklad:

Následuje příklad protokolu ingestování [diagnostiky](using-diagnostic-logs.md#diagnostic-logs-schema)Azure Průzkumník dat:

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
# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="example"></a>Příklad:

Protokoly aktivit Azure jsou protokoly na úrovni předplatného, které poskytují přehled o operacích provedených u prostředků v rámci vašeho předplatného. Následuje příklad události protokolu aktivit pro kontrolu přístupu:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Nastavení kanálu pro přijímání zpráv v Azure Průzkumník dat

Nastavení kanálu služby Azure Průzkumník dat zahrnuje několik kroků, jako je například [vytváření tabulek a přijímání dat](/azure/data-explorer/ingest-sample-data#ingest-data). Můžete také manipulovat, mapovat a aktualizovat data.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Připojení k webovému uživatelskému rozhraní Azure Průzkumník dat

V databázi Azure Průzkumník dat *TestDatabase* vyberte **dotaz** a otevřete webové uživatelské rozhraní pro Azure Průzkumník dat.

![Stránka dotazu](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Vytvoření cílových tabulek

Struktura protokolů Azure Monitor není tabulková. Budete pracovat s daty a rozbalíte jednotlivé události do jednoho nebo více záznamů. Nezpracovaná data se ingestují do mezilehlé tabulky s názvem *ActivityLogsRawRecords* pro protokoly aktivit a *DiagnosticRawRecords* pro diagnostické metriky a protokoly. V tuto chvíli se data budou zpracovávat a rozšiřovat. Pomocí zásad aktualizace budou rozbalená data v *ActivityLogs* tabulce pro protokoly aktivit, *DiagnosticMetrics* pro diagnostické metriky a *DiagnosticLogs* pro diagnostické protokoly. To znamená, že budete muset vytvořit dvě samostatné tabulky pro ingestování protokolů aktivit a tři samostatné tabulky pro ingestování diagnostických metrik a protokolů.

Pomocí webového uživatelského rozhraní Azure Průzkumník dat vytvořte cílové tabulky v databázi Azure Průzkumník dat.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Vytváření tabulek pro diagnostické metriky

1. V databázi *TestDatabase* vytvořte tabulku s názvem *DiagnosticMetrics* a uložte záznamy diagnostických metrik. Použijte následující příkaz `.create table` ovládacího prvku:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Vyberte možnost **Spustit** pro vytvoření tabulky.

    ![Spuštění dotazu](media/ingest-data-no-code/run-query.png)

1. Vytvořte tabulku zprostředkujících dat s názvem *DiagnosticRawRecords* v databázi *TestDatabase* pro manipulaci s daty pomocí následujícího dotazu. Vyberte možnost **Spustit** pro vytvoření tabulky.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nastavte [zásady pro uchovávání](/azure/kusto/management/retention-policy) nulových informací pro mezilehlé tabulky:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Vytvořit tabulky pro diagnostické protokoly 

1. V databázi *TestDatabase* vytvořte tabulku s názvem *DiagnosticLogs* a uložte záznamy diagnostických protokolů. Použijte následující příkaz `.create table` ovládacího prvku:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Vyberte možnost **Spustit** pro vytvoření tabulky.

1. Vytvořte tabulku zprostředkujících dat s názvem *DiagnosticRawRecords* v databázi *TestDatabase* pro manipulaci s daty pomocí následujícího dotazu. Vyberte možnost **Spustit** pro vytvoření tabulky.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nastavte [zásady pro uchovávání](/azure/kusto/management/retention-policy) nulových informací pro mezilehlé tabulky:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Vytvoření tabulek pro protokoly aktivit 

1. Vytvořte tabulku s názvem *ActivityLogs* v databázi *TestDatabase* pro příjem záznamů protokolu aktivit. Pokud chcete vytvořit tabulku, spusťte následující dotaz Azure Průzkumník dat:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Vytvořte tabulku zprostředkujících dat s názvem *ActivityLogsRawRecords* v databázi *TestDatabase* pro manipulaci s daty:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Nastavte [zásady pro uchovávání](/azure/kusto/management/retention-policy) nulových informací pro mezilehlé tabulky:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Vytvořit mapování tabulek

 Vzhledem k tomu, že je formát dat `json`, je vyžadováno mapování dat. Mapování `json` mapuje každou cestu JSON k názvu sloupce tabulky.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostické metriky/diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapování metrik a protokolů diagnostiky do tabulky

K namapování diagnostické metriky a dat protokolu do tabulky použijte následující dotaz:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapování protokolů aktivit na tabulku

Chcete-li namapovat data protokolu aktivit na tabulku, použijte následující dotaz:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Vytvoření zásady aktualizace pro data metrik a protokolů

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Vytvoření zásad aktualizace dat pro diagnostické metriky

1. Vytvořte [funkci](/azure/kusto/management/functions) , která rozbalí kolekci záznamů diagnostické metriky, aby každá hodnota v kolekci přijímala samostatný řádek. Použijte operátor [`mv-expand`](/azure/kusto/query/mvexpandoperator) :
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

2. Přidejte [zásadu aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v tabulce zprostředkujících dat *DiagnosticRawRecords* a ingestuje výsledky do tabulky *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Vytvoření zásad aktualizace dat pro diagnostické protokoly

1. Vytvořte [funkci](/azure/kusto/management/functions) , která rozšíří shromažďování záznamů diagnostických protokolů tak, aby každá hodnota v kolekci přijímala samostatný řádek. Povolíte protokoly příjmu v clusteru Azure Průzkumník dat a použijete [schéma příjmu protokolů](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Vytvoříte jednu tabulku pro úspěšnou a při neúspěšném příjmu, zatímco některá pole budou pro příjem úspěšných položek prázdná (například kód chyby). Použijte operátor [`mv-expand`](/azure/kusto/query/mvexpandoperator) :

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

2. Přidejte [zásadu aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v tabulce zprostředkujících dat *DiagnosticRawRecords* a ingestuje výsledky do tabulky *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Vytvoření zásad aktualizace dat pro protokoly aktivit

1. Vytvořte [funkci](/azure/kusto/management/functions) , která rozšíří shromažďování záznamů protokolu aktivit tak, aby každá hodnota v kolekci přijímala samostatný řádek. Použijte operátor [`mv-expand`](/azure/kusto/query/mvexpandoperator) :

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

2. Přidejte [zásadu aktualizace](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v tabulce zprostředkujících dat *ActivityLogsRawRecords* a ingestuje výsledky do tabulky *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Vytvoření oboru názvů Azure Event Hubs

Nastavení diagnostiky Azure umožňuje exportovat metriky a protokoly do účtu úložiště nebo do centra událostí. V tomto kurzu budeme směrovat metriky a protokoly prostřednictvím centra událostí. V následujících krocích vytvoříte obor názvů Event Hubs a centrum událostí pro diagnostické metriky a protokoly. Azure Monitor vytvoří informace o protokolech aktivit v centru událostí *Přehled – provozní protokoly* .

1. Vytvořte centrum událostí pomocí šablony Azure Resource Manager v Azure Portal. Chcete-li postupovat podle zbývajících kroků v tomto článku, klikněte pravým tlačítkem myši na tlačítko **nasadit do Azure** a pak vyberte možnost **otevřít v novém okně**. Tlačítko **nasadit do Azure** vás přesměruje na Azure Portal.

    [tlačítko ![nasazení do Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Vytvořte obor názvů Event Hubs a centrum událostí pro diagnostické protokoly.

    ![Vytvoření centra událostí](media/ingest-data-no-code/event-hub.png)

1. Do formuláře zadejte následující informace. Pro všechna nastavení, která nejsou uvedená v následující tabulce, použijte výchozí hodnoty.

    **Nastavení** | **Navrhovaná hodnota** | **Popis**
    |---|---|---|
    | **Předplatné** | *Vaše předplatné* | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | **Skupina prostředků** | *test-resource-group* | Vytvořte novou skupinu prostředků. |
    | **Umístění** | Vyberte oblast, která nejlépe vyhovuje vašim potřebám. | Vytvořte obor názvů Event Hubs ve stejném umístění jako jiné prostředky.
    | **Název oboru názvů** | *AzureMonitoringData* | Zvolte jedinečný název, který identifikuje váš obor názvů.
    | **Název centra událostí** | *DiagnosticData* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. |
    | **Název skupiny uživatelů** | *adxpipeline* | Vytvořte název skupiny uživatelů. Skupiny uživatelů umožňují, aby měla každá z aplikací samostatné zobrazení streamu událostí. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Připojit Azure Monitor metriky a protokoly do centra událostí

Teď musíte připojit své diagnostické metriky a protokoly a protokoly aktivit do centra událostí.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostické metriky/diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Připojení diagnostických metrik a protokolů k centru událostí

Vyberte prostředek, ze kterého se mají exportovat metriky. Několik typů prostředků podporuje export diagnostických dat, včetně Event Hubs obor názvů, Azure Key Vault, Azure IoT Hub a clusterů Průzkumník dat Azure. V tomto kurzu budeme používat cluster Azure Průzkumník dat jako náš prostředek, zkontrolujeme metriky výkonu dotazů a protokoly výsledků přijímání.

1. V Azure Portal vyberte svůj cluster Kusto.
1. Vyberte **nastavení diagnostiky**a pak vyberte odkaz **zapnout diagnostiku** . 

    ![Nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings.png)

1. Otevře se podokno **nastavení diagnostiky** . Proveďte následující kroky:
   1. Poskytněte data diagnostického protokolu název *ADXExportedData*.
   1. V části **protokol**zaškrtněte políčka **SucceededIngestion** i **FailedIngestion** .
   1. V části **metrika**zaškrtněte políčko **výkon dotazu** .
   1. Zaškrtněte políčko **datový proud do centra událostí** .
   1. Vyberte **Konfigurovat**.

      ![Podokno nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings-window.png)

1. V podokně **Vybrat centrum událostí** nakonfigurujte, jak se mají exportovat data z diagnostických protokolů do centra událostí, které jste vytvořili:
    1. V seznamu **Vybrat obor názvů centra událostí** vyberte *AzureMonitoringData*.
    1. V seznamu **Vybrat název centra událostí** vyberte možnost *DiagnosticData*.
    1. V seznamu **Vyberte název zásad centra událostí** vyberte **RootManagerSharedAccessKey**.
    1. Vyberte **OK**.

1. Vyberte **Uložit**.

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Připojení protokolů aktivit k centru událostí

1. V levé nabídce Azure Portal vyberte **Protokol aktivit**.
1. Otevře se okno **Protokol aktivit** . Vyberte **exportovat do centra událostí**.

    ![Okno protokolu aktivit](media/ingest-data-no-code/activity-log.png)

1. Otevře se okno **exportovat protokol aktivit** :
 
    ![Okno Exportovat protokol aktivit](media/ingest-data-no-code/export-activity-log.png)

1. V okně **exportovat protokol aktivit** proveďte následující kroky:
      1. Vyberte své předplatné.
      1. V seznamu **oblasti** zvolte **možnost Vybrat vše**.
      1. Zaškrtněte políčko **exportovat do centra událostí** .
      1. Zvolením **možnosti vybrat obor názvů služby Service Bus** otevřete podokno **Vybrat centrum událostí** .
      1. V podokně **Vybrat centrum událostí** vyberte své předplatné.
      1. V seznamu **Vybrat obor názvů centra událostí** vyberte *AzureMonitoringData*.
      1. V seznamu **Vyberte název zásad centra událostí** vyberte výchozí název zásady centra událostí.
      1. Vyberte **OK**.
      1. V levém horním rohu okna vyberte **Uložit**.
   Vytvoří se centrum událostí s názvem *Insights – provozními protokoly* .
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Zobrazení toku dat do Center událostí

1. Počkejte několik minut, než se nadefinuje připojení a bude dokončen export protokolu aktivit do centra událostí. Pokud chcete zobrazit centra událostí, která jste vytvořili, přejděte na svůj obor názvů Event Hubs.

    ![Vytvořená centra událostí](media/ingest-data-no-code/event-hubs-created.png)

1. Podívejte se na tok dat do centra událostí:

    ![Data centra událostí](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Připojení centra událostí k Azure Průzkumník dat

Nyní potřebujete vytvořit datová připojení pro diagnostické metriky a protokoly aktivit.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Vytvoření datového připojení pro diagnostické metriky a protokoly aktivit

1. V clusteru Azure Průzkumník dat s názvem *kustodocs*vyberte v nabídce vlevo možnost **databáze** .
1. V okně **databáze** vyberte databázi *TestDatabase* .
1. V nabídce vlevo vyberte možnost **přijímání dat**.
1. V okně pro příjem **dat** klikněte na **+ přidat datové připojení**.
1. V okně **datové připojení** zadejte následující informace:

    ![Datové připojení centra událostí](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostické metriky/diagnostické protokoly](#tab/diagnostic-metrics+diagnostic-logs) 

1. V okně **datové připojení** použijte následující nastavení:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *DiagnosticsLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centrum událostí** | *DiagnosticData* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *adxpipeline* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. V tomto kurzu použijete statické směrování (výchozí), kde zadáte název tabulky, formát dat a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Tabulka** | *DiagnosticRawRecords* | Tabulka, kterou jste vytvořili v databázi *TestDatabase* . |
    | **Formát dat** | *JSON* | Formát použitý v tabulce |
    | **Mapování sloupců** | *DiagnosticRawRecordsMapping* | Mapování, které jste vytvořili v databázi *TestDatabase* , která mapuje příchozí data JSON na názvy sloupců a datové typy tabulky *DiagnosticRawRecords* .|
    | | |

1. Vyberte **Vytvořit**.  

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)

1. V okně **datové připojení** použijte následující nastavení:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *ActivityLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centrum událostí** | *insights-operational-logs* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *$Default* | Výchozí skupina příjemců. V případě potřeby můžete vytvořit jinou skupinu uživatelů. |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. V tomto kurzu použijete statické směrování (výchozí), kde zadáte název tabulky, formát dat a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Tabulka** | *ActivityLogsRawRecords* | Tabulka, kterou jste vytvořili v databázi *TestDatabase* . |
    | **Formát dat** | *JSON* | Formát použitý v tabulce |
    | **Mapování sloupců** | *ActivityLogsRawRecordsMapping* | Mapování, které jste vytvořili v databázi *TestDatabase* , která mapuje příchozí data JSON na názvy sloupců a datové typy tabulky *ActivityLogsRawRecords* .|
    | | |

1. Vyberte **Vytvořit**.  
---

## <a name="query-the-new-tables"></a>Dotazování na nové tabulky

Nyní máte kanál s tokem dat. Ingestování prostřednictvím clusteru ve výchozím nastavení trvá 5 minut, takže data budou před zahájením dotazu zacházet do několika minut.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostické metriky](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Dotaz na tabulku diagnostických metrik

Následující dotaz analyzuje data o době trvání dotazů ze záznamů diagnostické metriky v Azure Průzkumník dat:

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

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostické protokoly](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Dotaz na tabulku diagnostických protokolů

Tento kanál vytváří ingestování prostřednictvím centra událostí. Prohlédnete si výsledky těchto ingest.
Následující dotaz analyzuje počet zpracovaných dat za minutu, včetně ukázky `Database`, `Table` a `IngestionSourcePath` pro každý interval:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Protokoly aktivit](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Dotaz na tabulku protokolů aktivit

Následující dotaz analyzuje data ze záznamů protokolu aktivit v Azure Průzkumník dat:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  průměr (trvání v MS) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>Další kroky

* Naučte se psát mnoho dalších dotazů na data extrahovaná z Azure Průzkumník dat pomocí [zápisu dotazů pro Azure Průzkumník dat](write-queries.md).
* [Monitorování operací ingestování v Azure Průzkumník dat pomocí diagnostických protokolů](using-diagnostic-logs.md)
* [Monitorování stavu clusteru pomocí metrik](using-metrics.md)
