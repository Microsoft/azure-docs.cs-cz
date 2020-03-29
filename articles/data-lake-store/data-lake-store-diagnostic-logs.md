---
title: Zobrazení diagnostických protokolů pro Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: 'Pochopit, jak nastavit a získat přístup k diagnostickým protokolům pro Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878686"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Přístup k diagnostickým protokolům pro Azure Data Lake Storage Gen1
Naučte se povolit protokolování diagnostiky pro váš účet Azure Data Lake Storage Gen1 a jak zobrazit protokoly shromážděné pro váš účet.

Organizace můžou povolit diagnostické protokolování pro svůj účet Azure Data Lake Storage Gen1 ke shromažďování stop auditu přístupu k datům, které poskytují informace, jako je seznam uživatelů, kteří přistupují k datům, jak často se k datům přistupuje, kolik dat je v účtu uloženo atd. Pokud je tato možnost povolena, diagnostika nebo požadavky jsou protokolovány na základě nejlepšího úsilí. Požadavky a diagnostické položky protokolu jsou vytvořeny pouze v případě, že jsou požadavky provedené proti koncovému bodu služby.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Povolení diagnostického protokolování pro účet Data Lake Storage Gen1
1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Otevřete svůj účet Data Lake Storage Gen1 a v okně účtu Data Lake Storage Gen1 klikněte na **Nastavení diagnostiky**.
3. V okně **Nastavení diagnostiky** klepněte na tlačítko **Zapnout diagnostiku**.

    ![Povolení protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Povolení diagnostických protokolů")

3. V okně **Nastavení diagnostiky** proveďte následující změny konfigurace protokolování diagnostiky.
   
    ![Povolení protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Povolení diagnostických protokolů")
   
   * Do **pole Název**zadejte hodnotu pro konfiguraci diagnostického protokolu.
   * Můžete se rozhodnout ukládat/zpracovávat data různými způsoby.
     
        * Vyberte možnost **Archivovat na účet úložiště** pro ukládání protokolů do účtu Úložiště Azure. Tuto možnost použijte, pokud chcete archivovat data, která budou později zpracována v dávce. Pokud vyberete tuto možnost, musíte zadat účet Azure Storage pro uložení protokolů.
        
        * Vyberte možnost **Streamovat do centra událostí** pro streamování dat protokolu do Centra událostí Azure. S největší pravděpodobností použijete tuto možnost, pokud máte kanál následného zpracování k analýze příchozích protokolů v reálném čase. Pokud vyberete tuto možnost, musíte zadat podrobnosti pro Azure Event Hub, který chcete použít.

        * Vyberte možnost **Odeslat do Analýzy protokolů,** chcete-li pomocí služby Azure Monitor analyzovat vygenerovaná data protokolu. Pokud vyberete tuto možnost, musíte zadat podrobnosti o pracovním prostoru Log Analytics, který byste použili provést analýzu protokolu. Viz [Zobrazení nebo analýza dat shromážděných pomocí protokolů Azure Monitor hledání](../azure-monitor/learn/tutorial-viewdata.md) podrobností o používání protokolů Azure Monitor.
     
   * Určete, zda chcete získat protokoly auditu nebo protokoly požadavků nebo obojí.
   * Zadejte počet dní, po které musí být data uchována. Uchovávání informací je použitelné jenom v případě, že k archivaci dat protokolu používáte účet úložiště Azure.
   * Klikněte na **Uložit**.

Po povolení nastavení diagnostiky můžete sledovat protokoly na kartě **Diagnostické protokoly.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Zobrazení diagnostických protokolů pro váš účet Data Lake Storage Gen1
Existují dva způsoby, jak zobrazit data protokolu pro váš účet Data Lake Storage Gen1.

* Ze zobrazení nastavení účtu Data Lake Storage Gen1
* Z účtu Azure Storage, kde jsou data uložená

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Použití zobrazení Nastavení úložiště datového jezera
1. V okně **Nastavení** účtu Data Lake Storage Gen1 klikněte na **diagnostické protokoly**.
   
    ![Zobrazit diagnostické protokoly](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Zobrazit diagnostické protokoly") 
2. V okně **Protokoly protokolů diagnostiky** byste měli vidět protokoly zařazené do kategorií **protokoly auditování** a **protokoly požadavků**.
   
   * Protokoly požadavků zachycují všechny požadavky rozhraní API provedené na účtu Data Lake Storage Gen1.
   * Protokoly auditu jsou podobné protokolům požadavků, ale poskytují mnohem podrobnější rozpis operací prováděných s účtem Gen1 úložiště datového jezera. Například volání rozhraní API pro jedno nahrávání v protokolech požadavků může mít za následek více operací "Připojit" v protokolech auditu.
3. Chcete-li stáhnout protokoly, klepněte na odkaz **Stáhnout** proti každé položce protokolu.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z účtu Azure Storage, který obsahuje data protokolu
1. Otevřete okno účtu Azure Storage přidružené k datovému úložišti Data Lake Storage Gen1 pro protokolování a klikněte na Objekty blob. Okno **služby blob** obsahuje seznam dvou kontejnerů.
   
    ![Zobrazit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Zobrazit diagnostické protokoly")
   
   * Kontejner **insights-protokoly-audit** obsahuje protokoly auditu.
   * Kontejner **insights protokoly protokoly** obsahuje protokoly požadavků.
2. V rámci těchto kontejnerů protokoly jsou uloženy pod následující strukturu.
   
    ![Zobrazit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Zobrazit diagnostické protokoly")
   
    Jako příklad lze například úplnou cestu k protokolu auditu`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobně úplná cesta k protokolu požadavků může být`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Pochopit strukturu dat protokolu
Protokoly auditu a požadavků jsou ve formátu JSON. V této části se podíváme na strukturu JSON pro protokoly požadavků a auditu.

### <a name="request-logs"></a>Protokoly požadavků
Zde je ukázková položka v protokolu požadavků ve formátu JSON. Každý objekt blob má jeden kořenový objekt nazývaný **záznamy,** který obsahuje pole objektů protokolu.

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

#### <a name="request-log-schema"></a>Vyžádat schéma protokolu
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |ID prostředku, na které se operace uskutečnila |
| category |Řetězec |Kategorie protokolu. Například **Požadavky**. |
| operationName |Řetězec |Název operace, která je protokolována. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta, který žádost poslaje |
| correlationId |Řetězec |ID protokolu, který lze použít k seskupení sady souvisejících položek protokolu |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti naleznete níže |

#### <a name="request-log-properties-schema"></a>Vyžádat schéma vlastností protokolu
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| Metoda http |Řetězec |Metoda HTTP použitá pro operaci. Například GET. |
| Cesta |Řetězec |Cesta, po které byla operace provedena |
| Délka obsahu požadavku |int |Délka obsahu požadavku HTTP |
| Id klienta |Řetězec |ID, které jednoznačně identifikuje tento požadavek |
| StartTime |Řetězec |Čas, kdy server obdržel požadavek |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu
Tady je ukázková položka v protokolu auditu ve formátu JSON. Každý objekt blob má jeden kořenový objekt nazývaný **záznamy,** který obsahuje pole objektů protokolu

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
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (v UTC) protokolu |
| resourceId |Řetězec |ID prostředku, na které se operace uskutečnila |
| category |Řetězec |Kategorie protokolu. Například **Audit**. |
| operationName |Řetězec |Název operace, která je protokolována. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| resultSignature |Řetězec |Další podrobnosti o operaci. |
| correlationId |Řetězec |ID protokolu, který lze použít k seskupení sady souvisejících položek protokolu |
| identity |Objekt |Identita, která vygenerovala protokol |
| properties |JSON |Podrobnosti naleznete níže |

#### <a name="audit-log-properties-schema"></a>Schéma vlastností protokolu auditování
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| Název datového proudu |Řetězec |Cesta, po které byla operace provedena |

## <a name="samples-to-process-the-log-data"></a>Ukázky pro zpracování dat protokolu
Při odesílání protokolů z Azure Data Lake Storage Gen1 do protokolů Azure Monitor (viz [Zobrazení nebo analýza dat shromážděných pomocí protokolů Azure Monitor hledání](../azure-monitor/learn/tutorial-viewdata.md) podrobností o používání protokolů Azure Monitor), následující dotaz vrátí tabulku obsahující seznam uživatelských zobrazovaných jmen, čas událostí a počet událostí pro čas události spolu s vizuální masy. Lze jej snadno upravit tak, aby zobrazoval identifikátor GUID uživatele nebo jiné atributy:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 poskytuje ukázku zpracování a analýzy dat protokolu. Vzorek najdete na [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)adrese . 

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)

