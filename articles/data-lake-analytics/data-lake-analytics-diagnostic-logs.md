---
title: Povolení a zobrazení diagnostických protokolů pro Azure Data Lake Analytics
description: Pochopit, jak nastavit a přístupu k diagnostickým protokolům pro Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: e65c6396d859a128777c66cad6a44bb033b50d50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623481"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Přístup k diagnostickým protokolům pro Azure Data Lake Analytics

Protokolování diagnostiky umožňuje shromažďovat záznamy auditu přístupu data. Tyto protokoly obsahují informace, jako:

* Seznam uživatelů, kteří přístup data.
* Jak často se data používají.
* Množství dat, které je uložený v účtu.

## <a name="enable-logging"></a>Povolit protokolování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Otevřete účet Data Lake Analytics a vyberte **diagnostické protokoly** z __monitorování__ části. Potom vyberte __zapněte diagnostiku__.

    ![Zapněte diagnostiku shromažďovat auditu a protokolů žádosti](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Z __nastavení diagnostiky__, zadejte __název__ pro tuto konfiguraci protokolování a pak vyberte položku protokolování možnosti.

    ![Zapněte diagnostiku shromažďovat auditu a protokolů žádosti](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "povolení diagnostických protokolů")

   * Můžete úložiště/zpracovat data třemi různými způsoby.

     * Vyberte __archivu do účtu úložiště__ k ukládání protokolů v účtu úložiště Azure. Tuto možnost použijte, pokud chcete archivovat data. Pokud vyberete tuto možnost, je nutné zadat účet úložiště Azure pro uložení protokolů.

     * Vyberte **datový proud do centra událostí** na datový proud protokolu data do centra událostí Azure. Tuto možnost použijte, pokud máte kanál zpracování příjmu dat, který analyzuje příchozí protokolů v reálném čase. Pokud vyberete tuto možnost, je nutné zadat podrobnosti pro centra událostí Azure, kterou chcete použít.

     * Vyberte __odeslat k analýze protokolů__ k odeslání dat službě Analýza protokolů. Tuto možnost použijte, pokud chcete použít k shromáždění a analýza protokolů analýzy protokolů.
   * Určete, jestli chcete získat protokoly auditu nebo protokoly požadavku nebo obě.  Žádost protokolu zaznamená každého požadavku rozhraní API. Protokol auditování zaznamenává všechny operace, které jsou aktivovány tohoto požadavku rozhraní API.

   * Pro __archivu do účtu úložiště__, zadejte počet dní, které chcete zachovat data.

   * Klikněte na __Uložit__.

        > [!NOTE]
        > Je nutné vybrat buď __archivu do účtu úložiště__, __datový proud do centra událostí__ nebo __odeslat k analýze protokolů__ před kliknutím na tlačítko __Uložit__ tlačítko.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Použít účet úložiště Azure, který obsahuje data protokolu

1. Pokud chcete zobrazit kontejnery objektů blob, které obsahují data protokolování, otevřete účet úložiště Azure pro Data Lake Analytics použitá pro protokolování a pak klikněte na tlačítko __objekty BLOB__.

   * Kontejner **přehledy. protokoly auditu** obsahující protokoly auditu.
   * Kontejner **přehledy. protokoly žádosti** obsahující protokoly požadavku.

2. V rámci kontejnerů protokoly jsou uloženy v následující soubor strukturu:

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

    Jako příklad může být úplná cesta k protokolu auditování:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Podobně může být úplná cesta k požadavku protokolu:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struktura protokolu

Protokoly auditu a žádosti o jsou v strukturovaného formátu JSON.

### <a name="request-logs"></a>Žádost o protokoly

Zde je vzorového vstupu do formátu JSON žádost protokolu. Každý objekt blob má jeden kořenový objekt názvem **záznamy** obsahující pole objektů protokolu.

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
| time |Řetězec |Časové razítko (ve formátu UTC) v protokolu |
| resourceId |Řetězec |Identifikátor prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **požadavky**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například GetAggregatedJobHistory. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta vytvoření požadavku |
| correlationId |Řetězec |Identifikátor protokolu. Tato hodnota slouží k seskupení sady položek související protokolu. |
| identity |Objekt |Identity, která generuje protokol |
| properties |JSON |Najdete v další části (požadavku protokolu vlastnosti schéma), podrobnosti |

#### <a name="request-log-properties-schema"></a>Schéma vlastnosti požadavku protokolu

| Název | Typ | Popis |
| --- | --- | --- |
| HttpMethod |Řetězec |Metoda HTTP se používá pro operaci. Například získáte. |
| Cesta |Řetězec |Cesta k operaci byla provedena na |
| RequestContentLength |celá čísla |Délka obsahu požadavku HTTP |
| clientRequestId |Řetězec |Identifikátor, který jedinečně identifikuje tuto žádost |
| StartTime |Řetězec |Čas, na které server přijal žádost |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu

Zde je vzorového vstupu v protokolu auditování formátu JSON. Každý objekt blob má jeden kořenový objekt názvem **záznamy** obsahující pole objektů protokolu.

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
| time |Řetězec |Časové razítko (ve formátu UTC) v protokolu |
| resourceId |Řetězec |Identifikátor prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **auditu**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například JobSubmitted. |
| resultType |Řetězec |Podřízený stav pro stav úlohy (operationName). |
| resultSignature |Řetězec |Další informace o stavu úlohy (operationName). |
| identity |Řetězec |Uživatel, který požadovanou operaci. Například, susan@contoso.com. |
| properties |JSON |Viz další část (schéma vlastnosti protokolu auditu) podrobností |

> [!NOTE]
> **resultType** a **resultSignature** poskytují informace o výsledku operace a obsahovat pouze hodnotu, pokud operace byla dokončena. Například pouze obsahovat hodnotu při **operationName** obsahuje hodnotu **JobStarted** nebo **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schéma vlastnosti protokolu auditu

| Název | Typ | Popis |
| --- | --- | --- |
| JobId |Řetězec |ID přiřazené úlohy |
| Název úlohy |Řetězec |Název, která byla poskytnuta pro úlohu |
| JobRunTime |Řetězec |Modul runtime používá ke zpracování úlohy |
| SubmitTime |Řetězec |Čas (v UTC), který úloha byla odeslána. |
| StartTime |Řetězec |Čas úlohy spuštění po odeslání (ve formátu UTC) |
| EndTime |Řetězec |Čas ukončení úkolu |
| Paralelismus |Řetězec |Počet jednotek Data Lake Analytics požadovaný pro tuto úlohu při odesílání |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, a **paralelismus** poskytují informace o operaci. Tyto položky pouze obsahovat hodnotu, pokud které operace má spustit nebo byla dokončena. Například **SubmitTime** pouze obsahuje hodnotu po **operationName** má hodnotu **JobSubmitted**.

## <a name="process-the-log-data"></a>Zpracování dat protokolu

Azure Data Lake Analytics poskytuje vzorku o tom, jak zpracovávat a analyzovat data protokolu. Můžete najít ukázku najdete na adrese [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Další postup
* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
