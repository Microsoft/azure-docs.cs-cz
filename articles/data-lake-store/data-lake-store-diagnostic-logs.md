---
title: Zobrazení diagnostických protokolů pro Azure Data Lake Store | Microsoft Docs
description: 'Pochopit, jak nastavit a přístupu k diagnostickým protokolům pro Azure Data Lake Store '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 5f1fa378c8eea68181d4596700238d03f360c5d0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Přístup k diagnostickým protokolům pro Azure Data Lake Store
Informace o povolení protokolování pro váš účet Data Lake Store a postup zobrazení shromážděné pro váš účet protokoly diagnostiky.

Organizace může povolit protokolování diagnostiky ke svému účtu Azure Data Lake Store ke shromažďování dat záznamy auditu přístupu, která poskytuje informace, jako je seznam uživatelům přístup k datům, jak často se data používají, kolik dat je uložené v účtu atd. Když je povolené, diagnostiky nebo žádosti o jsou protokolovány na základě typu best effort. Požadavky a Diagnostika položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Povolit protokolování diagnostiky pro váš účet Data Lake Store
1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Otevřete účet Data Lake Store a z vaší okně účtu Data Lake Store klikněte na tlačítko **diagnostické protokoly**.
3. V **protokolů diagnostiky** okně klikněte na tlačítko **zapněte diagnostiku**.

    ![Povolit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "povolení diagnostických protokolů")

3. V **diagnostiky** okno, proveďte následující změny konfigurace protokolování diagnostiky.
   
    ![Povolit protokolování diagnostiky](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "povolení diagnostických protokolů")
   
   * Pro **název**, zadejte hodnotu pro konfiguraci protokolů diagnostiky.
   * Můžete úložiště/zpracovat data různými způsoby.
     
        * Vyberte možnost **archivu do účtu úložiště** k ukládání protokolů pro účet úložiště Azure. Tuto možnost použijte, pokud chcete archivovat data, která bude zpracování dávky později. Pokud vyberete tuto možnost je nutné zadat účet služby Azure Storage k ukládání protokolů.
        
        * Vyberte možnost **datový proud do centra událostí** na datový proud protokolu data do centra událostí Azure. Bude s největší pravděpodobností používat tuto možnost, pokud máte kanálu zpracování příjmu dat pro analýzu příchozích protokolů v reálném čase. Pokud vyberete tuto možnost, je nutné zadat podrobnosti pro centra událostí Azure, kterou chcete použít.

        * Vyberte možnost **odeslat k analýze protokolů** používat službu Azure Log Analytics k analýze dat generovaný protokolu. Pokud vyberete tuto možnost, je nutné zadat podrobnosti pro pracovní prostor služby Operations Management Suite se, že používáte analýzy protokolů provést. V tématu [zobrazit a analyzovat data shromážděná pomocí hledání protokolů analýzy protokolů](../log-analytics/log-analytics-tutorial-viewdata.md) podrobnosti o použití analýzy protokolů.
     
   * Určete, jestli chcete získat protokoly auditu nebo protokoly požadavku nebo obě.
   * Zadejte počet dnů, pro které se musí uchovávat data. Uchování platí pouze pokud používáte k archivaci dat protokolu účtu úložiště Azure.
   * Klikněte na **Uložit**.

Jakmile povolíte nastavení diagnostiky, můžete sledovat v protokolech **diagnostické protokoly** kartě.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Zobrazení diagnostických protokolů pro účet Data Lake Store
Existují dva způsoby, jak zobrazit data protokolu pro váš účet Data Lake Store.

* V zobrazení nastavení účtu Data Lake Store
* Z účtu úložiště Azure, kde jsou data uložená

### <a name="using-the-data-lake-store-settings-view"></a>Pomocí Data Lake Store nastavení zobrazení
1. Z účtu Data Lake Store **nastavení** okně klikněte na tlačítko **diagnostické protokoly**.
   
    ![Protokolování diagnostiky zobrazení](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "zobrazení diagnostických protokolů") 
2. V **diagnostické protokoly** okno, měli byste vidět protokoly zařazený do kategorie službou **protokoly auditu** a **požadavku protokoly**.
   
   * Protokoly žádosti o zachycení každý API požadavek na účet Data Lake Store.
   * Protokoly auditu jsou podobná žádosti protokoly, ale poskytují mnohem podrobnější rozpis operací týkajících se účet Data Lake Store. Například volání rozhraní API jednoho uložení v protokolech žádost může způsobit více operací "Připojit" v protokolech auditu.
