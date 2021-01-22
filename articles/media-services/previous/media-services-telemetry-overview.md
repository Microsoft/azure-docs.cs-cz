---
title: Telemetrie Azure Media Services | Microsoft Docs
description: Tento článek poskytuje přehled telemetrie Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 4bf9a96d7ffc3b939abe8cfb889c5bd49fee09cc
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694583"
---
# <a name="azure-media-services-telemetry"></a>Telemetrie Azure Media Services  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Azure Media Services (AMS) umožňuje přístup k datům telemetrie/metrik pro své služby. Aktuální verze AMS umožňuje shromažďovat data telemetrie pro entity Live **Channel**, **StreamingEndpoint** a Live **Archive** . 

Telemetrie se zapisuje do tabulky úložiště v Azure Storagem účtu, který zadáte (obvykle byste použili účet úložiště přidružený k vašemu účtu AMS). 

Systém telemetrie nespravuje uchovávání dat. Stará data telemetrie můžete odebrat odstraněním tabulek úložiště.

Toto téma popisuje, jak nakonfigurovat a využít telemetrii AMS.

## <a name="configuring-telemetry"></a>Konfigurace telemetrie

Telemetrii můžete nakonfigurovat na členitosti na úrovni součásti. Existují dvě úrovně podrobností "normální" a "podrobné". V současné době obě úrovně vracejí stejné informace. Doporučuje se použít "normální". 

Následující témata ukazují, jak povolit telemetrii:

[Povolení telemetrie s .NET](media-services-dotnet-telemetry.md) 

