---
title: Migrace do kontejneru Read v3. x
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak migrovat do kontejnerů OCR pro čtení v3.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: 1cc17306265e6e8ba2e7fb3f570d0017b006b84f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284681"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrace na přečtené kontejnery OCR v3. x

Pokud používáte verzi 2 Počítačové zpracování obrazu čtení kontejneru OCR, použijte v tomto článku informace o upgradu aplikace na použití verze 3. x kontejneru. 


## <a name="configuration-changes"></a>Změny konfigurace

* `ReadEngineConfig:ResultExpirationPeriod` již není podporován. Kontejner pro čtení OCR má sestavenou úlohu cron, která odebere výsledky a Metadata přidružená k žádosti po 48 hodinách.
* `Cache:Redis:Configuration` již není podporován. Mezipaměť se nepoužívá v kontejnerech v3. x, takže ji nemusíte nastavovat.

## <a name="api-changes"></a>Změny rozhraní API

Kontejner Reada v 3.2 používá verzi 3 rozhraní API pro počítačové zpracování obrazu a má následující koncové body:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Podrobné informace o aktualizaci aplikací pro použití verze 3 cloudového rozhraní API pro čtení najdete v [Průvodci migrací REST API počítačové zpracování obrazu V3](./upgrade-api-versions.md) . Tyto informace platí i pro kontejner. Pamatujte, že operace synchronizace jsou podporované jenom v kontejnerech.

## <a name="memory-requirements"></a>Požadavky na paměť

Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků. Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner OCR pro čtení.

|Kontejner  |Minimum | Doporučeno  |
|---------|---------|------|
|Přečíst 3,2 – Preview | 8 jader, 16 GB paměti         | 8 jader, 24 GB paměti |

Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást příkazu Docker run.

## <a name="storage-implementations"></a>Implementace úložiště

>[!NOTE]
> MongoDB se už nepodporuje ve verzích 3. x kontejneru. Místo toho kontejnery podporují Azure Storage a offline systémy souborů.

| Implementace |    Požadované argumenty za běhu |
|---------|---------|
|Úroveň souboru (výchozí)    | Nejsou vyžadovány žádné argumenty modulu runtime. `/share` bude použit adresář. |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementace front

V systému V3. x kontejneru RabbitMQ není aktuálně podporován. Podporované implementace zálohování:

| Implementace | Běhové argument (y) | Zamýšlené použití |
|---------|---------|-------|
| V paměti (výchozí) | Nejsou vyžadovány žádné argumenty modulu runtime. | Vývoj a testování |
| Fronty Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Výroba |
| RabbitMQ    | Neaktivní | Výroba |

Pro přidání redundance načte kontejner Read v3. x časovač viditelnosti k zajištění úspěšného zpracování požadavků v případě selhání při spuštění v rámci sady více kontejnerů. 

Nastavte časovač s `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` , který nastaví čas, kdy bude zpráva neviditelná, když ji jiný pracovník zpracovává. Aby nedocházelo k redundantnímu zpracování stránek, doporučujeme nastavit časový limit na 120 sekund. Výchozí hodnota je 30 sekund.

| Výchozí hodnota | Doporučená hodnota |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](computer-vision-resource-container-config.md) pro nastavení konfigurace
* Další informace o rozpoznávání tiskového a rukopisného textu najdete v [přehledu optického rozpoznávání znaků](overview-ocr.md) .
* Podrobnosti o metodách podporovaných kontejnerem najdete v tématu [rozhraní API pro čtení](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Přečtěte si [Nejčastější dotazy](FAQ.md) k řešení problémů souvisejících se počítačové zpracování obrazu funkcemi.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)