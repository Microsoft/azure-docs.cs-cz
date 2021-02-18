---
title: Jak monitorovat Azure dosah
description: Naučte se konfigurovat metriky, výstrahy a nastavení diagnostiky Azure dosah pomocí Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: b13d03f05b7225a1c88f6b10b454749bd7b42477
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588338"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Metriky Azure dosah v Azure Monitor

Tento článek popisuje, jak nakonfigurovat metriky, výstrahy a nastavení diagnostiky pro Azure dosah pomocí Azure Monitor.

## <a name="monitor-azure-purview"></a>Monitorování Azure dosah

Správci Azure dosah můžou pomocí Azure Monitor sledovat provozní stav účtu dosah. Metriky jsou shromažďovány pro poskytování datových bodů za účelem sledování potenciálních problémů, řešení potíží a zlepšení spolehlivosti účtu dosah. Metriky se odesílají do Azure monitoru pro události, ke kterým dochází ve službě Azure dosah.

## <a name="aggregated-metrics"></a>Agregovaná metrika

K metrikám se dá dostat z Azure Portal účtu dosah. Přístup ke metrikám řídí přiřazení role účtu dosah. Aby bylo možné zobrazit metriky, musí být uživatelé součástí role čtenář monitoring v Azure dosah. Další informace o úrovních přístupu rolí najdete v podrobnostech o [oprávnění role čtenář monitoring](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) .

Osoba, která vytvořila účet dosah, automaticky získá oprávnění k zobrazení metrik. Pokud chce nikdo jiný zobrazit metriky, přidejte je do role **Čtenář monitorování** pomocí následujících kroků:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Přidat uživatele do role čtenář monitoring

Chcete-li přidat uživatele do role **Čtenář monitoring** , vlastník účtu dosah nebo vlastník předplatného může postupovat podle těchto kroků:

1. Přejít na [Azure Portal](https://portal.azure.com) a vyhledat název účtu Azure dosah.

2. Vyberte **Řízení přístupu (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Snímek obrazovky ukazující přístup k IAM":::

3. Vyberte **Přidat přiřazení role**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Snímek obrazovky ukazující, jak přidat přiřazení role":::

4. Vyberte **čtecí modul pro monitorování** rolí a nastavte přiřadit přístup k **uživateli, skupině nebo instančnímu objektu služby Azure AD**. A přiřaďte k metrikám účet AAD.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat roli Čtenář monitoring.":::

## <a name="metrics-visualization"></a>Vizualizace metrik

Uživatelé v roli **Čtenář monitorování** uvidí agregované metriky a diagnostické protokoly odeslané do Azure monitor. Metriky jsou uvedeny v Azure Portal pro odpovídající účet dosah. V Azure Portal vyberte část metriky a zobrazte seznam všech dostupných metrik.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Snímek obrazovky zobrazující dostupnou část metrik dosah" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Uživatelé Azure dosah mají přístup také ke stránce metriky přímo z centra pro správu účtu Azure dosah. Azure Portal spustíte výběrem Azure Monitor na hlavní stránce centra pro správu dosah.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Snímek obrazovky, ve kterém se spustí metriky dosah z centra pro správu." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Dostupné metriky

Chcete-li se seznámit s tím, jak používat část metriky v Azure Portal Přečtěte si následující dva dokumenty. [Začínáme s Průzkumníkem metrik](../azure-monitor/essentials/metrics-getting-started.md) a [pokročilými funkcemi Průzkumníka metrik](../azure-monitor/essentials/metrics-charts.md).

Následující tabulka obsahuje seznam metrik, které lze prozkoumat v Azure Portal:

| Název metriky | Obor názvů metriky | Typ agregace | Description |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Kontrola zrušena | Automatizovaná kontrola | Sum <br> Počet | Agregovat zrušené kontroly zdrojů dat za časové období |
| Kontrola dokončena | Automatizovaná kontrola | Sum <br> Počet | Agregovat dokončené kontroly zdrojů dat za časové období |
| Kontrola se nezdařila | Automatizovaná kontrola | Sum <br> Počet | Agregovat neúspěšné kontroly zdrojů dat za časové období |
| Doba kontroly provedena | Automatizovaná kontrola | Minimum <br> Maximum <br> Sum <br> Průměr | Agregovaná celková doba povedená kontrolami za časové období |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Diagnostické protokoly pro Azure Storage účtu

Nezpracované události telemetrie se generují Azure Monitor. Události se dají protokolovat k účtu úložiště zákazníka, který můžete vybrat pro další analýzu. Export protokolů se provádí prostřednictvím nastavení diagnostiky pro účet dosah na Azure Portal.

Postupujte podle pokynů pro vytvoření nastavení diagnostiky pro váš účet Azure dosah.

1. Pomocí tohoto článku vytvořte nové nastavení diagnostiky pro shromažďování protokolů a metrik platforem: [vytvořte nastavení diagnostiky a odešlete protokoly platforem a metriky do různých umístění](../azure-monitor/essentials/diagnostic-settings.md). Vyberte cíl jenom jako účet úložiště Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Snímek obrazovky znázorňující vytvoření diagnostického protokolu" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Zaprotokolujte události do účtu úložiště. Pro archivaci diagnostických protokolů se doporučuje vyhrazený účet úložiště. V tomto článku [vytvoříte účet úložiště](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Snímek obrazovky, který ukazuje přiřazení účtu úložiště pro diagnostický protokol." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Počkejte až 15 minut, než začnete přijímat protokoly v nově vytvořeném účtu úložiště. [Viz uchovávání dat a schéma protokolů prostředků v Azure Storage účet](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Po nakonfigurování diagnostických protokolů se události nasměrují do účtu úložiště.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Událost sleduje životní cyklus kontroly. Operace kontroly sleduje průběh v sekvenci stavů, z fronty, je spuštěná a nakonec stav terminálu proběhla úspěšně. | Neúspěšné | Zrušil. Událost je protokolována pro každý přechod stavu a schéma události bude obsahovat následující vlastnosti.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Vzorový protokol pro instanci události je uvedený v níže uvedené části.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Další kroky

[Zobrazit přehledy Asset Insights](asset-insights.md)