3. Pro stažení protokolů, klikněte na tlačítko **Stáhnout** odkaz u každé položky protokolu.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z účtu úložiště Azure, který obsahuje data protokolu
1. Otevřete okno účtu úložiště Azure spojené s Data Lake Store pro protokolování a pak klikněte na objekty BLOB. **Služba objektů Blob** okno uvádí dva kontejnery.
   
    ![Protokolování diagnostiky zobrazení](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "zobrazení diagnostických protokolů")
   
   * Kontejner **přehledy. protokoly auditu** obsahující protokoly auditu.
   * Kontejner **přehledy. protokoly žádosti** obsahující protokoly požadavku.
2. V rámci těchto kontejnerů protokoly jsou uloženy v rámci následující strukturu.
   
    ![Protokolování diagnostiky zobrazení](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "zobrazení diagnostických protokolů")
   
    Jako příklad může být úplná cesta k protokolu auditování `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobně může být úplná cesta k požadavku protokolu `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Pochopení struktury data protokolu
Protokoly auditu a požadavku jsou ve formátu JSON. V této části jsme zobrazit strukturu JSON pro požadavek a protokoly auditu.

### <a name="request-logs"></a>Žádost o protokoly
Zde je vzorového vstupu do formátu JSON žádost protokolu. Každý objekt blob má jeden kořenový objekt názvem **záznamy** obsahující pole objektů protokolu.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
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
| time |Řetězec |Časové razítko (ve formátu UTC) v protokolu |
| resourceId |Řetězec |ID prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **požadavky**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| callerIpAddress |Řetězec |IP adresa klienta vytvoření požadavku |
| correlationId |Řetězec |ID protokolu, který můžete použít k seskupení sady položek protokolu související |
| identity |Objekt |Identity, která generuje protokol |
| properties |JSON |Níže naleznete podrobnosti |

#### <a name="request-log-properties-schema"></a>Schéma vlastnosti požadavku protokolu
| Název | Typ | Popis |
| --- | --- | --- |
| HttpMethod |Řetězec |Metoda HTTP se používá pro operaci. Například získáte. |
| Cesta |Řetězec |Cesta k operaci byla provedena na |
| RequestContentLength |celá čísla |Délka obsahu požadavku HTTP |
| ClientRequestId |Řetězec |Identifikátor, který jedinečně identifikuje tuto žádost |
| StartTime |Řetězec |Čas, na které server přijal žádost |
| EndTime |Řetězec |Čas, kdy server odeslal odpověď |

### <a name="audit-logs"></a>Protokoly auditu
Zde je vzorového vstupu v protokolu auditování formátu JSON. Každý objekt blob má jeden kořenový objekt názvem **záznamy** obsahující pole objektů protokolu

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schéma protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| time |Řetězec |Časové razítko (ve formátu UTC) v protokolu |
| resourceId |Řetězec |ID prostředku, který operace trvalo umístit na |
| category |Řetězec |Kategorie protokolu. Například **auditu**. |
| operationName |Řetězec |Název operace, která je zaznamenána. Například getfilestatus. |
| resultType |Řetězec |Stav operace, například 200. |
| resultSignature |Řetězec |Další podrobnosti o operaci. |
| correlationId |Řetězec |ID protokolu, který můžete použít k seskupení sady položek protokolu související |
| identity |Objekt |Identity, která generuje protokol |
| properties |JSON |Níže naleznete podrobnosti |

#### <a name="audit-log-properties-schema"></a>Schéma vlastnosti protokolu auditu
| Název | Typ | Popis |
| --- | --- | --- |
| StreamName |Řetězec |Cesta k operaci byla provedena na |

## <a name="samples-to-process-the-log-data"></a>Ukázky ke zpracování dat protokolu
Při odesílání protokolů z Azure Data Lake Store k analýze protokolů Azure (viz [zobrazení a analyzovat data shromážděná pomocí hledání protokolů analýzy protokolů](../log-analytics/log-analytics-tutorial-viewdata.md) podrobnosti o použití analýzy protokolů), následující dotaz vrátí tabulku obsahující seznam uživatelů zobrazte názvy, čas události a počet událostí dobu události společně s visual grafu. Můžete ho snadno upravit tak, aby zobrazit GUID uživatele, nebo dalšími atributy:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Store poskytuje vzorku o tom, jak zpracovávat a analyzovat data protokolu. Můžete najít ukázku najdete na adrese [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Store](data-lake-store-overview.md)
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)

