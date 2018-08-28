---
title: Povolení a zobrazení diagnostických protokolů pro Azure Data Lake Analytics
description: Naučte se nastavit a získat přístup k protokolům diagnostiky pro Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 0bade9f393d879123b7b1485052f70924d9c9b9c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045477"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Přístup k protokolům diagnostiky pro Azure Data Lake Analytics

Protokolování diagnostiky umožňuje shromažďovat záznamy pro audit dat přístup. Tyto protokoly obsahují informace jako například:

* Seznam uživatelů, kteří používaná data.
* Jak často data potřebujete.
* Jak velký objem dat uložený v účtu.

## <a name="enable-logging"></a>Povolit protokolování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Otevřete svůj účet Data Lake Analytics a vyberte **diagnostické protokoly** z __monitorování__ oddílu. V dalším kroku vyberte __zapnout diagnostiku__.

    ![Zapnout diagnostiku shromažďovat auditu a vyžádat protokoly](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Z __nastavení diagnostiky__, zadejte __název__ pro tuto konfiguraci protokolování a pak vyberte protokolování.

    ![Zapnout diagnostiku shromažďovat auditu a žádat o protokoly](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "povolení diagnostických protokolů")

   * Můžete se k obchodu a zpracovávat je třemi různými způsoby.

     * Vyberte __archivovat do účtu úložiště__ k ukládání protokolů v účtu služby Azure storage. Tuto možnost použijte, pokud chcete archivovat data. Pokud vyberete tuto možnost, musíte zadat účet služby Azure storage k uložení protokolů k.

     * Vyberte **Stream do centra událostí** data protokolu Streamovat do centra událostí Azure. Tuto možnost použijte, pokud máte kanál zpracování příjmu dat, který analyzuje příchozí protokoly v reálném čase. Pokud vyberete tuto možnost, musíte zadat podrobnosti pro Azure Event Hubs, kterou chcete použít.

     * Vyberte __odesílat do Log Analytics__ odesílat data do služby Log Analytics. Tuto možnost použijte, pokud chcete používat službu Log Analytics ke shromažďování a analýza protokolů.
   * Určete, jestli chcete získat protokoly auditu nebo žádost o protokoly nebo obojí.  Požadavek protokolu zaznamená každého požadavku rozhraní API. Protokol auditu zaznamenává všechny operace, které jsou aktivovány tuto žádost rozhraní API.

   * Pro __archivovat do účtu úložiště__, zadejte počet dnů uchování data.

   * Klikněte na __Uložit__.

        > [!NOTE]
        > Musíte vybrat buď __archivovat do účtu úložiště__, __Stream do centra událostí__ nebo __odesílat do Log Analytics__ před kliknutím na tlačítko __Uložit__ tlačítko.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Použijte účet služby Azure Storage, který obsahuje data protokolu

1. Kontejnery objektů blob, které obsahují data protokolování zobrazíte otevřít účet úložiště Azure používané pro Data Lake Analytics pro protokolování a pak klikněte na tlačítko __objekty BLOB__.

   * Kontejner **insights – protokoly auditu** obsahuje protokoly auditu.
   * Kontejner **insights protokoly žádosti** obsahuje protokoly požadavku.

2. V rámci kontejnerů protokoly se budou ukládat v rámci následující strukturu souborů:

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

   > [!NOTE]
   > `##` Položky v cestě obsahují rok, měsíc, den a hodina, ve kterém byla vytvořena v protokolu. Data Lake Analytics vytvoří jeden soubor každou hodinu, takže `m=` vždy obsahuje hodnotu `00`.

    Jako příklad může být úplná cesta k protokolu auditu:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobně může být úplná cesta k požadavku protokolu:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura protokolu

Protokoly auditu a požadavek se předává ve strukturovaném formátu JSON.

### <a name="request-logs"></a>Vyžádat protokoly

Zde je vstup vzorového v protokol žádosti ve formátu JSON. Každý objekt blob má jeden kořenový objekt volána **záznamy** , která obsahuje pole objektů protokolu.

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

#### <a name="request-log-schema"></a>Schéma požadavku protokolu

| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (ve standardu UTC) v protokolu |
| resourceId |Řetězec |Identifikátor prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **požadavky**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například GetAggregatedJobHistory. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresu klientovi provádějícímu žádost |
| correlationId |Řetězec |Identifikátor protokolu. Tato hodnota slouží k seskupení sady položek protokolu související. |
| identity |Objekt |Identita, která vygeneruje protokol |
| properties |JSON |Viz následující část (schéma vlastnosti požadavku protokolu) podrobnosti |

#### <a name="request-log-properties-schema"></a>Požadavek protokolu vlastnosti schématu

| Název | Typ | Popis |
| --- | --- | --- |
| Vlastnost HttpMethod |Řetězec |Metoda protokolu HTTP se používá pro operaci. Třeba získáte. |
| Cesta |Řetězec |Cesta operaci byla provedena |
| RequestContentLength |int |Délka obsahu žádosti HTTP |
| ID žádosti klienta |Řetězec |Identifikátor, který jednoznačně identifikuje tento požadavek |
| StartTime |Řetězec |Čas, kdy server přijal požadavek |
| EndTime |Řetězec |Čas, odeslání odpovědi serveru |

### <a name="audit-logs"></a>Protokoly auditu

Tady je vstup vzorového v protokolu auditu ve formátu JSON. Každý objekt blob má jeden kořenový objekt volána **záznamy** , která obsahuje pole objektů protokolu.

    {
    "records":
      [        
        . . . .
        ,
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
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schéma protokolu auditu

| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (ve standardu UTC) v protokolu |
| resourceId |Řetězec |Identifikátor prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **auditu**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například JobSubmitted. |
| resultType |Řetězec |Podřízený stav pro stav úlohy (operationName). |
| resultSignature |Řetězec |Další podrobnosti o stavu úlohy (operationName). |
| identity |Řetězec |Uživatel, který požadované operace. Například, susan@contoso.com. |
| properties |JSON |Viz následující část (schéma vlastnosti protokolu auditu) podrobnosti |

> [!NOTE]
> **Hodnota resultType** a **resultSignature** poskytují informace o výsledku operace a obsahovat pouze hodnotu, pokud je operace dokončená. Například pouze obsahovat hodnotu při **operationName** obsahuje hodnotu **JobStarted** nebo **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schéma vlastnosti protokolu auditu

| Název | Typ | Popis |
| --- | --- | --- |
| JobId |Řetězec |ID přiřazených k úloze |
| Název úlohy |Řetězec |Název, který byl k dispozici pro úlohu |
| JobRunTime |Řetězec |Modul runtime používaný ke zpracování úlohy. |
| SubmitTime |Řetězec |Čas (v UTC), který úloha byla odeslána. |
| StartTime |Řetězec |Při spuštění úlohy spuštění po odeslání (ve standardu UTC) |
| EndTime |Řetězec |Čas ukončení úlohy |
| Paralelismus |Řetězec |Počet jednotek Data Lake Analytics požadovaná pro tuto úlohu během odesílání |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, a **paralelismu** poskytují informace o operaci. Tyto položky pouze obsahovat hodnotu, pokud, které operace má zahájena nebo dokončena. Například **SubmitTime** obsahuje pouze hodnotu po **operationName** má hodnotu **JobSubmitted**.

## <a name="process-the-log-data"></a>Zpracování dat protokolu

Azure Data Lake Analytics najdete vzorový o tom, jak zpracovávat a analyzovat data protokolů. Můžete najít ukázce kódu na [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Další postup
* [Přehled služby Azure Data Lake Analytics](data-lake-analytics-overview.md)
