---
title: Azure Media Services Telemetrie | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Azure Media Services telemetrie.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 48b88aed833b0cd15f47195c67be80fe75fe153f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005178"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetrie  

Azure Media Services (AMS) umožňuje získat přístup k data telemetrie/metriky pro jeho služeb. Aktuální verze AMS umožňuje shromažďovat telemetrická data pro živé **kanál**, **StreamingEndpoint**a live **archivu** entity. 

Telemetrická data jsou zapsána do tabulky úložiště v účtu služby Azure Storage, který určíte (obvykle použijete účet úložiště přidružený k účtu AMS). 

Systém telemetrie uchovávání dat nespravuje. Staré telemetrická data můžete odebrat odstraněním tabulky úložiště.

Toto téma popisuje postup při konfiguraci a využití telemetrie AMS.

## <a name="configuring-telemetry"></a>Konfigurace telemetrie

Konfigurace telemetrie na úrovni členitosti komponenty. Existují dvě úrovně podrobností "Normální" nebo "Verbose". V současné době obou úrovních vrátí stejné informace. Doporučuje se použít "normální. 

Následující témata ukazují, jak povolit telemetrii:

[Zapnutí telemetrie s využitím .NET](media-services-dotnet-telemetry.md) 

[Zapnutí telemetrie s využitím REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Informace o používání telemetrii

Telemetrická data se zapisují do Azure Table Storage v účtu úložiště, který jste zadali při konfiguraci telemetrie pro účet Media Services. Tato část popisuje úložiště tabulky pro metriky.

Telemetrická data můžete využívat v jednom z následujících způsobů:

- Čtení dat přímo z Azure Table Storage (např. použití sady SDK služby Storage). Popis tabulky v úložišti telemetrická data, najdete v článku **využívání telemetrické informace** v [to](https://msdn.microsoft.com/library/mt742089.aspx) tématu.

Nebo

- Využijte podporu v sadě Media Services .NET SDK pro čtení úložiště dat, jak je popsáno v [to](media-services-dotnet-telemetry.md) tématu. 


Telemetrická data schématu je popsáno níže je navržená tak, aby dobrý výkon v rámci Azure Table Storage:

- Data jsou rozdělená podle ID účtu a ID služby umožňující telemetrická data z každé služby bude Dotazováno nezávisle na sobě.
- Oddíly obsahovat datum, které se poskytují rozumné horní mez na požadovanou velikost oddílu.
- Klíče řádku jsou v pořadí reverzní čas umožňující nejnovější položky telemetrie, které chcete dotaz pro danou službu.

Mělo by teď proběhnout řadu běžných dotazů účinný:

- Paralelní, nezávislé stahování dat pro jednotlivé služby.
- Načítání všech dat pro danou službu v konkrétní období.
- Načítání posledních dat pro službu.

### <a name="telemetry-table-storage-output-schema"></a>Schéma výstupu tabulky úložiště telemetrie

Telemetrická data uložená v agregaci v jedné tabulce, "TelemetryMetrics20160321" kde "20160321" je datum vytvořené tabulce. Systém telemetrie vytvoří samostatnou tabulku denně nové závislosti v 00:00 UTC. Tabulka slouží k uložení opakujících se hodnot, jako ingestování s přenosovou rychlostí v rámci daného časového období čas, počet odeslaných bajtů, atd. 

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
PartitionKey|{ID účtu} _ {entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>ID účtu je součástí klíče oddílu pro zjednodušení pracovních postupů, kde více účtů Media Services jsou zápisu do stejného účtu úložiště.
RowKey|{seconds půlnoci} _ {náhodnou hodnotu}|01688_00199<br/><br/>Klíč řádku začíná počet sekund, po půlnoci na povolit dotazy na první styl n v rámci oddílu. Další informace najdete v [tomto](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) článku. 
Časové razítko|Datum a čas|Auto časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Type|Typ na subjekt poskytující telemetrická data|Kanál/StreamingEndpoint/archiv<br/><br/>Typ události je právě řetězcovou hodnotu.
Název|Název události telemetrie|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Čas telemetrické události došlo k chybě (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Poskytuje sledovaného časového entity odesílá telemetrická data (například kanál). Může být synchronizací mezi součástmi, takže tato hodnota je přibližný čas
ID služby|{ID služby}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Vlastnosti specifické pro entity|Podle definice události|StreamName: stream1 s přenosovou rychlostí 10123...<br/><br/>Zbývající vlastnosti jsou definována pro typ danou událost. Obsah Azure Table je páry klíč-hodnota.  (to znamená, že různé řádky v tabulce mají různé sady vlastností).

### <a name="entity-specific-schema"></a>Schéma konkrétní entity

Existují tři typy dat telemetrická data specifická pro entity položek vložena se následující frekvence:

- Koncové body streamování: Každých 30 sekund
- Živé kanály: Každou minutu
- Živý archív: Každou minutu

**Koncový bod streamování**

Vlastnost|Hodnota|Příklady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Auto časové razítko z Azure Table 2016-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
Název|Název|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
název hostitele|Název hostitele koncového bodu|builddemoserver.origin.mediaservices.windows.net
StatusCode|Stav záznamy protokolu HTTP|200
Kód výsledku|Podrobnosti výsledku kódu|S_OK
RequestCount|Celkový požadavek v agregaci|3
BytesSent|Agregovaný počet odeslaných bajtů|2987358
ServerLatency|Server Průměrná latence (včetně úložiště)|129
E2ELatency|Průměrná latence začátku do konce|250

**Živého kanálu**

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Auto časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Type|Type|Kanál
Název|Název|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ sledování video nebo audio/textu|video/audio
TrackName|Název dráhy|video/audio_1
S přenosovou rychlostí|Sledování s přenosovou rychlostí|785000
CustomAttributes –||   
IncomingBitrate|Skutečné příchozí s přenosovou rychlostí|784548
OverlapCount|Překrytí v ingestu|0
DiscontinuityCount|Přerušení pro sledování|0
LastTimestamp|Časové razítko poslední přijatých dat|1800488800
NonincreasingCount|Počet fragmentů zrušeny kvůli nerostoucí časové razítko|2
UnalignedKeyFrames|Zda jsme obdrželi fragment(s) (v rámci úrovně kvality) kde klíčové snímky není zarovnána. |True
UnalignedPresentationTime|Určuje, zda dostali jsme fragment(s) (v rámci úrovně kvality/stop) Pokud není zarovnána čas prezentace|True
UnexpectedBitrate|True pokud počítá skutečné přenosové rychlosti pro zvuk/video sledují > 40 000 počtu bitů za sekundu a IncomingBitrate == 0 nebo IncomingBitrate a actualBitrate liší o 50 % |True
V pořádku|Hodnota TRUE, v případě <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> jsou všechny 0|True<br/><br/>V jakém stavu je složený funkci, která vrátí hodnotu false, pokud některý z následujících podmínek uchování:<br/><br/>- OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Živý archív**

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Auto časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Type|Type|Archiv
Název|Název|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Adresa url aplikace|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Název dráhy|audio_1
TrackType|Typ dráhy|Audio/video
Atribut CustomAttribute|Hex řetězec, který rozlišuje mezi různé stopa se stejným názvem a s přenosovou rychlostí (s více úhel kamery)|
S přenosovou rychlostí|Sledování s přenosovou rychlostí|785000
V pořádku|Hodnota TRUE, v případě FragmentDiscardedCount == 0 & & ArchiveAcquisitionError == False|True (tyto dvě hodnoty nejsou k dispozici v metriky, ale jsou k dispozici v zdroj události)<br/><br/>V jakém stavu je složený funkci, která vrátí hodnotu false, pokud některý z následujících podmínek uchování:<br/><br/>- FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Obecné funkce Q & A

### <a name="how-to-consume-metrics-data"></a>Jak používat data metrik?

Můžete například měřená data se ukládá jako řadu tabulek Azure v účtu úložiště zákazníka. Tato data mohou být spotřebovány pomocí následujících nástrojů:

- AMS SDK
- Microsoft Azure Storage Explorer (podporuje export do formátu hodnot oddělených čárkami a zpracování v aplikaci Excel)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak zjistit průměrné využití šířky pásma?

Průměrné využití šířky pásma je průměrem BytesSent konfigurovatelnou dobu.

### <a name="how-to-define-streaming-unit-count"></a>Jak definovat streamování počet jednotek?

Streamování počet jednotek je definovat jako ve špičce propustnost ze služby koncové body streamování děleno maximální propustnost jeden koncový bod streamování. Použitelné propustnost jeden koncový bod streamování ve špičce je 160 MB/s.
Předpokládejme například, že nejvyšší propustnost ze služby zákazníka je 40 MB/s (maximální hodnota BytesSent konfigurovatelnou dobu). Streamování počet jednotek je rovno (40 MB/s) * (8 bitů/bajtů) /(160 Mbps) = 2 jednotky streamování.

### <a name="how-to-find-average-requestssecond"></a>Jak najít průměrný počet požadavků za sekundu?

Pokud chcete najít průměrný počet požadavků za sekundu, výpočetní konfigurovatelnou dobu průměrný počet žádostí (RequestCount).

### <a name="how-to-define-channel-health"></a>Jak definovat kanál health?

Stav kanálu lze definovat jako složeného logickou funkci tak, že má hodnotu false, pokud některý z následujících podmínek uchování:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Jak detekovat nespojitosti?

Ke zjištění nespojitosti najít všechny datové položky kanálu kde DiscontinuityCount > 0. Odpovídající ObservedTime časové razítko označuje dobu, kdy nespojitosti došlo.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak detekovat časového razítka překrývají?

Ke zjištění časového razítka překrývají, najít všechny datové položky kanálu kde OverlapCount > 0. Odpovídající ObservedTime časové razítko označuje, že došlo k chybě časy, ve kterých se překrývá časové razítko.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak najít streamování selhání žádostí a důvodů?

Najít streamování selhání žádostí a důvody, najdete všechny datové položky koncový bod streamování, kde kód výsledku není roven S_OK. Odpovídající pole StatusCode označuje důvod selhání požadavku.

### <a name="how-to-consume-data-with-external-tools"></a>Jak používat data pomocí externích nástrojů?

Telemetrická data dat může zpracovat a vizualizovat pomocí následujících nástrojů:

- PowerBI
- Application Insights
- Azure Monitor (formerly Shoebox)
- Živé řídicí panel AMS
- Azure Portal (čeká na vydání)

### <a name="how-to-manage-data-retention"></a>Jak spravovat uchovávání dat?

Systém telemetrie neposkytuje správu uchovávání dat nebo automatické odstranění starých záznamů. Proto musíte ke správě a ručně odstranit staré záznamy z tabulky úložiště. Mohou odkazovat na sada SDK úložiště pro jak na to.

## <a name="next-steps"></a>Další postup

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
