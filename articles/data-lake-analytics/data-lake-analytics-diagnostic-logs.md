---
title: Povolení a zobrazení diagnostických protokolů pro Azure Data Lake Analytics
description: Pochopit, jak nastavit diagnostické protokoly pro Azure Data Lake Analytics a získat k nim přístup
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60616487"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Přístup k protokolům diagnostiky pro Azure Data Lake Analytics

Diagnostické protokolování umožňuje shromažďovat stopy auditu přístupu k datům. Tyto protokoly poskytují informace, jako jsou:

* Seznam uživatelů, kteří přistupovali k datům.
* Jak často jsou data přístupná.
* Kolik dat je uloženo v účtu.

## <a name="enable-logging"></a>Povolit protokolování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Otevřete svůj účet Data Lake Analytics a v části __Monitor__ vyberte **Diagnostické protokoly.** Dále vyberte __Zapnout diagnostiku__.

    ![Zapnutí diagnostiky pro shromažďování protokolů auditu a požadavků](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. V __části Nastavení diagnostiky__zadejte __název__ pro tuto konfiguraci protokolování a vyberte možnosti protokolování.

    ![Zapnutí diagnostiky pro shromažďování protokolů auditu a požadavků](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Povolení diagnostických protokolů")

   * Můžete se rozhodnout ukládat/zpracovávat data třemi různými způsoby.

     * Vyberte __Archivovat do účtu úložiště__ pro ukládání protokolů v účtu úložiště Azure. Tuto možnost použijte, pokud chcete data archivovat. Pokud vyberete tuto možnost, musíte zadat účet úložiště Azure pro uložení protokolů.

     * Vyberte **Stream do centra událostí,** chcete-li streamovat data protokolu do Centra událostí Azure. Tuto možnost použijte, pokud máte kanál následného zpracování, který analyzuje příchozí protokoly v reálném čase. Pokud vyberete tuto možnost, musíte zadat podrobnosti pro Azure Event Hub, který chcete použít.

     * Chcete-li odeslat data do služby Azure Monitor, vyberte __odeslat do analýzy protokolů.__ Tuto možnost použijte, pokud chcete použít protokoly Azure Monitor ke shromažďování a analýze protokolů.
   * Určete, zda chcete získat protokoly auditu nebo protokoly požadavků nebo obojí.  Protokol požadavků zachytí každý požadavek rozhraní API. Protokol auditu zaznamenává všechny operace, které jsou spuštěny tímto požadavkem rozhraní API.

   * V __poli Archivovat do účtu úložiště__zadejte počet dní, po které mají být data uchována.

   * Klikněte na __Uložit__.

        > [!NOTE]
        > Před kliknutím na tlačítko __Uložit__ je musíte vybrat buď __Archivovat do účtu úložiště__, __Streamovat do centra událostí__ nebo __Odeslat do analýzy protokolů.__

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Použití účtu Azure Storage, který obsahuje data protokolu

1. Pokud chcete zobrazit kontejnery objektů blob, které uchovávají data protokolování, otevřete účet Azure Storage používaný pro Analýzu datového jezera pro protokolování a klikněte na __objekty BLOB__.

   * Kontejner **insights-protokoly-audit** obsahuje protokoly auditu.
   * Kontejner **insights protokoly protokoly** obsahuje protokoly požadavků.

2. V kontejnerech jsou protokoly uloženy pod následující strukturou souborů:

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
   > Položky `##` v cestě obsahují rok, měsíc, den a hodinu, ve kterých byl protokol vytvořen. Data Lake Analytics vytváří jeden `m=` soubor každou hodinu, takže vždy obsahuje hodnotu `00`.

    Jako příklad může být úplná cesta k protokolu auditu:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobně úplná cesta k protokolu požadavků může být:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura protokolu

Protokoly auditu a požadavků jsou ve strukturovaném formátu JSON.

### <a name="request-logs"></a>Protokoly požadavků

Zde je ukázková položka v protokolu požadavků ve formátu JSON. Každý objekt blob má jeden kořenový objekt nazývaný **záznamy,** který obsahuje pole objektů protokolu.

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

#### <a name="request-log-schema"></a>Vyžádat schéma protokolu

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |Identifikátor zdroje, na který se operace uskutečnila |
| category |Řetězec |Kategorie protokolu. Například **Požadavky**. |
| operationName |Řetězec |Název operace, která je protokolována. Například GetAggregatedJobHistory. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta, který žádost poslaje |
| correlationId |Řetězec |Identifikátor protokolu. Tuto hodnotu lze použít k seskupení sady souvisejících položek protokolu. |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti naleznete v další části (Schéma vlastností protokolu požadavku) |

#### <a name="request-log-properties-schema"></a>Vyžádat schéma vlastností protokolu

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| Metoda http |Řetězec |Metoda HTTP použitá pro operaci. Například GET. |
| Cesta |Řetězec |Cesta, po které byla operace provedena |
| Délka obsahu požadavku |int |Délka obsahu požadavku HTTP |
| Id klienta |Řetězec |Identifikátor, který jednoznačně identifikuje tento požadavek |
| StartTime |Řetězec |Čas, kdy server obdržel požadavek |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu

Tady je ukázková položka v protokolu auditu ve formátu JSON. Každý objekt blob má jeden kořenový objekt nazývaný **záznamy,** který obsahuje pole objektů protokolu.

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

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |Identifikátor zdroje, na který se operace uskutečnila |
| category |Řetězec |Kategorie protokolu. Například **Audit**. |
| operationName |Řetězec |Název operace, která je protokolována. Například JobSubmitted. |
| resultType |Řetězec |Dílčí stav stavu úlohy (operationName). |
| resultSignature |Řetězec |Další podrobnosti o stavu úlohy (operationName). |
| identity |Řetězec |Uživatel, který požádal o operaci. Například, susan@contoso.com. |
| properties |JSON |Podrobnosti naleznete v další části (Schéma vlastností protokolu auditu). |

> [!NOTE]
> **resultType** a **resultSignature** poskytují informace o výsledku operace a obsahují hodnotu pouze v případě, že byla operace dokončena. Například obsahují hodnotu pouze v **případě, že operationName** obsahuje hodnotu **JobStarted** nebo **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schéma vlastností protokolu auditování

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| JobId |Řetězec |ID přiřazené k úloze |
| Název_pracovní pozice |Řetězec |Název, který byl poskytnut pro úlohu |
| JobRunTime |Řetězec |Doba běhu použitá ke zpracování úlohy |
| OdeslatČas |Řetězec |Čas (v UTC), kdy byla úloha odeslána |
| StartTime |Řetězec |Čas spuštění úlohy po odeslání (v utc) |
| EndTime |Řetězec |Čas ukončení úlohy |
| Paralelnost |Řetězec |Počet jednotek Data Lake Analytics požadovaných pro tuto úlohu během odesílání |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**a **Parallelism** poskytují informace o operaci. Tyto položky obsahují hodnotu pouze v případě, že tato operace byla zahájena nebo dokončena. Například **SubmitTime** obsahuje pouze hodnotu po **operationName** má hodnotu **JobSubmitted**.

## <a name="process-the-log-data"></a>Zpracování dat protokolu

Azure Data Lake Analytics poskytuje ukázku zpracování a analýzy dat protokolu. Vzorek najdete na [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)adrese .

## <a name="next-steps"></a>Další kroky
* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
