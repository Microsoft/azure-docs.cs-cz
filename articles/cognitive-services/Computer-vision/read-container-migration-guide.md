---
title: Migrace do kontejnerů OCR v3. x
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak migrovat do kontejnerů OCR pro čtení v3.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 714a4709eceea875798940de962716d34437f2a9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530455"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrace na přečtené kontejnery OCR v3. x

Pokud používáte verzi 2 Počítačové zpracování obrazu čtení kontejneru OCR, použijte v tomto článku informace o upgradu aplikace na použití verze 3. x kontejneru. 


## <a name="configuration-changes"></a>Změny konfigurace

* `ReadEngineConfig:ResultExpirationPeriod` již není podporován. Kontejner pro čtení má sestavenou úlohu cron, která odebere výsledky a Metadata přidružená k žádosti po 48 hodinách.
* `Cache:Redis:Configuration` již není podporován. Mezipaměť se nepoužívá v kontejnerech v3. x, takže ji nemusíte nastavovat.

## <a name="api-changes"></a>Změny rozhraní API

Kontejnery v3. x používají verzi 3 rozhraní API pro počítačové zpracování obrazu a mají následující koncové body:

#### <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

Podrobné informace o aktualizaci aplikací pro použití verze 3 cloudového rozhraní API pro čtení najdete v [Průvodci migrací REST API počítačové zpracování obrazu V3](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions) . Tyto informace platí i pro kontejner. Pamatujte, že operace synchronizace jsou podporované jenom v kontejnerech.

## <a name="memory-requirements"></a>Požadavky na paměť

Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků. Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

|Kontejner  |Minimum | Doporučeno  |
|---------|---------|------|
|Přečíst 3,0 – Preview     | 8 jader, 16 GB paměti         | 8 jader, 24 GB paměti
|Přečíst 3,1 – Preview | 8 jader, 16 GB paměti         | 8 jader, 24 GB paměti

Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást příkazu Docker run.

## <a name="storage-implementations"></a>Implementace úložiště

>[!NOTE]
> MongoDB se už nepodporuje ve verzích 3. x kontejneru. Místo toho kontejnery podporují Azure Storage a offline systémy souborů.

| Implementace |  Požadované argumenty za běhu |
|---------|---------|
|Úroveň souboru (výchozí)   | Nejsou vyžadovány žádné argumenty modulu runtime. `/share` bude použit adresář. |
|Azure Blob | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementace front

V systému V3. x kontejneru RabbitMQ není aktuálně podporován. Podporované implementace zálohování:

| Implementace | Běhové argument (y) | Zamýšlené použití |
|---------|---------|-------|
| V paměti (výchozí) | Nejsou vyžadovány žádné argumenty modulu runtime. | Vývoj a testování |
| Fronty Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produkce |
| RabbitMQ  | Neaktivní | Produkce |

Pro přidání redundance načte kontejner Read v3. x časovač viditelnosti k zajištění úspěšného zpracování požadavků v případě selhání při spuštění v rámci sady více kontejnerů. 

Nastavte časovač s `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` , který nastaví čas, kdy bude zpráva neviditelná, když ji jiný pracovník zpracovává. Aby nedocházelo k redundantnímu zpracování stránek, doporučujeme nastavit časový limit na 120 sekund. Výchozí hodnota je 30 sekund.

| Výchozí hodnota | Doporučená hodnota |
|---------|---------|
| 30000 |   120000 |


## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](computer-vision-resource-container-config.md) pro nastavení konfigurace
* Přečtěte si [počítačové zpracování obrazu přehled](overview.md) , kde najdete další informace o rozpoznávání vytištěného a rukopisného textu.
* Podrobnosti o metodách podporovaných kontejnerem najdete v [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Přečtěte si [Nejčastější dotazy](FAQ.md) k řešení problémů souvisejících se počítačové zpracování obrazu funkcemi.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
