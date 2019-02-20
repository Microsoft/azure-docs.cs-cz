---
title: 'Kurz: Ingestovat data diagnostiky a aktivitu protokolu v Průzkumníku dat Azure bez jeden řádek kódu'
description: V tomto kurzu se dozvíte, jak k ingestování dat do Průzkumníku dat Azure bez jeden řádek kódu a dotaz tato data.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 94ecfda127422e395cf10a4fac1bb07cd0614f5c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416611"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Kurz: Příjem dat v Průzkumníku dat Azure bez jeden řádek kódu

V tomto kurzu se dozvíte, jak ingestování diagnostiku a data protokolu aktivit do Průzkumníku dat Azure clusteru bez jeden řádek kódu. Tato metoda jednoduché ingestování umožňuje rychle začít se zadáváním dotazů Průzkumníka služby Azure Data pro analýzu dat.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvořte tabulky a příjmu mapování v databázi aplikace Průzkumník dat Azure.
> * Formátu přijatých dat pomocí zásad pro aktualizaci.
> * Vytvoření [centra událostí](/azure/event-hubs/event-hubs-about) a připojte ho do Průzkumníku dat Azure.
> * Stream data do centra událostí z [diagnostické protokoly Azure monitoru](/azure/azure-monitor/platform/diagnostic-logs-overview) a [protokolů aktivit Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Dotazování hodnot zpracovaných dat pomocí Průzkumníka služby Azure Data.

> [!NOTE]
> Vytvořte všechny prostředky ve stejném umístění nebo oblasti Azure. Jde o požadavek pro diagnostické protokoly Azure monitoru.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Průzkumník dat Azure clusteru a databáze služby](create-cluster-database-portal.md). V tomto kurzu je název databáze *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Zprostředkovatel dat – diagnostiky pro monitorování Azure a protokolů aktivit

Zobrazení a pochopení dat poskytované Azure Monitoring diagnostiky a protokolů aktivit. Vytvoříme kanál ingestování založené na tato data schémata.

### <a name="diagnostic-logs-example"></a>Příklad diagnostických protokolů

Diagnostické protokoly Azure jsou metriky, protože ho vygeneroval služby Azure, které poskytují informace o fungování dané služby. Data je agregovat s časovým intervalem 1 minuta. Každá událost diagnostické protokoly obsahuje jeden záznam. Následuje příklad schématu metrická událost Průzkumník dat Azure na dobu trvání dotazu:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Příklad protokoly aktivit

Protokoly aktivit v Azure jsou protokolů úrovně předplatného obsahující sadu záznamů. Protokoly poskytují přehled o tom, operace prováděné s prostředky ve vašem předplatném. Na rozdíl od diagnostické protokoly událostí protokoly aktivit obsahuje celou řadu záznamy. Budeme muset rozdělit toto pole záznamy v pozdější části kurzu. Tady je příklad událost protokolu aktivit pro kontrolu přístupu:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Nastavení příjmu kanálu v Průzkumníku dat Azure 

