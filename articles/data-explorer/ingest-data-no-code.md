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
ms.openlocfilehash: c171962fd6177a01afdb8e9605b09574c99f485e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889218"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Kurz: Příjem dat v Průzkumníku dat Azure bez jeden řádek kódu

V tomto kurzu se dozvíte, jak k ingestování dat z diagnostiky a protokolů aktivit do Průzkumníku dat Azure clusteru bez psaní kódu. Pomocí této metody jednoduché ingestování můžete rychle zahájit, dotazování Průzkumníka služby Azure Data pro analýzu dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte tabulky a příjmu mapování v databázi aplikace Průzkumník dat Azure.
> * Formátování hodnot zpracovaných dat pomocí zásad pro aktualizaci.
> * Vytvoření [centra událostí](/azure/event-hubs/event-hubs-about) a připojte ho do Průzkumníku dat Azure.
> * Stream data do centra událostí z [diagnostické protokoly Azure monitoru](/azure/azure-monitor/platform/diagnostic-logs-overview) a [protokolů aktivit Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Dotaz na přijaté data pomocí Průzkumníku dat Azure.

> [!NOTE]
> Vytvořte všechny prostředky ve stejném umístění Azure nebo oblasti. Jde o požadavek pro diagnostické protokoly Azure monitoru.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Průzkumník dat Azure clusteru a databáze služby](create-cluster-database-portal.md). V tomto kurzu je název databáze *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Zprostředkovatel dat Azure Monitor: Diagnostika a protokolů aktivit

Zobrazení a pochopení dat v protokolech diagnostiky a aktivit Azure Monitor k dispozici. Vytvoříme kanál ingestování založené na tato data schémata.

### <a name="diagnostic-logs-example"></a>Příklad diagnostických protokolů

Diagnostické protokoly Azure jsou metriky, protože ho vygeneroval služby Azure, které poskytují informace o fungování dané služby. Data je agregovat s časovým intervalem 1 minuta. Každá událost v protokolu diagnostiky obsahuje jeden záznam. Tady je příklad schématu událostí metrika Průzkumník dat Azure na dobu trvání dotazu:

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

Protokoly aktivit v Azure jsou úroveň předplatného protokolů, které obsahují sadu záznamů. Protokoly poskytují přehled o tom, operace prováděné s prostředky ve vašem předplatném. Na rozdíl od diagnostické protokoly každá událost v protokolu aktivit obsahuje celou řadu záznamů. Potřebujeme k rozdělení toto pole záznamy v pozdější části kurzu. Tady je příklad události protokolu aktivit pro kontrolu přístupu:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Vytvořit kanál ingestování v Průzkumníku dat Azure

