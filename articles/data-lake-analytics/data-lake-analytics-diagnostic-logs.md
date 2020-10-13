---
title: Povolení a zobrazení diagnostických protokolů pro Azure Data Lake Analytics
description: Vysvětlení způsobu nastavení a přístupu k diagnostickým protokolům pro Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: f1f4320f0bfb924883eb7ae4807dcb714cd89983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331926"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Přístup k protokolům diagnostiky pro Azure Data Lake Analytics

Protokolování diagnostiky umožňuje shromažďovat záznamy pro audit přístupu k datům. Tyto protokoly obsahují informace, jako například:

* Seznam uživatelů, kteří získali data.
* Jak často jsou k datům přistupovaná data.
* Kolik dat je uloženo v účtu.

## <a name="enable-logging"></a>Povolit protokolování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Otevřete účet Data Lake Analytics a v části __monitorování__ vyberte **diagnostické protokoly** . V dalším kroku vyberte __zapnout diagnostiku__.

    ![Snímek obrazovky zobrazující zvolenou akci diagnostické protokoly a možnost zapnout diagnostiku pro shromažďování následujících protokolů](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Z __nastavení diagnostiky__zadejte __název__ této konfigurace protokolování a pak vyberte možnosti protokolování.

    ![Zapnout diagnostiku pro shromažďování protokolů auditu a žádostí](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Povolení diagnostických protokolů")

   * Data můžete ukládat/zpracovávat třemi různými způsoby.

     * Vyberte možnost __archivovat do účtu úložiště__ a uložte protokoly v účtu úložiště Azure. Tuto možnost použijte, pokud chcete archivovat data. Pokud vyberete tuto možnost, musíte zadat účet služby Azure Storage, do kterého se budou protokoly ukládat.

     * Vyberte **Stream do centra událostí** pro streamování dat protokolu do centra událostí Azure. Tuto možnost použijte, pokud máte kanál pro zpracování pro příjem dat, který analyzuje příchozí protokoly v reálném čase. Pokud vyberete tuto možnost, musíte zadat podrobnosti o centru událostí Azure, které chcete použít.

     * Vyberte __Odeslat pro Log Analytics__ pro odeslání dat do služby Azure monitor. Tuto možnost použijte, pokud chcete protokoly Azure Monitor použít ke shromažďování a analýze protokolů.
   * Určete, zda chcete získat protokoly auditu nebo protokoly žádostí nebo obojí.  Protokol žádostí zachycuje všechny požadavky rozhraní API. Protokol auditu zaznamenává všechny operace, které jsou aktivovány touto žádostí rozhraní API.

   * Pro __archivaci do účtu úložiště__zadejte počet dní, po které se mají data uchovávat.

   * Klikněte na __Uložit__.

        > [!NOTE]
        > Před kliknutím na tlačítko __Uložit__ musíte vybrat buď možnost __archivovat do účtu úložiště__, __datový proud do centra událostí__ nebo __Odeslat Log Analytics__ .

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Použít účet Azure Storage, který obsahuje data protokolu

1. Chcete-li zobrazit kontejnery objektů blob, které uchovávají data protokolování, otevřete Azure Storage účet používaný pro Data Lake Analytics protokolování a potom klikněte na __objekty blob__.

   * Kontejner **Insights-logs-audit** obsahuje protokoly auditu.
   * Kontejner **Insights-logs – požadavky** obsahují protokoly žádostí.

2. V kontejnerech jsou protokoly uloženy v následující struktuře souborů:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > `##`Položky v cestě obsahují rok, měsíc, den a hodinu, v nichž byl protokol vytvořen. Data Lake Analytics každou hodinu vytvoří jeden soubor, takže `m=` vždycky obsahuje hodnotu `00` .

    Úplná cesta k protokolu auditu může být například:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Podobně platí, že úplná cesta k protokolu žádostí může být:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Struktura protokolu

Protokoly auditu a žádosti jsou ve strukturovaném formátu JSON.

### <a name="request-logs"></a>Protokoly žádostí

Zde je ukázkový záznam v protokolu žádostí ve formátu JSON. Každý objekt BLOB má jeden kořenový objekt nazvaný **záznam** , který obsahuje pole objektů log.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schéma protokolu žádostí

| Název | Typ | Description |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |Identifikátor prostředku, na kterém byla operace provedena |
| category |Řetězec |Kategorie protokolu Například **požadavky**. |
| operationName |Řetězec |Název operace, která se zaznamená do protokolu. Například GetAggregatedJobHistory. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta, který vytváří požadavek |
| correlationId |Řetězec |Identifikátor protokolu. Tato hodnota se dá použít k seskupení sady souvisejících položek protokolu. |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti najdete v další části (schéma žádostí o vlastnosti protokolu). |

#### <a name="request-log-properties-schema"></a>Vlastnosti protokolu žádosti – schéma

| Název | Typ | Description |
| --- | --- | --- |
| HttpMethod |Řetězec |Metoda HTTP použitá pro operaci Například GET. |
| Cesta |Řetězec |Cesta, na které byla operace provedena |
| RequestContentLength |int |Délka obsahu požadavku HTTP |
| ID žádosti klienta |Řetězec |Identifikátor, který jedinečně identifikuje tento požadavek |
| StartTime |Řetězec |Čas přijetí žádosti serverem |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu

Zde je ukázkový záznam v protokolu auditu ve formátu JSON. Každý objekt BLOB má jeden kořenový objekt nazvaný **záznam** , který obsahuje pole objektů log.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Schéma protokolu auditu

| Název | Typ | Description |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |Identifikátor prostředku, na kterém byla operace provedena |
| category |Řetězec |Kategorie protokolu Například **audit**. |
| operationName |Řetězec |Název operace, která se zaznamená do protokolu. Například JobSubmitted. |
| resultType |Řetězec |Dílčí stav pro stav úlohy (OperationName). |
| resultSignature |Řetězec |Další podrobnosti o stavu úlohy (OperationName). |
| identity |Řetězec |Uživatel, který požadoval operaci Například, susan@contoso.com. |
| properties |JSON |Podrobnosti najdete v další části (schéma vlastností protokolu auditu). |

> [!NOTE]
> hodnoty **ResultType** a **resultSignature** poskytují informace o výsledku operace a v případě dokončení operace obsahují pouze hodnotu. Například obsahují hodnotu pouze v případě, že hodnota **OperationName** obsahuje hodnotu **JobStarted** nebo **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schéma vlastností protokolu auditu

| Název | Typ | Description |
| --- | --- | --- |
| JobId |Řetězec |ID přiřazené k úloze |
| JobName |Řetězec |Název, který byl zadán pro úlohu |
| JobRunTime |Řetězec |Modul runtime použitý ke zpracování úlohy |
| SubmitTime |Řetězec |Čas (v UTC), kdy byla úloha odeslána |
| StartTime |Řetězec |Čas spuštění úlohy po odeslání (v UTC) |
| EndTime |Řetězec |Čas ukončení úlohy |
| Paralelismu |Řetězec |Počet jednotek Data Lake Analytics požadovaných pro tuto úlohu při odeslání |

> [!NOTE]
> **SubmitTime**, **čas_spuštění**, **čas_ukončení**a **paralelismus** poskytují informace o operaci. Tyto položky obsahují hodnotu pouze v případě, že operace byla spuštěna nebo dokončena. Například **SubmitTime** obsahuje hodnotu **JobSubmitted**pouze po hodnotě **OperationName** .

## <a name="process-the-log-data"></a>Zpracování dat protokolu

Azure Data Lake Analytics poskytuje ukázku, jak zpracovávat a analyzovat data protokolu. Ukázku najdete na adrese [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Další kroky

[Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
