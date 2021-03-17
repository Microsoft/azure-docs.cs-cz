---
title: Příprava na změnu formátu pro Azure Monitor protokoly prostředků
description: Protokoly prostředků Azure se přesunuly tak, aby používaly doplňovací objekty blob, od 1. listopadu 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.openlocfilehash: d4be3983d70a1ca78d849e231b8cc55e2b5895d4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033193"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Příprava na změnu formátu Azure Monitor protokoly platformy archivované na účet úložiště

> [!WARNING]
> Pokud odesíláte [protokoly prostředků Azure nebo metriky do účtu úložiště pomocí nastavení diagnostiky](./resource-logs.md#send-to-azure-storage) nebo [protokolů aktivit v účtu úložiště pomocí profilů protokolů](./resource-logs.md#send-to-azure-storage), formát dat v účtu úložiště se změní na řádky JSON od 1. listopadu 2018. Níže uvedené pokyny popisují dopad a způsob aktualizace nástrojů pro zpracování nového formátu.
>

## <a name="what-changed"></a>Co se změnilo

Azure Monitor nabízí možnost, která umožňuje odesílat protokoly prostředků a protokoly aktivit do účtu služby Azure Storage, Event Hubs oboru názvů nebo do Log Analyticsho pracovního prostoru v Azure Monitor. Aby bylo možné vyřešit potíže s výkonem systému, **1. listopadu 2018 ve 12:00 půlnoci času UTC** se změnil formát data protokolu odeslání do úložiště objektů BLOB. Pokud máte nástroje, které čtou data z úložiště objektů blob, je potřeba aktualizovat nástroje, abyste pochopili nový formát dat.

* Ve čtvrtek od 1. listopadu 2018 ve 12:00 půlnoci UTC se formát objektu BLOB změnil na [řádky JSON](http://jsonlines.org/). To znamená, že každý záznam bude oddělený novým řádkem, bez pole vnějších záznamů a žádné čárky mezi záznamy JSON.
* Formát objektu BLOB se změnil pro všechna nastavení diagnostiky ve všech předplatných najednou. První PT1H.jsv souboru vydaném pro 1. listopadu používal tento nový formát. Názvy objektů BLOB a kontejnerů zůstávají stejné.
* Nastavení diagnostiky mezi před 1. listopadu pokračuje v generování dat v aktuálním formátu do 1. listopadu.
* Tato změna nastala v rámci všech oblastí veřejné cloudové oblasti. Tato změna se neprojeví v Microsoft Azure provozovaných v cloudech 21Vianet, Azure Německo nebo Azure Government.
* Tato změna má vliv na následující typy dat:
  * [Protokoly prostředků Azure](./resource-logs.md#send-to-azure-storage) ([tady najdete seznam prostředků](./resource-logs-schema.md))
  * [Metriky prostředků Azure, které se exportují pomocí nastavení diagnostiky](../essentials/diagnostic-settings.md)
  * [Data protokolu aktivit Azure, která se exportují pomocí profilů protokolů](./activity-log.md)
* Tato změna nemá vliv na:
  * Protokoly toku sítě
  * Protokoly služby Azure nejsou k dispozici prostřednictvím Azure Monitor zatím (například protokoly Azure App Service prostředků, protokoly analýzy úložiště)
  * Směrování protokolů o prostředcích a protokolů aktivit Azure do jiných cílů (Event Hubs Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak zjistit, jestli jste ovlivnili

Tato změna ovlivní jenom v případě, že:
1. Odesílají data protokolu do účtu služby Azure Storage pomocí nastavení diagnostiky a
2. Mít nástroje, které závisí na struktuře JSON těchto protokolů v úložišti.
 
Pokud chcete zjistit, jestli máte nastavení diagnostiky, která odesílají data do účtu služby Azure Storage, můžete přejít do části **monitorování** na portálu, kliknout na **nastavení diagnostiky** a identifikovat všechny prostředky, u nichž je **stav diagnostiky** nastaven na **povoleno**:

![Okno nastavení diagnostiky Azure Monitor](media/resource-logs-blob-format/portal-diag-settings.png)

Pokud je stav diagnostiky nastaven na povoleno, máte aktivní nastavení diagnostiky pro tento prostředek. Kliknutím na prostředek zjistíte, jestli nějaké nastavení diagnostiky posílá data do účtu úložiště:

![Účet úložiště je povolený.](media/resource-logs-blob-format/portal-storage-enabled.png)

Pokud máte prostředky odesílající data do účtu úložiště pomocí těchto nastavení diagnostiky prostředků, bude tato změna mít vliv na formát dat v tomto účtu úložiště. Pokud nemáte vlastní nástroje, které nefungují z těchto účtů úložiště, změna formátu vás nebude ovlivňovat.

### <a name="details-of-the-format-change"></a>Podrobnosti změny formátu

Aktuální formát PT1H.jsv souboru v úložišti objektů BLOB v Azure používá pole JSON záznamů. Tady je ukázka souboru protokolu trezoru klíčů nyní:

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

Nový formát používá [řádky JSON](http://jsonlines.org/), kde každá událost je řádek a znak nového řádku indikuje novou událost. V tomto příkladu bude výše uvedený vzor vypadat jako v PT1H.jssouboru po změně:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Tento nový formát umožňuje Azure Monitor vkládat soubory protokolu pomocí [doplňovacích objektů BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), které jsou efektivnější pro nepřetržité připojení nových dat událostí.

## <a name="how-to-update"></a>Postup aktualizace

Aktualizace je potřeba provést jenom v případě, že máte vlastní nástroje, které ingestují tyto soubory protokolu k dalšímu zpracování. Pokud používáte externí nástroj Log Analytics nebo nástroj SIEM, doporučujeme [místo toho použít centra událostí k](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)ingestování těchto dat. Integrace centra událostí je snazší v souvislosti s zpracováním protokolů z mnoha služeb a umístěním do záložky v konkrétním protokolu.

Vlastní nástroje by se měly aktualizovat tak, aby zpracovávala jak aktuální formát, tak formát řádků JSON, jak je popsáno výše. Tím se zajistí, že když se data začnou zobrazovat v novém formátu, nástroje se neruší.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [archivaci protokolů prostředků prostředků do účtu úložiště](./resource-logs.md#send-to-azure-storage) .
* Další informace o [archivaci dat protokolu aktivit do účtu úložiště](./activity-log.md#legacy-collection-methods)
