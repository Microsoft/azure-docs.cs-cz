---
title: Monitorování operací ingestování v Azure Průzkumník dat pomocí diagnostických protokolů
description: Naučte se, jak nastavit diagnostické protokoly pro Azure Průzkumník dat pro monitorování operací přijímání.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 13f86f0156299619d8bf8d92eb92bbcf8b4cb76c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173797"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorování operací ingestování v Azure Průzkumník dat pomocí diagnostických protokolů (Preview)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do tabulky v databázi, abyste na ni mohli spouštět dotazy. [Protokoly diagnostiky Azure monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) poskytují data o provozu prostředků Azure. Azure Průzkumník dat používá diagnostické protokoly pro přehledy o úspěšných a neúspěšných přijímání. Protokoly operací můžete exportovat do Azure Storage, centra událostí nebo Log Analytics a monitorovat tak stav ingestování. Protokoly ze Azure Storage a centra událostí Azure se dají směrovat do tabulky v clusteru Azure Průzkumník dat, abyste mohli dále analyzovat.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).
* Vytvořte [cluster a databázi](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Nastavení diagnostických protokolů pro cluster Azure Průzkumník dat

Diagnostické protokoly lze použít ke konfiguraci shromažďování následujících dat protokolu:
* Úspěšné operace ingestování: tyto protokoly obsahují informace o úspěšně dokončených operacích ingestování.
* Nezdařené operace ingestování: tyto protokoly obsahují podrobné informace o neúspěšných operacích ingestování včetně podrobností o chybě. 

Data se pak archivují do účtu úložiště, streamování do centra událostí nebo se odešlou do Log Analytics podle vašich požadavků.

### <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou ve výchozím nastavení zakázané. Chcete-li povolit diagnostické protokoly, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)vyberte prostředek clusteru Azure Průzkumník dat, který chcete monitorovat.
1. V části **monitorování**vyberte **nastavení diagnostiky**.
  
    ![Přidat diagnostické protokoly](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Vyberte **Přidat nastavení diagnostiky**.
1. V okně **nastavení diagnostiky** :
 
    ![Konfigurace nastavení diagnostiky](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Pro nastavení diagnostiky vyberte **název** .
    1. Vyberte jeden nebo více cílů: účet úložiště, centrum událostí nebo Log Analytics.
    1. Vyberte protokoly, které se mají shromažďovat: `SucceededIngestion` nebo `FailedIngestion`.
    1. Vyberte [metriky](using-metrics.md) , které se mají shromáždit (volitelné).   
    1. Výběrem možnosti **Uložit** uložte nové nastavení a metriky diagnostických protokolů.
    1. Vytvořte **novou žádost o podporu** v Azure Portal pro žádost o aktivaci diagnostických protokolů.

Nová nastavení se nastaví za několik minut. Protokoly se pak zobrazí v nakonfigurovaném cíli archivace (účet úložiště, centrum událostí nebo Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny [diagnostické protokoly Azure monitor sdílejí společné schéma nejvyšší úrovně](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Průzkumník dat má jedinečné vlastnosti pro vlastní události. Všechny protokoly jsou uložené ve formátu JSON.

### <a name="ingestion-logs-schema"></a>Schéma protokolů přijímání

Řetězce JSON protokolu obsahují prvky uvedené v následující tabulce:

|Název               |Popis
|---                |---
|time               |Čas sestavy
|resourceId         |ID prostředku Azure Resource Manager
|operationName      |Název operace: MICROSOFT. KUSTO/CLUSTERY/INGESTOVÁNÍ/AKCE
|operationVersion   |Verze schématu: ' 1,0 ' 
|category           |Kategorie operace. `SucceededIngestion` nebo `FailedIngestion`. Vlastnosti se u [úspěšné operace](#successful-ingestion-operation-log) nebo [nezdařené operace](#failed-ingestion-operation-log)liší.
|properties         |Podrobné informace o operaci.

#### <a name="successful-ingestion-operation-log"></a>Úspěšný protokol operace ingestování

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
**Vlastnosti diagnostického protokolu úspěšné operace**

|Název               |Popis
|---                |---
|succeededOn        |Čas dokončení přijímání příjmu
|operationId        |ID operace ingestování Azure Průzkumník dat
|database           |Název cílové databáze
|table              |Název cílové tabulky
|ingestionSourceId  |ID zdroje dat ingestování
|ingestionSourcePath|Cesta zdroje dat ingestování nebo identifikátoru URI objektu BLOB
|rootActivityId     |ID aktivity

#### <a name="failed-ingestion-operation-log"></a>Neúspěšný protokol operace ingestování

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

**Vlastnosti diagnostického protokolu nezdařené operace**

|Název               |Popis
|---                |---
|failedOn           |Čas dokončení přijímání příjmu
|operationId        |ID operace ingestování Azure Průzkumník dat
|database           |Název cílové databáze
|table              |Název cílové tabulky
|ingestionSourceId  |ID zdroje dat ingestování
|ingestionSourcePath|Cesta zdroje dat ingestování nebo identifikátoru URI objektu BLOB
|rootActivityId     |ID aktivity
|details            |Podrobný popis chyby a chybové zprávy
|errorCode          |Kód chyby 
|failureStatus      |`Permanent` nebo `Transient`. Opakování přechodného selhání může být úspěšné.
|originatesFromUpdatePolicy|True, pokud selhání pochází ze zásady aktualizace
|shouldRetry        |True, pokud opakování může být úspěšné

## <a name="next-steps"></a>Další kroky

* [Kurz: ingestování a dotazování dat monitorování v Azure Průzkumník dat](ingest-data-no-code.md)
* [Monitorování stavu clusteru pomocí metrik](using-metrics.md)