Nastavení kanál Průzkumník dat Azure zahrnuje několik kroků, jako například [ingestování vytváření a data tabulky](/azure/data-explorer/ingest-sample-data#ingest-data). Můžete také manipulovat s, mapování a aktualizovat data.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Připojit se k webu Průzkumník dat Azure uživatelského rozhraní

V Průzkumníku dat Azure *TestDatabase* databáze, vyberte **dotazu** otevřete uživatelské rozhraní služby Azure Data Explorer Web.

![Stránka dotazu](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Vytvoření cílové tabulky

Vytvoření cílové tabulky v databázi Průzkumníka služby Azure Data pomocí Azure dat Explorer webového uživatelského rozhraní.

#### <a name="the-diagnostic-logs-table"></a>Diagnostické protokoly tabulky

1. V *TestDatabase* databáze, vytvořte tabulku s názvem *DiagnosticLogsRecords* k ukládání diagnostických protokolů záznamů. Pomocí následujících `.create table` řídit příkaz:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Vyberte **spustit** k vytvoření této tabulky.

    ![Spuštění dotazu](media/ingest-data-no-code/run-query.png)

#### <a name="the-activity-logs-tables"></a>Tabulky protokoly aktivit.

Vzhledem k tomu, že struktura protokoly aktivit se tabulkový, budete potřebovat k manipulaci s daty a rozšiřovat každá událost a jeden nebo více záznamů. Nezpracovaná data se ingestují do převodní tabulky s názvem *ActivityLogsRawRecords*. Data budou v té době manipulovat a rozšířit. Rozšířená data se potom být přijímána *ActivityLogsRecords* tabulky pomocí zásady aktualizace. To znamená, že bude nutné k vytvoření dvou samostatných tabulek pro příjem protokolů aktivit.

1. Vytvořte tabulku s názvem *ActivityLogsRecords* v *TestDatabase* databáze pro příjem záznamů protokolu aktivit. K vytvoření této tabulky, spusťte následující dotaz Průzkumník dat Azure:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Vytváření dočasných dat tabulku s názvem *ActivityLogsRawRecords* v *TestDatabase* databáze pro manipulaci s daty:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Vytvoření mapování tabulek

 Vzhledem k tomu, že formát dat je `json`, data mapování je povinný. `json` Mapování každá cesta json se mapuje na název sloupce tabulky.

#### <a name="table-mapping-for-diagnostic-logs"></a>Mapování tabulek pro diagnostické protokoly

Pokud chcete namapovat diagnostické protokoly dat do tabulky, použijte tento dotaz:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Mapování tabulek pro protokoly aktivit

K mapování dat protokolů aktivit do tabulky, použijte následující dotaz:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-activity-logs-data"></a>Vytvořit zásady aktualizace pro data protokolů aktivit

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

2. Přidat [aktualizovat zásady](/azure/kusto/concepts/updatepolicy) do cílové tabulky. Tato zásada se automaticky spustí dotaz na všechny nově přijatých dat v *ActivityLogsRawRecords* dočasných dat tabulky a ingestovat jeho výsledky do *ActivityLogsRecords* tabulky:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Vytvoření oboru názvů Azure Event Hubs

Diagnostické protokoly Azure Povolit export metriky pro účet úložiště nebo do centra událostí. V tomto kurzu jsme bude směrovat metriky prostřednictvím centra událostí. V následujících krocích vytvoříte obor názvů služby Event Hubs a centra událostí pro diagnostické protokoly. Azure Monitor se vytvoří Centrum událostí *insights-operational-logs* protokoly aktivity.

1. Vytvoření centra událostí pomocí šablony Azure Resource Manageru na webu Azure Portal. Postupujte podle zbývajících kroků v tomto článku, klikněte pravým tlačítkem myši **nasadit do Azure** tlačítko a pak vyberte **otevřít v novém okně**. **Nasadit do Azure** tlačítko vás přesměruje na web Azure Portal.

    [![Tlačítko nasazení do Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Vytvořte obor názvů služby Event Hubs a centra událostí pro diagnostické protokoly.

    ![Vytvoření centra událostí](media/ingest-data-no-code/event-hub.png)

1. Do formuláře zadejte následující informace. Nastavení nejsou uvedené v následující tabulce použijte výchozí hodnoty.

    **Nastavení** | **Navrhovaná hodnota** | **Popis**
    |---|---|---|
    | **Předplatné** | *Vaše předplatné* | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | **Skupina prostředků** | *test-resource-group* | Vytvořte novou skupinu prostředků. |
    | **Umístění** | Vyberte oblast, která nejlépe vyhovuje vašim potřebám. | Vytvoření oboru názvů služby Event Hubs ve stejném umístění jako ostatní prostředky.
    | **Název Namespace** | *AzureMonitoringData* | Zvolte jedinečný název, který identifikuje váš obor názvů.
    | **Název centra událostí** | *DiagnosticLogsData* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. |
    | **Název skupiny uživatelů** | *adxpipeline* | Vytvořte název skupiny příjemců. Skupiny uživatelů umožňují, aby měla každá z aplikací samostatné zobrazení streamu událostí. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Připojení Azure Monitor protokoly do vašeho centra událostí

Teď je potřeba připojit diagnostických protokolů a protokolů aktivit do centra událostí.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Připojit diagnostické protokoly do vašeho centra událostí

Vyberte zdroj, ze kterého se má exportovat metriky. Několik typů prostředků podporovala export diagnostické protokoly, včetně oboru názvů Event Hubs, Azure Key Vault, Azure IoT Hub a clustery Průzkumník dat Azure. V tomto kurzu používáme cluster Průzkumník dat Azure jako naše prostředek.

1. Vyberte svůj cluster Kusto na webu Azure Portal.
1. Vyberte **nastavení diagnostiky**a pak vyberte **zapnout diagnostiku** odkaz. 

    ![Nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings.png)

1. **Nastavení diagnostiky** se otevře podokno. Proveďte následující kroky:
    1. Poskytnout diagnostické údaje protokolu název *ADXExportedData*.
    1. V části **METRIKA**, vyberte **AllMetrics** (volitelné) zaškrtněte políčko.
    1. Vyberte **Stream do centra událostí** zaškrtávací políčko.
    1. Vyberte **nakonfigurovat**.

    ![Podokno nastavení diagnostiky](media/ingest-data-no-code/diagnostic-settings-window.png)

1. V **centra událostí vyberte** podokně nakonfigurovat jak exportovat data z diagnostické protokoly do centra událostí, které jste vytvořili:
    1. V **vyberte obor názvů centra událostí** vyberte *AzureMonitoringData*.
    1. V **název centra událostí vyberte** vyberte *diagnosticlogsdata*.
    1. V **název zásady centra událostí vyberte** vyberte **RootManagerSharedAccessKey**.
    1. Vyberte **OK**.

1. Vyberte **Uložit**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Připojte protokoly aktivit se do vašeho centra událostí

1. V nabídce vlevo na webu Azure portal vyberte **protokolu aktivit**.
1. **Protokolu aktivit** otevře se okno. Vyberte **exportovat do centra událostí**.

    ![Okno protokoly aktivit](media/ingest-data-no-code/activity-log.png)

1. **Exportovat protokol aktivit** otevře se okno:
 
    ![Export okno protokoly aktivit](media/ingest-data-no-code/export-activity-log.png)

1. V **exportovat protokol aktivit** okno, proveďte následující kroky:
      1. Vyberte své předplatné.
      1. V **oblastech** klikněte na položku **Vybrat vše**.
      1. Vyberte **exportovat do centra událostí** zaškrtávací políčko.
      1. Zvolte **vyberte obor názvů služby Service bus** otevřít **centra událostí vyberte** podokně.
      1. V **centra událostí vyberte** podokně, vyberte své předplatné.
      1. V **vyberte obor názvů centra událostí** vyberte *AzureMonitoringData*.
      1. V **název zásady centra událostí vyberte** vyberte výchozí název zásady centra událostí.
      1. Vyberte **OK**.
      1. V levém horním rohu okna, vyberte **Uložit**.
   Centrum událostí s názvem *insights-operational-logs* se vytvoří.

### <a name="see-data-flowing-to-your-event-hubs"></a>Zobrazení dat odesílaných do služby event hubs

1. Počkejte několik minut, než je definována připojení a dokončení exportu protokolu aktivit do centra událostí. Přejděte do svého oboru názvů služby Event Hubs do služby event hubs, kterou jste vytvořili v tématu.

    ![Vytvoření služby Event hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Zobrazení dat odesílaných do vašeho centra událostí:

    ![Datové centrum událostí](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Připojení centra událostí do Průzkumníku dat Azure

Teď je potřeba vytvořit datová připojení pro diagnostické protokoly a protokoly aktivit.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Vytvoření datového připojení pro diagnostické protokoly

1. Ve vašem clusteru Průzkumník dat Azure s názvem *kustodocs*vyberte **databází** v levé nabídce.
1. V **databází** okně vaše *TestDatabase* databáze.
1. V nabídce vlevo vyberte **ingestování**.
1. V **ingestování** okna, klikněte na tlačítko **+ přidat datové připojení**.
1. V **datové připojení** okno, zadejte následující informace:

    ![Připojení datového centra událostí](media/ingest-data-no-code/event-hub-data-connection.png)

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *DiagnosticsLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centra událostí** | *diagnosticlogsdata* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *adxpipeline* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. Pro účely tohoto kurzu budete používat statického směrování (výchozí), ve kterém zadáte název tabulky, formát data a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Tabulka** | *DiagnosticLogsRecords* | V tabulce, kterou jste vytvořili v *TestDatabase* databáze. |
    | **Formát dat** | *JSON* | Formát používaný v tabulce. |
    | **Mapování sloupců** | *DiagnosticLogsRecordsMapping* | Mapování, kterou jste vytvořili v *TestDatabase* databáze, který mapuje příchozí data JSON na typy sloupců názvy a datové sady *DiagnosticLogsRecords* tabulky.|
    | | |

1. Vyberte **Vytvořit**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Vytvoření datového připojení pro protokoly aktivit

Opakujte kroky v části Vytvoření datového připojení pro diagnostické protokoly část vytvoření datového připojení pro vaše protokoly aktivit.

1. Použijte následující nastavení v **datové připojení** okno:

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Název datového připojení** | *ActivityLogsConnection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | **Obor názvů centra událostí** | *AzureMonitoringData* | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | **Centra událostí** | *insights-operational-logs* | Centrum událostí, které jste vytvořili |
    | **Skupina uživatelů** | *$Default* | Výchozí skupinu příjemců. V případě potřeby můžete vytvořit skupinu jiný příjemce. |
    | | |

    Cílová tabulka:

    Existují dvě možnosti směrování: *statické* a *dynamické*. Pro účely tohoto kurzu budete používat statického směrování (výchozí), ve kterém zadáte název tabulky, formát data a mapování. Možnost **Moje data zahrnují informace o směrování** ponechte nevybranou.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | **Tabulka** | *ActivityLogsRawRecords* | V tabulce, kterou jste vytvořili v *TestDatabase* databáze. |
    | **Formát dat** | *JSON* | Formát používaný v tabulce. |
    | **Mapování sloupců** | *ActivityLogsRawRecordsMapping* | Mapování, kterou jste vytvořili v *TestDatabase* databáze, který mapuje příchozí data JSON na typy sloupců názvy a datové sady *ActivityLogsRawRecords* tabulky.|
    | | |

1. Vyberte **Vytvořit**.  

## <a name="query-the-new-tables"></a>Dotazování nové tabulky

Nyní máte kanál s daty toku. Ingestování prostřednictvím clusteru trvá přibližně 5 minut ve výchozím nastavení, takže povolit datový tok několik minut před zahájením pro dotazování.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Příklad dotazu na tabulku diagnostické protokoly

Následující dotaz analyzuje data doby trvání dotazu z diagnostický protokol záznamů v Průzkumníku dat Azure:

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

### <a name="an-example-of-querying-the-activity-logs-table"></a>Příklad dotazování tabulky protokolů aktivit

Následující dotaz analyzuje data ze záznamů protokolu aktivit v Průzkumníku dat Azure:

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

Naučte se psát mnoho další dotazy na data, která jste rozbalili ze Průzkumník dat Azure s použitím v následujícím článku:

> [!div class="nextstepaction"]
> [Psaní dotazů pro Azure Data Explorer](write-queries.md)
