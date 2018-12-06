---
title: Zobrazení protokolů diagnostiky pro Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: 'Naučte se nastavit a získat přístup k protokolům diagnostiky pro Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 357257d38c444eae8077568993d49816e3c090a3
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966071"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Přístup k protokolům diagnostiky pro Azure Data Lake Storage Gen1
Zjistěte, jak povolit diagnostické protokolování pro váš účet Azure Data Lake Storage Gen1 a o tom, k zobrazení protokolů shromažďovaných pro váš účet.

Organizace může povolit protokolování diagnostiky ke svému účtu Azure Data Lake Storage Gen1 ke shromažďování záznamů pro audit přístupu k data, která poskytuje informace, jako je seznam uživatelům přístup k datům, jak často data potřebujete, jaká data se ukládají do účet, atd. Při povolení diagnostiky a/nebo žádosti o přihlášení snažíme maximálně vyhovět. Požadavky a Diagnostika položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Povolit protokolování diagnostiky pro svůj účet Data Lake Storage Gen1
1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Otevřete svůj účet Data Lake Storage Gen1 a okno účtu Data Lake Storage Gen1, klikněte na **nastavení diagnostiky**.
3. V **nastavení diagnostiky** okna, klikněte na tlačítko **zapnout diagnostiku**.

    ![Povolit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "povolení diagnostických protokolů")

3. V **nastavení diagnostiky** okno, proveďte následující změny konfigurace protokolování diagnostiky.
   
    ![Povolit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "povolení diagnostických protokolů")
   
   * Pro **název**, zadejte hodnotu pro konfiguraci diagnostických protokolů.
   * Můžete se k obchodu a zpracovávat je různými způsoby.
     
        * Vyberte možnost **archivovat do účtu úložiště** k ukládání protokolů pro účet služby Azure Storage. Tuto možnost použijte, pokud chcete archivovat data, která bude zpracována batch později. Pokud vyberete tuto možnost, je nutné zadat účet služby Azure Storage k uložení protokolů k.
        
        * Vyberte možnost **Stream do centra událostí** data protokolu Streamovat do centra událostí Azure. Pravděpodobně bude používat tuto možnost, pokud máte kanál zpracování příjmu dat k analýze příchozí protokolů v reálném čase. Pokud vyberete tuto možnost, musíte zadat podrobnosti pro Azure Event Hubs, kterou chcete použít.

        * Vyberte možnost **odesílat do Log Analytics** k používání služby Azure Log Analytics k analýze dat generovaných protokolu. Pokud vyberete tuto možnost, musíte zadat podrobnosti pracovního prostoru Log Analytics můžete využít provést analýzu protokolu. Zobrazit [zobrazení nebo analýza shromážděných dat pomocí prohledávání protokolu log Analytics](../azure-monitor/learn/tutorial-viewdata.md) podrobnosti o použití Log Analytics.
     
   * Určete, jestli chcete získat protokoly auditu nebo žádost o protokoly nebo obojí.
   * Zadejte počet dnů, u kterých musí uchovávat data. Uchovávání platí pouze pokud používáte účet úložiště Azure pro archivaci dat protokolu.
   * Klikněte na **Uložit**.

Po povolení diagnostických nastavení můžete sledovat v protokolech **diagnostické protokoly** kartu.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Zobrazení diagnostických protokolů pro účet Data Lake Storage Gen1
Chcete-li zobrazit data protokolu pro váš účet Data Lake Storage Gen1 dvěma způsoby.

* Z Data Lake Storage Gen1 zobrazit nastavení účtu
* Z účtu služby Azure Storage, kde jsou data uložená

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Pomocí Data Lake Storage Gen1 nastavení zobrazení
1. Z účtu Data Lake Storage Gen1 **nastavení** okna, klikněte na tlačítko **diagnostické protokoly**.
   
    ![Zobrazit diagnostické protokoly](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "zobrazit diagnostické protokoly") 
2. V **diagnostické protokoly** okno, měli byste vidět protokoly zařazený do kategorie službou **protokoly auditu** a **vyžádat protokoly**.
   
   * Protokoly žádosti o zachycení každého požadavku rozhraní API v účtu Data Lake Storage Gen1 provedeno.
   * Protokoly auditu se podobají vyžádat protokoly, ale poskytují mnohem podrobnější rozpis operací v účtu Data Lake Storage Gen1. Například může dojít jedním nahráním volání rozhraní API v protokolech žádosti více "Připojení" operací v protokolech auditování.
