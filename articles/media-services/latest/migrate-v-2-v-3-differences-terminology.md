---
title: Změny v terminologii a entitě Media Services V3
description: Tento článek popisuje rozdíly v terminologii mezi Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 6f677c8753f09e146d300186e19217568952b417
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705394"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Změny v terminologii a entitě mezi Media Services V2 a V3

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-2.svg)

Tento článek popisuje rozdíly v terminologii mezi Azure Media Services V2 a v3.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Projděte si zásady vytváření názvů, které se vztahují k prostředkům Media Services V3. Také zkontrolovat [pojmenování objektů BLOB](assets-concept.md#naming)

## <a name="terminology-changes"></a>Terminologické změny

- *Lokátor* se nyní označuje jako *Lokátor streamování*.
- *Kanál* se teď nazývá *živá událost*.
- *Program* se nyní označuje jako *živý výstup*.
- *Úloha* se teď nazývá *JobOutput*, která je součástí úlohy.

## <a name="entity-changes"></a>Změny entit
| **V2 – entita**<!-- row --> | **V3 – entita** | **Pokyny** | **Dostupné pro v3** | **Aktualizováno uživatelem V3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Entita `AccessPolicies` v v3 neexistuje. | Ne | Ne |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Ano | Ano |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Ano | Ne |
| `AssetFile`<!-- row --> | <!-- empty --> |Entita `AssetFiles` v v3 neexistuje. I když soubory (objekty blob úložiště), které nahráváte, jsou stále považovány za soubory.<br/><br/> Místo toho použijte rozhraní API Azure Storage k vytvoření výčtu objektů BLOB v kontejneru. Existují dva způsoby, jak použít transformaci na soubory s úlohou:<br/><br/>Soubory, které jsou už nahrané do úložiště: identifikátor URI by zahrnoval ID assetu pro úlohy, které se mají provést u prostředků v rámci účtu úložiště.<br/><br/>Soubory, které se mají nahrát během procesu transformace a úlohy: Asset se vytvoří v úložišti, vrátí se adresa URL SAS, soubory se nahrají do úložiště a pak se tato transformace aplikuje na soubory. | Ne | Ne |
| `Channel`<!-- row --> | `LiveEvent` | Živé události nahrazují kanály z rozhraní v2 API. Přenášejí většinu funkcí a nabízí další nové funkce, jako jsou živé přepisy, úsporný režim a podpora pro přijímání RTMP. <br/><br/>Podívejte [se na živá událost ve scénáři živé streamování](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Ne | Ne |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` už není entitou, teď je to vlastnost lokátoru streamování.<br/><br/>  V v3 jsou data klíče obsahu přidružená k `StreamingLocator` (pro výstupní šifrování) nebo samotnému prostředku (pro šifrování úložiště na straně klienta). | Ano | Ne |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Ano | Ne |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` jsou součástí `ContentKeyPolicy` . | Ano | Ne |
| `IngestManifest`<!-- row --> | <!-- empty --> | Entita `IngestManifests` v v3 neexistuje. Nahrání souborů v v3 zahrnuje rozhraní API služby Azure Storage. Prostředky se nejprve vytvoří a pak se nahrají soubory do přidruženého kontejneru úložiště. Existuje mnoho způsobů, jak získat data do kontejneru Azure Storage, který se dá použít místo toho. `JobInputHttp` poskytuje také způsob, jak si v případě potřeby stáhnout vstup úlohy z dané adresy URL. | Ne | Ne |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Existuje mnoho způsobů, jak získat data do kontejneru Azure Storage, který se dá použít místo toho. `JobInputHttp` poskytuje také způsob, jak si v případě potřeby stáhnout vstup úlohy z dané adresy URL. | Ne | Ne |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Existuje mnoho způsobů, jak získat data do kontejneru Azure Storage, který se dá použít místo toho. `JobInputHttp` poskytuje také způsob, jak si v případě potřeby stáhnout vstup úlohy z dané adresy URL. | Ne | Ne |
| `Job`<!-- row --> | `Job` | Vytvořte `Transform` před vytvořením `Job` . | Ne | Ne |
| `JobTemplate`<!-- row --> | `Transform` | `Transform`Místo toho použijte. Transformace je samostatná entita z úlohy a je opakovaně použita. | Ne | Ne |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Ano | Ne |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Místo vyhledávání `MediaProcessor` pro použití podle názvu použijte požadovanou předvolbu při definování transformace. Použitá předvolba určí procesor médií používaný systémem úloh. Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | Ne | NEDEF (jen pro čtení v v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Oznámení v v3 jsou zpracovávána prostřednictvím Azure Event Grid. `NotificationEndpoint`Je nahrazeno registrací předplatného Event Grid, který také zapouzdřuje konfiguraci pro typy oznámení, které mají být přijímány (ve verzi v2 byla zpracována `JobNotificationSubscription` úlohou, `TaskNotificationSubscription` úlohou a telemetrie `ComponentMonitoringSetting` ). Telemetrii v2 byla rozdělená mezi Azure Event Grid a Azure Monitor, aby se vešla do vylepšení většího ekosystému Azure. | Ne | Ne |
| `Program`<!-- row --> | `LiveOutput` | Živé výstupy teď nahrazují programy v rozhraní API v3.  | Ne | Ne |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Koncové body streamování zůstávají primárně stejné. Používají se k dynamickému balení, šifrování a doručování HLS a PŘERUŠOVANého obsahu pro živé i streamování na vyžádání buď přímo ze zdroje, nebo přes CDN. Mezi nové funkce patří podpora pro lepší Azure Monitor integraci a vytváření grafů. |  Ano | Ano |
| `Task`<!-- row --> | `JobOutput` | Nahradil `JobOutput` (což už není samostatnou entitou v rozhraní API).  Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Ne | Ne |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Nahradil `TransformOutput` (což už není samostatnou entitou v rozhraní API). Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Ne | Ne |
| `Inputs`<!-- row --> | `Inputs` | Vstupy a výstupy jsou teď na úrovni úlohy. Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md) . | Ne | Ne |
| `Outputs`<!-- row --> | `Outputs` | Vstupy a výstupy jsou teď na úrovni úlohy.  V v3 se formát metadat změnil z XML na JSON.  Živé výstupy začínají při vytváření a při odstranění se zastaví. Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md) . | Ne | Ne |


| **Další změny** | **2**  | **Technologie** |
|---|---|---|
| **Storage** <!--new row --> |||
| Storage <!--new row --> | | Sady SDK V3 jsou teď oddělené od sady SDK úložiště, což vám dává větší kontrolu nad verzí sady SDK pro úložiště, kterou chcete použít, a vyhněte se problémům se správou verzí.                      |
| **Kódování** <!--new row --> |||
| Přenosové rychlosti kódování <!--new row --> | přenosové rychlosti měřené v KB/s: 128 (KB/s)| bity za sekundu ex: 128000 (bity za sekundu)|
| Kódování DRM FairPlay <!--new row --> | V Media Services V2 lze zadat inicializační vektor (IV). | V Media Services V3 nelze zadat FairPlay IV.|
| Kodér úrovně Premium <!--new row --> | Kodér úrovně Premium a starší indexer| [Kodér úrovně Premium](../previous/media-services-encode-asset.md) a starší [procesory media Analytics](../previous/legacy-components.md) (Azure Media Services indexer 2 Preview, Face redigování atd.) nejsou přístupné prostřednictvím v3. Přidali jsme podporu pro mapování zvukových kanálů na standardní kodér.  Podívejte [se na zvuk v dokumentaci k rozhraní Swagger pro kódování Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Viz témata kódování v [kódování založeném na scénářích](migrate-v-2-v-3-migration-scenario-based-encoding.md) . |
| **Transformace a úlohy** <!--new row -->|||
| Zpracování protokolu HTTPS na základě úloh <!--new row --> |<!-- empty -->| Pro zpracování úloh založených na souborech můžete jako vstup použít adresu URL HTTPS. Nemusíte mít obsah, který je už uložený v Azure, ani nemusíte vytvářet prostředky. |
| Šablony ARM pro úlohy <!--new row --> | Šablony ARM v v2 neexistovaly. | Transformaci lze použít k vytvoření opakovaně použitelných konfigurací, k vytvoření Azure Resource Manager šablon a k izolaci nastavení zpracování mezi několika zákazníky nebo klienty. |
| **Živé události** <!--new row --> |||
| Koncový bod streamování <!--new row --> | Koncový bod streamování představuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) pro další distribuci. | Koncové body streamování zůstávají primárně stejné. Používají se k dynamickému balení, šifrování a doručování HLS a PŘERUŠOVANého obsahu pro živé i streamování na vyžádání buď přímo ze zdroje, nebo přes CDN.  Mezi nové funkce patří podpora pro lepší Azure Monitor integraci a vytváření grafů. |
| Kanály živých událostí <!--new row --> | Kanály zodpovídá za zpracování obsahu živého streamování. Kanál poskytuje vstupní koncový bod (adresa URL pro příjem), který pak poskytnete pro živý překódování. Kanál přijímá živé vstupní proudy z živého překódování a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více koncových bodů streamování. Kanály také poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.| Živé události nahrazují kanály z rozhraní v2 API. Přenášejí většinu funkcí a nabízí další nové funkce, jako jsou živé přepisy, úsporný režim a podpora pro přijímání RTMP. |
| Živé programy událostí <!--new row --> | Program umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálu a programu je podobný tradičnímu médiu, kde kanál obsahuje konstantní datový proud obsahu a program je vymezen na určitou událost s časovým limitem na daném kanálu. Nastavením vlastnosti můžete určit počet hodin, po které chcete zachovat zaznamenaný obsah pro program `ArchiveWindowLength` . Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.| Živé výstupy teď nahrazují programy v rozhraní API v3. |
| Živá délka události <!--new row --> |<!-- empty -->| Živé události 24/7 můžete streamovat při použití Media Services pro překódování informačního kanálu s jednou přenosovou rychlostí do výstupního datového proudu s více přenosovými rychlostmi.|
| Latence živé události <!--new row --> |<!-- empty -->| Nová podpora živého streamování s nízkou latencí pro živé události |
| Live Event Preview <!--new row --> |<!-- empty -->| Live Event Preview podporuje dynamické balení a dynamické šifrování. To umožňuje ochranu obsahu ve verzi Preview i SPOJOVNÍK a HLS balení. |
| Služby RTMP pro živé události <!--new row --> |<!-- empty-->| Vylepšená podpora RTMPs se zvýšenou stabilitou a větší podporou kodéru zdrojů. |
| Zabezpečené ingestování RTMP událostí v reálném čase <!--new row --> | | Při vytváření živé události získáte čtyři adresy URL pro příjem. 4 adresy URL pro přijímání jsou skoro stejné, mají stejný token pro streamování `AppId` , ale liší se jenom část čísla portu. Dvě z těchto adres URL jsou primární a zálohují pro RTMP.| 
| Živý přepis události <!--new row --> |<!-- empty--> | Služba Azure Media Service nabízí video, zvuk a text v různých protokolech. Když publikujete živý datový proud pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba přepisu text v IMSC 1.1 kompatibilní TTML.|
| Živý pohotovostní režim událostí <!--new row --> | Pro v2 nebyl žádný pohotovostní režim. | Úsporný režim je nová funkce v3, která pomáhá spravovat aktivní fondy živých událostí. Zákazníci teď můžou zahájit živou událost v úsporném režimu s nižšími náklady před přechodem do stavu spuštěno. To zlepšuje čas spuštění kanálů a snižuje náklady na provozní aktivní fondy pro rychlejší spouštění. |
| Účtování živé události <!--new row --> | <!-- empty-->| Účtování živých událostí je založeno na měřičích živých kanálů. |
| Živé výstupy <!--new row --> | Programy musely být spuštěny po vytvoření. | Živé výstupy začínají při vytváření a při odstranění se zastaví. |

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]