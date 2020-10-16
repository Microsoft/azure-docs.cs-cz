---
title: Zobrazení diagnostických protokolů pro Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Vysvětlení, jak nastavit a přistupovat k diagnostickým protokolům pro Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 4476e20772c0736f35c074b200ea9fd47a0ae81c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109166"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Přístup k diagnostickým protokolům pro Azure Data Lake Storage Gen1
Naučte se, jak povolit protokolování diagnostiky pro váš účet Azure Data Lake Storage Gen1 a jak zobrazit protokoly shromážděné pro váš účet.

Organizace můžou povolit protokolování diagnostiky pro svůj účet Azure Data Lake Storage Gen1, aby mohli shromažďovat záznamy pro audit přístupu k datům, jako jsou například seznamy uživatelů, kteří přistupují k datům, jak často se k datům přistupuje, kolik dat se v účtu ukládá atd. Pokud je tato možnost povolená, jsou diagnostické a/nebo požadavky protokolovány co nejvíc. Obě žádosti a položky protokolu diagnostiky se vytvoří jenom v případě, že se u koncového bodu služby nastavily požadavky.

## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Povolení protokolování diagnostiky pro váš účet Data Lake Storage Gen1
1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Otevřete účet Data Lake Storage Gen1 a v okně Data Lake Storage Gen1 účtu klikněte na **nastavení diagnostiky**.
3. V okně **nastavení diagnostiky** klikněte na **zapnout diagnostiku**.

    ![Snímek obrazovky s Data Lake Storage účet 1. generace s možností nastavení diagnostiky a možností zapnout diagnostiku s názvem.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Povolení diagnostických protokolů")

3. V okně **nastavení diagnostiky** proveďte následující změny pro konfiguraci protokolování diagnostiky.
   
    ![Snímek obrazovky s oddílem nastavení diagnostiky s textovým polem název a možností Uložit s názvem.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Povolení diagnostických protokolů")
   
   * Do pole **název**zadejte hodnotu Konfigurace diagnostického protokolu.
   * Můžete si vybrat, že chcete data ukládat a zpracovávat různými způsoby.
     
        * Vyberte možnost **archivovat do účtu úložiště** pro ukládání protokolů do účtu Azure Storage. Tuto možnost použijete, pokud chcete archivovat data, která budou zpracována dávkově za pozdější datum. Pokud vyberete tuto možnost, musíte zadat účet Azure Storage, do kterého se budou ukládat protokoly.
        
        * Vyberte možnost **streamování do centra událostí** pro streamování dat protokolu do centra událostí Azure. Tato možnost bude pravděpodobně použita, pokud máte k analýze příchozích protokolů v reálném čase kanál pro zpracování dat. Pokud vyberete tuto možnost, musíte zadat podrobnosti o centru událostí Azure, které chcete použít.

        * Vyberte možnost **odeslání Log Analytics** k použití služby Azure monitor k analýze generovaných dat protokolu. Pokud vyberete tuto možnost, musíte zadat podrobné informace o pracovním prostoru Log Analytics, které byste použili při analýze protokolu. Podrobnosti o používání protokolů Azure Monitor najdete v tématu [zobrazení nebo analýza dat shromážděných pomocí protokolu Azure monitor](../azure-monitor/log-query/get-started-portal.md) .
     
   * Určete, zda chcete získat protokoly auditu nebo protokoly žádostí nebo obojí.
   * Zadejte počet dní, po které musí být data uchována. Uchování se dá použít jenom v případě, že k archivaci dat protokolu používáte účet Azure Storage.
   * Klikněte na **Uložit**.

Po povolení nastavení diagnostiky můžete sledovat protokoly na kartě **diagnostické protokoly** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Zobrazit protokoly diagnostiky pro váš účet Data Lake Storage Gen1
Existují dva způsoby, jak zobrazit data protokolu pro váš Data Lake Storage Gen1 účet.

* V zobrazení nastavení účtu Data Lake Storage Gen1
* Z účtu Azure Storage, kde jsou data uložená

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Použití zobrazení nastavení Data Lake Storage Gen1
1. V okně **Nastavení** účtu Data Lake Storage Gen1 klikněte na **diagnostické protokoly**.
   
    ![Zobrazit diagnostické protokoly](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Zobrazit diagnostické protokoly") 
2. V okně **protokoly diagnostiky** byste měli vidět protokoly v kategoriích **auditu** a protokoly **žádostí**.
   
   * Protokoly žádostí zaznamenávají všechny požadavky rozhraní API provedené na účtu Data Lake Storage Gen1.
   * Protokoly auditu jsou podobné protokolům požadavků, ale poskytují mnohem podrobnější rozpis operací prováděných s účtem Data Lake Storage Gen1. Například jedno volání rozhraní API pro nahrání v protokolech požadavků může mít za následek vícenásobné operace "připojit" v protokolech auditu.
3. Protokoly stáhnete tak, že kliknete na odkaz **ke stažení** u každé položky protokolu.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z účtu Azure Storage, který obsahuje data protokolu
1. Otevřete okno Azure Storage účtu přidruženého k Data Lake Storage Gen1 pro protokolování a potom klikněte na objekty blob. Okno **BLOB Service** uvádí dva kontejnery.
   
    ![Snímek obrazovky okna Data Lake Storage Gen 1 – vybraná možnost objekty BLOB a okno služby blogu s názvy dvou služby BLOB Service, které jsou vyvolány.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Zobrazit diagnostické protokoly")
   
   * Kontejner **Insights-logs-audit** obsahuje protokoly auditu.
   * Kontejner **Insights-logs – požadavky** obsahují protokoly žádostí.
2. V rámci těchto kontejnerů jsou protokoly uloženy v následující struktuře.
   
    ![Snímek obrazovky se strukturou protokolů, jak je uložen v kontejneru.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Zobrazit diagnostické protokoly")
   
    Příkladem může být úplná cesta k protokolu auditu. `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobně platí, že úplná cesta k protokolu žádostí může být `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Pochopení struktury dat protokolu
Protokoly auditu a požadavků jsou ve formátu JSON. V této části se podíváme na strukturu formátu JSON pro žádosti a protokoly auditu.

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
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schéma protokolu žádostí
| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |ID prostředku, na kterém byla operace provedena |
| category |Řetězec |Kategorie protokolu Například **požadavky**. |
| operationName |Řetězec |Název operace, která se zaznamená do protokolu. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta, který vytváří požadavek |
| correlationId |Řetězec |ID protokolu, který se dá použít k seskupení sady souvisejících položek protokolu |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti najdete níže. |

#### <a name="request-log-properties-schema"></a>Vlastnosti protokolu žádosti – schéma
| Název | Typ | Popis |
| --- | --- | --- |
| HttpMethod |Řetězec |Metoda HTTP použitá pro operaci Například GET. |
| Cesta |Řetězec |Cesta, na které byla operace provedena |
| RequestContentLength |int |Délka obsahu požadavku HTTP |
| ID žádosti klienta |Řetězec |ID, které jedinečně identifikuje tento požadavek |
| StartTime |Řetězec |Čas přijetí žádosti serverem |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu
Zde je ukázkový záznam v protokolu auditu ve formátu JSON. Každý objekt BLOB má jeden kořenový objekt nazvaný **záznam** , který obsahuje pole objektů log.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Schéma protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |ID prostředku, na kterém byla operace provedena |
| category |Řetězec |Kategorie protokolu Například **audit**. |
| operationName |Řetězec |Název operace, která se zaznamená do protokolu. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| resultSignature |Řetězec |Další podrobnosti o operaci. |
| correlationId |Řetězec |ID protokolu, který se dá použít k seskupení sady souvisejících položek protokolu |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti najdete níže. |

#### <a name="audit-log-properties-schema"></a>Schéma vlastností protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| StreamName |Řetězec |Cesta, na které byla operace provedena |

## <a name="samples-to-process-the-log-data"></a>Ukázky pro zpracování dat protokolu
Při odesílání protokolů z Azure Data Lake Storage Gen1 do protokolů Azure Monitor (podrobnosti o používání protokolů Azure Monitor naleznete v tématu [zobrazení nebo analýza dat shromážděných pomocí protokolu Azure monitor log](../azure-monitor/log-query/get-started-portal.md) ). následující dotaz vrátí tabulku obsahující seznam zobrazovaných uživatelských jmen, čas událostí a počet událostí pro čas události spolu s vizuálním grafem. Dá se snadno upravit, aby se zobrazil identifikátor GUID uživatele nebo jiné atributy:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 poskytuje ukázku, jak zpracovávat a analyzovat data protokolu. Ukázku najdete na adrese [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)