3. Pro stahování protokolů, klikněte na tlačítko **Stáhnout** odkaz pro každý záznam protokolu.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z účtu služby Azure Storage, který obsahuje data protokolu
1. Otevře se okno účtu Azure Storage související s Data Lake Storage Gen1 pro protokolování a potom klikněte na objekty BLOB. **Službu Blob service** okno uvádí dva kontejnery.
   
    ![Protokolování diagnostiky zobrazení](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "zobrazit diagnostické protokoly")
   
   * Kontejner **insights – protokoly auditu** obsahuje protokoly auditu.
   * Kontejner **insights protokoly žádosti** obsahuje protokoly požadavku.
2. V rámci těchto kontejnerů protokoly se budou ukládat v rámci následující strukturu.
   
    ![Protokolování diagnostiky zobrazení](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "zobrazit diagnostické protokoly")
   
    Jako příklad může být úplná cesta k protokolu auditu `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobně může být úplná cesta k požadavku protokolu `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Seznamte se se strukturou dat protokolu
Protokoly auditu a žádosti jsou ve formátu JSON. V této části podívejte se na strukturu JSON pro žádost o jsme protokoly auditu.

### <a name="request-logs"></a>Vyžádat protokoly
Zde je vstup vzorového v protokol žádosti ve formátu JSON. Každý objekt blob má jeden kořenový objekt volána **záznamy** , která obsahuje pole objektů protokolu.

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

#### <a name="request-log-schema"></a>Schéma požadavku protokolu
| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (ve standardu UTC) v protokolu |
| resourceId |Řetězec |ID prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **požadavky**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresu klientovi provádějícímu žádost |
| correlationId |Řetězec |ID protokolu, který můžete použít k seskupení sady položek protokolu související |
| identity |Objekt |Identita, která vygeneruje protokol |
| properties |JSON |Podrobnosti najdete níže |

#### <a name="request-log-properties-schema"></a>Požadavek protokolu vlastnosti schématu
| Název | Typ | Popis |
| --- | --- | --- |
| Vlastnost HttpMethod |Řetězec |Metoda protokolu HTTP se používá pro operaci. Třeba získáte. |
| Cesta |Řetězec |Cesta operaci byla provedena |
| RequestContentLength |int |Délka obsahu žádosti HTTP |
| ID žádosti klienta |Řetězec |ID, které jednoznačně identifikuje tento požadavek |
| StartTime |Řetězec |Čas, kdy server přijal požadavek |
| EndTime |Řetězec |Čas, odeslání odpovědi serveru |

### <a name="audit-logs"></a>Protokoly auditu
Tady je vstup vzorového v protokolu auditu ve formátu JSON. Každý objekt blob má jeden kořenový objekt volána **záznamy** , která obsahuje pole objektů protokolu

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

#### <a name="audit-log-schema"></a>Schéma protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (ve standardu UTC) v protokolu |
| resourceId |Řetězec |ID prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **auditu**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| resultSignature |Řetězec |Další podrobnosti o operaci. |
| correlationId |Řetězec |ID protokolu, který můžete použít k seskupení sady položek protokolu související |
| identity |Objekt |Identita, která vygeneruje protokol |
| properties |JSON |Podrobnosti najdete níže |

#### <a name="audit-log-properties-schema"></a>Schéma vlastnosti protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| StreamName |Řetězec |Cesta operaci byla provedena |

## <a name="samples-to-process-the-log-data"></a>Ukázky ke zpracování dat protokolu
Při odesílání protokolů z Azure Data Lake Storage Gen1 ke službě Azure Log Analytics (viz [zobrazení nebo analýza shromážděných dat pomocí prohledávání protokolu log Analytics](../azure-monitor/learn/tutorial-viewdata.md) podrobnosti o použití Log Analytics), následující dotaz vrátí tabulku se seznamem uživatele, zobrazte názvy, čas události a počet událostí čas události společně s vizuálním grafu. Můžete ho snadno upravit tak, aby zobrazit GUID uživatele, nebo dalšími atributy:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 najdete vzorový o tom, jak zpracovávat a analyzovat data protokolů. Můžete najít ukázce kódu na [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)

