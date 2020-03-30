---
title: Monitorování operací ingestování Průzkumníka dat Azure pomocí diagnostických protokolů
description: Zjistěte, jak nastavit diagnostické protokoly pro Azure Data Explorer pro monitorování operací ingestování.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277432"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorování operací ingestování Průzkumníka dat Azure pomocí diagnostických protokolů (Preview)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Potom ingestovat (načíst) data do tabulky v databázi, takže můžete spustit dotazy proti němu. [Diagnostické protokoly Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) poskytují data o provozu prostředků Azure. Azure Data Explorer používá diagnostické protokoly pro přehled o úspěchy a selhání ingestování. Protokoly operací můžete exportovat do Azure Storage, Event Hub nebo Log Analytics a sledovat stav ingestování. Protokoly z Azure Storage a Azure Event Hub usměrní te na tabulku v clusteru Azure Data Explorer pro další analýzu.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).
* Vytvořte [cluster a databázi](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Nastavení diagnostických protokolů pro cluster Průzkumníka dat Azure

Diagnostické protokoly lze použít ke konfiguraci kolekce následujících dat protokolu:
* Úspěšné operace ingestování: Tyto protokoly mají informace o úspěšně dokončených operacích ingestování.
* Operace při neúspěšném ingestování: Tyto protokoly mají podrobné informace o neúspěšných operacích ingestování, včetně podrobností o chybě. 

Data se pak archivují do účtu úložiště, streamují do centra událostí nebo se posílají do Log Analytics podle vašich specifikací.

### <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou ve výchozím nastavení zakázány. Chcete-li povolit diagnostické protokoly, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte prostředek clusteru Azure Data Explorer, který chcete monitorovat.
1. V části **Monitorování** vyberte **Nastavení diagnostiky**.
  
    ![Přidání protokolů diagnostiky](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Vyberte **Přidat diagnostické nastavení**.
1. V okně **Nastavení diagnostiky:**
 
    ![Konfigurace nastavení diagnostiky](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Vyberte **Název** pro diagnostické nastavení.
    1. Vyberte jeden nebo více cílů: účet úložiště, Centrum událostí nebo Log Analytics.
    1. Vyberte protokoly, `SucceededIngestion` které `FailedIngestion`mají být shromažďovány: nebo .
    1. Vyberte [metriky, které](using-metrics.md#supported-azure-data-explorer-metrics) se mají shromažďovat (volitelné).  
    1. Výběrem **možnosti Uložit** uložíte nové nastavení diagnostických protokolů a metriky.
    1. Vytvořte **novou žádost o podporu** na webu Azure Portal a požádejte o aktivaci diagnostických protokolů.

Nové nastavení bude nastaveno během několika minut. Protokoly se pak zobrazí v nakonfigurovaném cíli archivace (účet úložiště, Centrum událostí nebo analýza protokolů). 

## <a name="diagnostic-logs-schema"></a>Schéma diagnostických protokolů

Všechny [diagnostické protokoly Azure Monitor sdílejí společné schéma nejvyšší úrovně](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer má jedinečné vlastnosti pro své vlastní události. Všechny protokoly jsou uloženy ve formátu JSON.

### <a name="ingestion-logs-schema"></a>Schéma protokolů ingestování

Řetězce Log JSON obsahují prvky uvedené v následující tabulce:

|Name (Název)               |Popis
|---                |---
|time               |Čas zprávy
|resourceId         |ID prostředků Správce prostředků Azure
|operationName      |Název operace: 'MICROSOFT. KUSTO/CLUSTERS/INGEST/ACTION"
|operationVersion   |Verze schématu: '1.0' 
|category           |Kategorie operace. `SucceededIngestion` nebo `FailedIngestion`. Vlastnosti se liší pro [úspěšnou operaci](#successful-ingestion-operation-log) nebo [neúspěšnou operaci](#failed-ingestion-operation-log).
|properties         |Podrobné informace o operaci.

#### <a name="successful-ingestion-operation-log"></a>Protokol operace úspěšného ingestování

**Příklad:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Vlastnosti protokolu diagnostiky úspěšné operace**

|Name (Název)               |Popis
|---                |---
|succeededOn        |Doba dokončení požití
|operationId        |ID operace ingestování Průzkumníka dat Azure
|database           |Název cílové databáze
|tabulka              |Název cílové tabulky
|ingestionSourceId  |ID zdroje dat o požití
|cesta ingestionSourcePath|Cesta zdroje dat o ingestování nebo identifikátorURI objektu BLOB
|rootActivityId     |ID aktivity

#### <a name="failed-ingestion-operation-log"></a>Protokol operace při selhání

**Příklad:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Vlastnosti protokolu diagnostiky operace se nezdařilo**

|Name (Název)               |Popis
|---                |---
|failedOn           |Doba dokončení požití
|operationId        |ID operace ingestování Průzkumníka dat Azure
|database           |Název cílové databáze
|tabulka              |Název cílové tabulky
|ingestionSourceId  |ID zdroje dat o požití
|cesta ingestionSourcePath|Cesta zdroje dat o ingestování nebo identifikátorURI objektu BLOB
|rootActivityId     |ID aktivity
|Podrobnosti            |Podrobný popis chyby a chybové zprávy
|Errorcode          |Kód chyby 
|failureStav      |`Permanent` nebo `Transient`. Opakování přechodné ho selhání může být úspěšné.
|pocházíFromUpdatePolicy|True, pokud selhání pochází ze zásady aktualizace
|shouldRetry        |True, pokud opakování může být úspěšné

## <a name="next-steps"></a>Další kroky

* [Kurz: Ingestování a monitorování dotazů v Průzkumníku dat Azure](ingest-data-no-code.md)
* [Použití metrik k monitorování stavu clusterů](using-metrics.md)