[Povolení telemetrie s REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Využívání informací o telemetrie

Telemetrie se zapisuje do Azure Storage tabulky v účtu úložiště, který jste zadali při konfiguraci telemetrie pro Media Services účet. Tato část popisuje tabulky úložiště pro metriky.

Data telemetrie můžete využívat jedním z následujících způsobů:

- Načte data přímo z Azure Table Storage (například pomocí sady Storage SDK). Popis tabulek úložišť telemetrie najdete v [tomto](/previous-versions/azure/mt742089(v=azure.100)) tématu v **informacích o využívání telemetrie** .

Nebo

- Použijte podporu v sadě Media Services .NET SDK pro čtení dat úložiště, jak je popsáno v [tomto](media-services-dotnet-telemetry.md) tématu. 


Schéma telemetrie popsané níže je navrženo tak, aby poskytovalo dobrý výkon v rámci omezení Azure Table Storage:

- Data jsou rozdělená podle ID účtu a ID služby, aby bylo možné nezávisle na telemetrie z každé služby dotazovat.
- Oddíly obsahují datum k poskytnutí přiměřené horní meze velikosti oddílu.
- Klíče řádků jsou v opačném časovém pořadí, aby bylo možné pro danou službu zadat dotaz na nejnovější položky telemetrie.

To by mělo umožňovat efektivní využití mnoha běžných dotazů:

- Paralelní a nezávislé stahování dat pro samostatné služby.
- Načítání všech dat pro danou službu v časovém intervalu.
- Načítání nejaktuálnějších dat pro službu.

### <a name="telemetry-table-storage-output-schema"></a>Schéma výstupu úložiště tabulek telemetrie

Data telemetrie jsou ukládána v agregaci v jedné tabulce, "TelemetryMetrics20160321", kde "20160321" je datum vytvořené tabulky. Systém telemetrie vytvoří pro každý nový den samostatnou tabulku založenou na 00:00 UTC. Tabulka se používá k ukládání opakujících se hodnot, jako je například příjem přenosové rychlosti v rámci určitého časového období, odeslaných bajtů atd. 

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|{ID účtu} _ {ID entity}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>ID účtu je součástí klíče oddílu, aby se zjednodušily pracovní postupy, ve kterých se do stejného účtu úložiště zapisují více účtů Media Services.
RowKey|{sekund až půlnoc} _ {Random Value}|01688_00199<br/><br/>Klíč řádku začíná počtem sekund až půlnoci, aby bylo možné v rámci oddílu použít dotazy na horní n-Style. Další informace najdete v [tomto](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) článku. 
Timestamp|Datum/Čas|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241 Z
Typ|Typ entity poskytující data telemetrie|Kanál/StreamingEndpoint/archiv<br/><br/>Typ události je pouze hodnota řetězce.
Name|Název události telemetrie|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Čas výskytu události telemetrie (UTC)|2016-09-09T22:42:36.924 Z<br/><br/>Zjištěný čas je poskytován entitou odesílající telemetrii (například kanál). Může dojít k problémům při synchronizaci mezi komponentami, aby tato hodnota byla přibližná.
Idslužby|{ID služby}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Vlastnosti specifické pro entitu|Podle definice události|Datový proud: stream1, přenosová rychlost 10123,...<br/><br/>Zbývající vlastnosti jsou definovány pro daný typ události. Obsah tabulky Azure je páry klíč-hodnota.  (to znamená, že různé řádky v tabulce mají různé sady vlastností).

### <a name="entity-specific-schema"></a>Schéma specifické pro entitu

Existují tři typy datových položek s mezimetrikami specifické pro entitu, které byly vloženy s následující frekvencí:

- Koncové body streamování: každých 30 sekund
- Živé kanály: každou minutu
- Živý Archiv: každou minutu

**Koncový bod streamování**

Vlastnost|Hodnota|Příklady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatické časové razítko z Azure Table 2016 – 09-09T22:43:42.241 Z
Typ|Typ|StreamingEndpoint
Název|Název|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
Idslužby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Název hostitele|Název hostitele koncového bodu|builddemoserver.origin.mediaservices.windows.net
StatusCode|Zaznamenává stav HTTP|200
ResultCode|Podrobnosti o kódu výsledku|S_OK
RequestCount|Celkový požadavek v agregaci|3
BytesSent|Odeslané agregované bajty|2987358
ServerLatency|Průměrná latence serveru (včetně úložiště)|129
E2ELatency|Průměrná koncová latence|250

**Živý kanál**

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241 Z
Typ|Typ|Kanál
Název|Název|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
Idslužby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ videa, zvuk/text sledování|Video/zvuk
Stop|Název stopy|video/audio_1
Rychlostí|Rychlost sledování|785000
CustomAttributes –||   
IncomingBitrate|Skutečná příchozí přenosová rychlost|784548
OverlapCount|Překrytí v ingestu|0
DiscontinuityCount|Nekontinuita pro sledování|0
LastTimestamp|Poslední přijaté časové razítko dat|1800488800
NonincreasingCount|Počet zahozených fragmentů z důvodu nerostoucího časového razítka|2
UnalignedKeyFrames|Bez ohledu na to, zda byly přijaty fragmenty (v různých úrovních kvality), ve kterých nebyly zarovnány klíčové snímky |Ano
UnalignedPresentationTime|Bez ohledu na to, zda byly přijaty fragmenty (v různých úrovních kvality/stopy), kde není zarovnán čas prezentace|Ano
UnexpectedBitrate|True, pokud se počítá nebo Skutečná přenosová rychlost zvukového/obrazového záznamu > 40 000 b/s a IncomingBitrate = = 0 nebo IncomingBitrate a actualBitrate se liší o 50% |Ano
V pořádku|True, pokud <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> jsou všechny 0|Ano<br/><br/>V pořádku je složená funkce, která vrací hodnotu false, pokud se drží kterákoli z následujících podmínek:<br/><br/>– OverlapCount > 0<br/>– DiscontinuityCount > 0<br/>– NonincreasingCount > 0<br/>-UnalignedKeyFrames = = true<br/>-UnalignedPresentationTime = = true<br/>-UnexpectedBitrate = = true

**Živý archiv**

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241 Z
Typ|Typ|Archiv
Název|Název|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
Idslužby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Manifest|Adresa URL programu|Asset – eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba. ISM
Stop|Název stopy|audio_1
TrackType|Typ stopy|Zvuk/video
Atribut CustomAttribute|Řetězec v šestnáctkové soustavě, který rozlišuje různé stopy se stejným názvem a přenosovou rychlostí (ve více než jednom úhlu kamery)|
Rychlostí|Rychlost sledování|785000
V pořádku|True, pokud FragmentDiscardedCount = = 0 && ArchiveAcquisitionError = = false|True (tyto dvě hodnoty nejsou v metrikě přítomny, ale jsou přítomny ve zdrojové události)<br/><br/>V pořádku je složená funkce, která vrací hodnotu false, pokud se drží kterákoli z následujících podmínek:<br/><br/>– FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = true

## <a name="general-qa"></a>Obecné Q&A

### <a name="how-to-consume-metrics-data"></a>Jak spotřebovávat data metrik?

Data metrik se ukládají jako série tabulek Azure v účtu úložiště zákazníka. Tato data lze spotřebovat pomocí následujících nástrojů:

- SADA SDK AMS
- Průzkumník služby Microsoft Azure Storage (podporuje export do formátu hodnot oddělených čárkami a zpracovaných v Excelu)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak najít průměrnou spotřebu šířky pásma?

Průměrná spotřeba šířky pásma je průměrem BytesSent v časovém intervalu.

### <a name="how-to-define-streaming-unit-count"></a>Jak definovat počet jednotek streamování?

Počet jednotek streamování se dá definovat jako propustnost ve špičce z koncových bodů streamování služby děleného propustností jednoho koncového bodu streamování. Nejvyšší použitelná propustnost jednoho datového proudu streamování je 160 MB/s.
Předpokládejme například, že propustnost ve špičce ze služby zákazníka je 40 MB/s (maximální hodnota BytesSent v časovém intervalu). Počet jednotek streamování se pak rovná (40 MB/s) × (8 bitů/bajt)/(160 MB/s) = 2 jednotky streamování.

### <a name="how-to-find-average-requestssecond"></a>Jak najít průměrný počet požadavků za sekundu?

Chcete-li zjistit průměrný počet požadavků za sekundu, vypočítá průměrný počet požadavků (RequestCount) v časovém intervalu.

### <a name="how-to-define-channel-health"></a>Jak definovat stav kanálů?

Stav kanálu lze definovat jako složenou logickou funkci, protože je NEPRAVDA, pokud se drží kterákoli z následujících podmínek:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = true 
- UnalignedPresentationTime = = true 
- UnexpectedBitrate = = true


### <a name="how-to-detect-discontinuities"></a>Jak detekovat nekontinuity?

Chcete-li detekovat nekontinuity, vyhledejte všechny datové položky kanálu, kde DiscontinuityCount > 0. Příslušné časové razítko ObservedTime označuje čas, kdy došlo k dispozici.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak detekovat překrytí časového razítka?

Chcete-li zjistit překrytí časového razítka, vyhledejte všechny datové položky kanálu, kde OverlapCount > 0. Příslušné časové razítko ObservedTime označuje čas, kdy došlo k překrytí časového razítka.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak najít selhání požadavků a důvody pro streamování?

Pokud chcete najít selhání požadavků a důvody streamování, najděte všechny datové vstupy streamování, kde ResultCode není rovno S_OK. Odpovídající pole StatusCode označuje důvod selhání žádosti.

### <a name="how-to-consume-data-with-external-tools"></a>Jak spotřebovávat data pomocí externích nástrojů?

Data v mikrometrikě je možné zpracovávat a vizuálně rozlišit pomocí následujících nástrojů:

- Power BI
- Application Insights
- Azure Monitor (dříve Shoebox)
- Živý řídicí panel AMS
- Portál Azure (čeká na vydání)

### <a name="how-to-manage-data-retention"></a>Jak spravovat uchovávání dat?

Systém telemetrie neposkytuje správu uchovávání dat ani automatické odstranění starých záznamů. Proto je třeba ručně spravovat a odstraňovat staré záznamy z tabulky úložiště. To, jak to udělat, najdete v sadě SDK pro úložiště.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
