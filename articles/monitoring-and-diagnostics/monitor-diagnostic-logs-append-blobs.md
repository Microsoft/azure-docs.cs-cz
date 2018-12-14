---
title: Příprava pro změnu formátu pro diagnostické protokoly Azure monitoru
description: Diagnostické protokoly Azure se přesune do použít doplňovací objekty BLOB na 1. listopadu 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 1d580e6468044f14b6a206911c4da237f774af3b
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386515"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Příprava pro změnu formátu pro diagnostické protokoly Azure monitoru archivovat do účtu úložiště

> [!WARNING]
> Pokud odesíláte [diagnostických protokolů prostředků Azure nebo metrik na účet úložiště pomocí nastavení diagnostiky prostředků](./../azure-monitor/platform/archive-diagnostic-logs.md) nebo [profily protokolů aktivit do účtu úložiště pomocí protokolů](./../azure-monitor/platform/archive-activity-log.md), formát dat v účet úložiště se změní na řádky JSON na 1. listopadu 2018. Následující pokyny popisují dopad a k aktualizaci nástrojů pro zpracování na nový formát. 
>
> 

## <a name="what-is-changing"></a>Co se mění

Azure Monitor nabízí možnosti, které vám umožní odesílat data diagnostiky prostředků a data protokolu aktivit do účtu služby Azure storage, obor názvů Event Hubs, nebo do Log Analytics. Aby bylo možné řešení problému s výkonem systému na **1. listopadu 2018 půlnoci 12:00 UTC** změní formát protokolu posílat data do úložiště objektů blob. Pokud máte nástroje, který je čtení dat z úložiště objektů blob, musíte aktualizovat nástrojů pochopit nový formát data.

* Čtvrtek, dne 1. 2018 půlnoci 12:00 UTC, se změní formát objektu blob bude [řádků JSON](http://jsonlines.org/). To znamená, že každý záznam se být odděleny znaku nového řádku, žádné pole vnější záznamy a žádné čárek mezi záznamy JSON.
* Změny formátu objektů blob pro všechna nastavení diagnostiky napříč všemi předplatnými najednou. První soubor PT1H.json pro 1. listopadu se bude používat tento nový formát. Názvy objektů blob a kontejnerů zůstávají stejné.
* Nastavení diagnostiky a 1. listopadu dál posílat data do aktuálního formátu až do 1. listopadu.
* Tato změna dojde současně ve všech veřejných cloudových oblastech. Změna ještě nedojde v cloudech Azure China, Azure Germany a Azure Government.
* Tato změna má vliv na následující typy dat:
  * [Diagnostické protokoly Azure prostředků](./../azure-monitor/platform/archive-diagnostic-logs.md) ([najdete v seznamu prostředků zde](./../azure-monitor/platform/tutorial-dashboards.md))
  * [Metriky prostředků Azure se exportované sadou nastavení diagnostiky](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)
  * [Azure data protokolu aktivit se exportované sadou profily protokolů](./../azure-monitor/platform/archive-activity-log.md)
* Tato změna nemá vliv:
  * Protokoly toku Network
  * Protokoly služby Azure nejsou k dispozici prostřednictvím služby Azure Monitor ještě (například diagnostické protokoly služby Azure App Service, protokoly analýzy úložiště)
  * Směrování Azure diagnostických protokolů a protokolů aktivit do jiných cílů (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak zobrazit, pokud jste se to týká

Můžete se pouze by to vliv na tato změna vás:
1. Odesílání dat protokolu do účtu služby Azure storage pomocí nastavení diagnostiky prostředků, a
2. Máte nástroje, které závisí na struktuře JSON tyto protokoly v úložišti.
 
A zjistěte, jestli máte nastavení diagnostiky prostředků, které odesílají data do účtu služby Azure storage, můžete přejít na **monitorování** části portálu klikněte na **nastavení diagnostiky**a identifikovat všechny prostředky, které mají **stavu diagnostiky** nastavena na **povoleno**:

![Okno Azure Monitor nastavení diagnostiky](./media/monitor-diagnostic-logs-append-blobs/portal-diag-settings.png)

Diagnostický stav nastaven na povoleno, máte aktivní nastavení diagnostiky pro tento prostředek. Klikněte na prostředek, který chcete zobrazit, pokud žádné nastavení diagnostiky se odesílání dat do účtu úložiště:

![Účet úložiště povolená](./media/monitor-diagnostic-logs-append-blobs/portal-storage-enabled.png)

Pokud máte prostředky odesílání dat do účtu úložiště pomocí těchto nastavení diagnostiky prostředků, bude tato změna vliv formát dat v tomto účtu úložiště. Pokud nemáte vlastních nástrojů, kterou z těchto účtů úložiště, neovlivní můžete změnit formát.

### <a name="details-of-the-format-change"></a>Podrobnosti o změně formátu

Aktuální formát souboru PT1H.json v úložišti objektů blob v Azure používá pole JSON záznamů. Tady je příklad souboru protokolu trezoru klíčů nyní:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Používá nový formát [řádků JSON](http://jsonlines.org/), kde každá událost představuje řádku a znak nového řádku označuje nové události. Tady je ukázka výše bude vypadat v souboru PT1H.json po provedení změny:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Tento nový formát umožňuje nabízených souborů protokolů pomocí Azure monitoru [doplňovací objekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), které jsou efektivnější pro průběžně přidávání nových dat událostí.

## <a name="how-to-update"></a>Postup aktualizace

Stačí provést aktualizace, pokud máte vlastní nástroje, které ingestují tyto soubory protokolů k dalšímu zpracování. Pokud provádíte použít externí log analytics nebo nástrojem SIEM, doporučujeme [pomocí služby event hubs k ingestování dat místo toho](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Event hubs integrace je snadnější z hlediska zpracování protokolů z mnoha služeb a umístění v konkrétní protokolu pro záložky.

Vlastní nástroje se musí aktualizovat na aktuální formát a formát řádků JSON je popsáno výše. Tím se zajistí, že jakmile se zobrazí v novém formátu dat, vaše nástroje nedojde k narušení.

## <a name="next-steps"></a>Další postup

* Další informace o [archivace diagnostických protokolů prostředků do účtu úložiště](./../azure-monitor/platform/archive-diagnostic-logs.md)
* Další informace o [archivovat data protokolu aktivit do účtu úložiště](./../azure-monitor/platform/archive-activity-log.md)