Nastavení kanálu Průzkumník dat Azure obsahuje různé kroky, které zahrnují [ingestování vytváření a data tabulky](/azure/data-explorer/ingest-sample-data#ingest-data). Můžete také manipulovat s, mapování a aktualizovat data.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Připojení k Azure Data Explorer webového uživatelského rozhraní

1. V Průzkumníku dat Azure *AzureMonitoring* databáze, vyberte **dotazu**, otevře se Průzkumník dat Azure webového uživatelského rozhraní.

    ![Dotaz](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Vytvoření cílové tabulky

Vytvoření cílové tabulky v databázi Průzkumník dat Azure pomocí webového uživatelského rozhraní Průzkumníka služby Azure Data.

#### <a name="diagnostic-logs-table"></a>Diagnostické protokoly tabulky

1. Vytvořit tabulku *DiagnosticLogsRecords* v *AzureMonitoring* databáze, která bude dostávat diagnostický protokol záznamů pomocí `.create table` řídit příkaz:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Vyberte **spustit** k vytvoření této tabulky.

    ![Spustit dotaz](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Protokoly aktivit tabulky

Vzhledem k tomu, že struktura protokoly aktivit se tabulkový, budete potřebovat pracuje s daty a rozšiřovat každá událost a jedna nebo více záznamů. Nezpracovaná data se ingestují do převodní tabulky *ActivityLogsRawRecords*. Data budou v té době manipulovat a rozšířit. Rozšířená data se potom být přijímána *ActivityLogsRecords* tabulky pomocí zásady aktualizace. Proto budete muset vytvořit dvou samostatných tabulek pro příjem protokolů aktivit.

1. Vytvořit tabulku *ActivityLogsRecords* v *AzureMonitoring* databáze, která bude dostávat záznamů protokolu aktivit. Spuštěním následujícího dotazu Průzkumník dat Azure k vytvoření této tabulky:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Vytvoření tabulky dočasných dat *ActivityLogsRawRecords* v *AzureMonitoring* databáze pro manipulaci s daty:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Vytvoření mapování tabulek

 Formát dat je `json`, proto je nutné data mapování. `json` Mapování každá cesta json se mapuje na název sloupce tabulky.

#### <a name="diagnostic-logs-table-mapping"></a>Mapování tabulek diagnostické protokoly

Mapování dat do tabulky pomocí následujícího dotazu:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Protokoly aktivit mapování tabulek

Mapování dat do tabulky pomocí následujícího dotazu:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Vytvořit zásady aktualizace

1. Vytvoření [funkce](/azure/kusto/management/functions) , který rozbalí kolekce záznamů tak, aby každá hodnota v kolekci obdrží samostatném řádku. Použití [ `mvexpand` ](/azure/kusto/query/mvexpandoperator) operátor:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Přidat [aktualizovat zásady](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Se automaticky spustí dotaz na všechny nově přijatých dat v *ActivityLogsRawRecords* dočasných dat tabulky a ingestovat jeho výsledky do *ActivityLogsRecords* tabulky:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Vytvoření Namespace centra událostí

Diagnostické protokoly Azure Povolit export metriky pro účet úložiště nebo centra událostí. V tomto kurzu jsme směrovat metriky prostřednictvím centra událostí. Pro diagnostické protokoly v následujících krocích vytvoříte Centrum událostí a služby Event Hubs Namespace. Monitorování Azure vytvoří Centrum událostí *insights-operational-logs* protokoly aktivity.

1. Vytvoření centra událostí pomocí šablony Azure Resource Manageru na webu Azure Portal. Pomocí následujícího tlačítka spusťte nasazení. Klikněte pravým tlačítkem a vyberte **otevřít v novém okně**, takže můžete postupujte podle zbývajících kroků v tomto článku. **Nasadit do Azure** tlačítko vás přesměruje na web Azure Portal.

    [![Nasazení do Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Vytvoření Namespace centra událostí a Centrum událostí pro diagnostické protokoly.

    ![Vytvoření centra událostí](media/ingest-data-no-code/event-hub.png)

    Do formuláře zadejte následující informace. Pro všechna nastavení, která nejsou uvedená v následující tabulce, použijte výchozí hodnoty.

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | Skupina prostředků | *test-resource-group* | Vytvořte novou skupinu prostředků. |
    | Umístění | Vyberte oblast, která nejlépe vyhovuje vašim potřebám. | Vytvořte obor názvů centra událostí ve stejném umístění jako ostatní prostředky.
    | Název oboru názvů | *AzureMonitoringData* | Zvolte jedinečný název, který identifikuje váš obor názvů.
    | Název centra událostí | *DiagnosticLogsData* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. |
    | Název skupiny uživatelů | *adxpipeline* | Vytvořte název skupiny příjemců. Umožňuje spotřebovávat více aplikací na každý má samostatné zobrazení datového proudu událostí. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Připojení Azure Monitoring protokoly do centra událostí

### <a name="diagnostic-logs-connection-to-event-hub"></a>Diagnostické protokoly připojení do centra událostí

Vyberte zdroj, ze kterého se má exportovat metriky. Existuje několik typů prostředků, které umožňují export diagnostické protokoly, včetně Event Hub Namespace, trezor klíčů, IoT Hub a cluster Průzkumníka služby Azure Data. V tomto kurzu používáme jako naše prostředků clusteru Průzkumník dat Azure.

1. Vyberte svůj cluster Kusto na webu Azure Portal.

    ![Nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings.png)

1. Vyberte **nastavení diagnostiky** v levé nabídce.
1. Klikněte na tlačítko **zapnout diagnostiku** odkaz. **Nastavení diagnostiky** otevře se okno.

    ![Okno nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings-window.png)

1. V **nastavení diagnostiky** podokna:
    1. Název diagnostické údaje protokolu: *ADXExportedData*
    1. Vyberte **AllMetrics** zaškrtávacího políčka (volitelné).
    1. Vyberte **Stream do centra událostí** zaškrtávací políčko.
    1. Klikněte na tlačítko **konfigurace**

1. V **centra událostí vyberte** podokně nakonfigurovat export do centra událostí, které jste vytvořili:
    1. **Vyberte obor názvů centra událostí** *AzureMonitoringData* z rozevíracího seznamu.
    1. **Název centra událostí vyberte** *diagnosticlogsdata* z rozevíracího seznamu.
    1. **Název zásady centra událostí vyberte** z rozevíracího seznamu.
    1. Klikněte na **OK**.

1. Klikněte na **Uložit**. Obor názvů centra událostí, název a název zásady se zobrazí v okně.

    ![Uložit nastavení diagnostiky](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Protokoly aktivit do centra událostí připojení

1. V levé nabídce portálu Azure Portal, vyberte **protokolu aktivit**
1. **Protokol aktivit** otevře se okno. **Klikněte na Exportovat do centra událostí**

    ![Protokol aktivit](media/ingest-data-no-code/activity-log.png)

1. V **exportovat protokol aktivit** okno:
 
    ![Exportovat protokol aktivit](media/ingest-data-no-code/export-activity-log.png)

    1. Vyberte své předplatné.
    1. V **oblastech** rozevíracím seznamu zvolte **Vybrat vše**
    1. Vyberte **exportovat do centra událostí** zaškrtávací políčko.
    1. Klikněte na **vyberte obor názvů služby Service bus** otevřít **centra událostí vyberte** podokně.
    1. V **centra událostí vyberte** podokně, vyberte z rozevíracích nabídek: vašeho předplatného, váš obor názvů služby event *AzureMonitoringData*a výchozí název zásady centra událostí.
    1. Klikněte na **OK**.
    1. Klikněte na tlačítko **Uložit** v pravé horní části okna. Centrum událostí s názvem *insights-operational-logs* se vytvoří.

### <a name="see-data-flowing-to-your-event-hubs"></a>Zobrazení dat odesílaných do služby Event Hubs

1. Počkejte několik minut, dokud připojení se definuje a dokončit export protokolu aktivit do centra událostí. Přejděte do svého oboru názvů centra událostí do služby event hubs, kterou jste vytvořili v tématu.

    ![Vytvoření služby Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Zobrazení dat odesílaných do vašeho centra událostí:

    ![Event Hubs data](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Připojení centra událostí do Průzkumníku dat Azure

### <a name="diagnostic-logs-data-connection"></a>Diagnostické protokoly datové připojení

1. V Průzkumníku dat Azure clusteru *kustodocs*vyberte **databází** v levé nabídce.
1. V **databází** okna, vyberte název vaší databáze *AzureMonitoring*
1. V nabídce vlevo vyberte **příjem dat**
1. V **ingestování** okna, klikněte na tlačítko **+ přidat datové připojení**
1. V **datové připojení** okno, zadejte následující informace:

    ![Připojení centra událostí](media/ingest-data-no-code/event-hub-data-connection.png)

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *DiagnosticsLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | Obor názvů centra událostí | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | Centrum událostí | *diagnosticlogsdata* | Centrum událostí, které jste vytvořili |
    | Skupina uživatelů | *adxpipeline* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. Pro účely tohoto kurzu použijte statického směrování (výchozí), ve kterém zadáte název tabulky, formátu a mapování. Proto nechte **Moje data zahrnují informace o směrování** nezaškrtnuté.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *DiagnosticLogsRecords* | V tabulce, kterou jste vytvořili v *AzureMonitoring* databáze. |
    | Formát dat | *JSON* | Formátování v tabulce. |
    | Mapování sloupců | *DiagnosticLogsRecordsMapping* | Mapování, kterou jste vytvořili v *AzureMonitoring* databáze, který mapuje příchozí data JSON na sloupec názvy a datové typy *DiagnosticLogsRecords*.|
    | | |

1. Klikněte na **Vytvořit**  

### <a name="activity-logs-data-connection"></a>Připojení dat protokolů aktivit

Opakujte kroky v [diagnostické protokoly datové připojení](#diagnostic-logs-data-connection) část, která vytvoří aktivita se protokoluje datové připojení.

1. Vložit na následující nastavení **datové připojení** okno:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *ActivityLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | Obor názvů centra událostí | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | Centrum událostí | *insights-operational-logs* | Centrum událostí, které jste vytvořili |
    | Skupina uživatelů | *$Default* | Výchozí skupinu příjemců. V případě potřeby můžete vytvořit skupinu jiný příjemce. |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. Pro účely tohoto kurzu použijte statického směrování (výchozí), ve kterém zadáte název tabulky, formátu a mapování. Proto nechte **Moje data zahrnují informace o směrování** nezaškrtnuté.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *ActivityLogsRawRecords* | V tabulce, kterou jste vytvořili v *AzureMonitoring* databáze. |
    | Formát dat | *JSON* | Formátování v tabulce. |
    | Mapování sloupců | *ActivityLogsRawRecordsMapping* | Mapování, kterou jste vytvořili v *AzureMonitoring* databáze, který mapuje příchozí data JSON na sloupec názvy a datové typy *ActivityLogsRawRecords*.|
    | | |

1. Klikněte na **Vytvořit**  

## <a name="query-the-new-tables"></a>Dotazování nové tabulky

Máte kanál s daty toku. Ingestování prostřednictvím clusteru trvá přibližně 5 minut, ve výchozím nastavení, proto povoluje datový tok několik minut, než začnete dotazu.

### <a name="diagnostic-logs-table-query-example"></a>Příklad dotazu tabulky diagnostické protokoly

Následující dotaz analyzuje data doby trvání dotazu z Průzkumníka služby Azure Data diagnostický protokol záznamů:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Výsledky dotazu:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>Příklad dotazu tabulky protokolů aktivit

Následující dotaz analyzuje data ze záznamů protokolu aktivit Průzkumník dat Azure:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Výsledky dotazu:
|   |   |
| --- | --- |
|   |  AVG(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Další postup

Naučte se psát mnoho další dotazy na data, která jste rozbalili ze Průzkumník dat Azure pomocí v následujícím článku:

> [!div class="nextstepaction"]
> [Psát dotazy pro Průzkumník dat Azure](write-queries.md)
