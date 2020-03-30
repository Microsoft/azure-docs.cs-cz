---
title: Příprava na změnu formátu do protokolů prostředků Azure Monitoru
description: Popisuje dopad a jak aktualizovat nástroje pro zpracování nových protokolů prostředků Azure, které byly změněny tak, aby používaly objekty BLOB připojit 1.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d30652d4e068cbceb79e6da60b48176b9de64647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670436"
---
# <a name="prepare-for-format-change-to-azure-monitor-resource-logs-archived-to-a-storage-account"></a>Příprava na změnu formátu do protokolů prostředků Azure Monitoru archivovaných do účtu úložiště

> [!WARNING]
> Pokud odesíláte [protokoly prostředků Azure nebo metriky do účtu úložiště pomocí nastavení diagnostiky prostředků](./../../azure-monitor/platform/archive-diagnostic-logs.md) nebo [protokolů aktivit do účtu úložiště pomocí profilů protokolu](./../../azure-monitor/platform/archive-activity-log.md), formát dat v účtu úložiště se změní na Řádky JSON v listopadu 1, 2018. Níže uvedené pokyny popisují dopad a jak aktualizovat nástroje pro zpracování nového formátu. 
>
> 

## <a name="what-is-changing"></a>Co se mění

Azure Monitor nabízí funkci, která umožňuje odesílat diagnostická data o prostředcích a data protokolu aktivit do účtu úložiště Azure, oboru názvů Event Hubs nebo do pracovního prostoru Log Analytics v Azure Monitoru. Aby bylo možné vyřešit problém s výkonem **systému, v listopadu 1, 2018 v 12:00 půlnoci UTC** formát dat protokolu odeslat do úložiště objektů blob se změní. Pokud máte nástroje, které jsou čtení dat z úložiště objektů blob, budete muset aktualizovat nástroje pochopit nový formát dat.

* Ve čtvrtek 1. [JSON Lines](http://jsonlines.org/) To znamená, že každý záznam bude oddělen novým řádkem, bez pole vnějších záznamů a bez čárek mezi záznamy JSON.
* Formát objektu blob se změní pro všechna diagnostická nastavení ve všech předplatných najednou. První soubor PT1H.json vyzařovaný pro 1. Názvy objektů blob a kontejnerů zůstávají stejné.
* Nastavení diagnostického nastavení mezi dneškem a 1.
* K této změně dojde najednou ve všech oblastech veřejného cloudu. Ke změně nedojde v Microsoft Azure provozovaném cloudy 21Vianet, Azure Germany nebo Azure Government.
* Tato změna má vliv na následující datové typy:
  * [Protokoly prostředků Azure](archive-diagnostic-logs.md) [(viz seznam prostředků zde)](diagnostic-logs-schema.md)
  * [Metriky prostředků Azure exportované podle nastavení diagnostiky](diagnostic-settings.md)
  * [Data protokolu aktivit Azure exportovaná pomocí profilů protokolu](archive-activity-log.md)
* Tato změna nemá vliv na:
  * Protokoly toku sítě
  * Protokoly služeb Azure ještě nejsou dostupné prostřednictvím Azure Monitoru (například protokoly prostředků služby Azure App Service, protokoly analýzy úložiště).
  * Směrování protokolů prostředků Azure a protokolů aktivit do jiných cílů (centra událostí, analýza protokolů)

### <a name="how-to-see-if-you-are-impacted"></a>Jak zjistit, zda jste ovlivněni

Tato změna vás ovlivní pouze v případě, že:
1. Posílají data protokolu do účtu úložiště Azure pomocí nastavení diagnostiky prostředků a
2. Mít nástroje, které závisí na struktuře JSON těchto protokolů v úložišti.
 
Chcete-li zjistit, zda máte nastavení diagnostiky prostředků, které odesílávají data do účtu úložiště Azure, můžete přejít do části **Sledování** na portálu, kliknout na **Nastavení diagnostiky**a identifikovat všechny prostředky, které mají **diagnostický stav** nastavený na **Povoleno**:

![Okno Nastavení diagnostiky monitoru Azure](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Pokud je stav diagnostiky nastaven na povoleno, máte aktivní diagnostické nastavení tohoto prostředku. Kliknutím na prostředek zobrazíte, jestli nějaká diagnostická nastavení neodesílají data do účtu úložiště:

![Účet úložiště povolen.](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Pokud máte prostředky odesílající data do účtu úložiště pomocí těchto nastavení diagnostiky prostředků, formát dat v tomto účtu úložiště bude mít vliv na tuto změnu. Pokud nemáte vlastní nástroje, které fungují mimo tyto účty úložiště, změna formátu nebude mít vliv na vás.

### <a name="details-of-the-format-change"></a>Podrobnosti o změně formátu

Aktuální formát souboru PT1H.json v úložišti objektů blob Azure používá pole json záznamů. Zde je ukázka souboru protokolu KeyVault nyní:

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

Nový formát používá [řádky JSON](http://jsonlines.org/), kde každá událost je řádek a znak nového řádku označuje novou událost. Zde je to, co výše uvedená ukázka bude vypadat v souboru PT1H.json po změně:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Tento nový formát umožňuje Azure Monitor nabízení souborů protokolu pomocí [připojit objekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), které jsou efektivnější pro průběžné přidávání nových dat událostí.

## <a name="how-to-update"></a>Jak aktualizovat

Aktualizace je třeba provést pouze v případě, že máte vlastní nástroje, které tyto soubory protokolu ingestuje pro další zpracování. Pokud používáte externí protokol analytics nebo siem nástroj, doporučujeme [použít centra událostí k ingestování těchto dat místo](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integrace centra událostí je jednodušší, pokud jde o zpracování protokolů z mnoha služeb a umístění bookmarkingu v určitém protokolu.

Vlastní nástroje by měly být aktualizovány tak, aby zpracovávali aktuální formát i formát JSON Lines popsaný výše. Tím zajistíte, že když se data začnou objevovat v novém formátu, vaše nástroje se nepřeruší.

## <a name="next-steps"></a>Další kroky

* Informace o [archivaci protokolů prostředků prostředků do účtu úložiště](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Informace o [archivaci dat protokolu aktivit do účtu úložiště](./../../azure-monitor/platform/archive-activity-log.md)

