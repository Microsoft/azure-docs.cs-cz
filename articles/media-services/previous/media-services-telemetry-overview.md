---
title: Telemetrie mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled telemetrie Microsoft Azure Media Services.
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
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895781"
---
# <a name="azure-media-services-telemetry"></a>Telemetrie mediálních služeb Azure  


> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services (AMS) umožňuje přístup k telemetrických nebo metrických dat pro své služby. Aktuální verze Služby AMS umožňuje shromažďovat telemetrická data pro živé **kanály**, **Streamendpoint**a živé **archivní** entity. 

Telemetrie se zapisuje do tabulky úložiště v účtu Azure Storage, který zadáte (obvykle byste použili účet úložiště přidružený k vašemu účtu AMS). 

Telemetrický systém nespravuje uchovávání dat. Stará telemetrická data můžete odebrat odstraněním tabulek úložiště.

Toto téma popisuje, jak nakonfigurovat a využívat telemetrii AMS.

## <a name="configuring-telemetry"></a>Konfigurace telemetrie

Telemetrická data můžete nakonfigurovat na úrovni komponenty rozlišovací schopnost. Existují dvě úrovně detailů "Normální" a "Verbose". V současné době obě úrovně vrátí stejné informace. Doporučuje se používat "Normální. 

Následující témata ukazují, jak povolit telemetrii:

[Povolení telemetrie pomocí rozhraní .NET](media-services-dotnet-telemetry.md) 

[Povolení telemetrie pomocí rest](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Spotřebovávající telemetrické informace

Telemetrie se zapisuje do tabulky úložiště Azure v účtu úložiště, který jste zadali při konfiguraci telemetrie pro účet Mediální chslužeb. Tato část popisuje tabulky úložiště pro metriky.

Telemetrická data můžete využívat jedním z následujících způsobů:

- Čtení dat přímo z Azure Table Storage (např. pomocí sady Storage SDK). Popis tabulek úložiště telemetrie naleznete v **tématu Náročné telemetrické informace** v [tomto](https://msdn.microsoft.com/library/mt742089.aspx) tématu.

Nebo

- Použijte podporu sady Media Services .NET SDK pro čtení dat úložiště, jak je popsáno v [tomto](media-services-dotnet-telemetry.md) tématu. 


Schéma telemetrie popsané níže je navrženo tak, aby přináfčila dobrý výkon v rámci azure table storage:

- Data jsou rozdělena podle ID účtu a ID služby, aby telemetrická data z každé služby bylo dotazováno nezávisle.
- Oddíly obsahují datum poskytnout přiměřené horní mez na velikost oddílu.
- Klíče řádků jsou v obráceném časovém pořadí, aby bylo možné dotazovat nejnovější položky telemetrie pro danou službu.

To by mělo umožnit, aby mnoho běžných dotazů bylo efektivní:

- Paralelní, nezávislé stahování dat pro samostatné služby.
- Načítání všech dat pro danou službu v rozsahu dat.
- Načítání nejnovějších dat pro službu.

### <a name="telemetry-table-storage-output-schema"></a>Schéma výstupu úložiště tabulky telemetrie

Telemetrická data jsou uložena v agregaci v jedné tabulce "TelemetryMetrics20160321", kde "20160321" je datum vytvořené tabulky. Telemetrický systém vytvoří samostatnou tabulku pro každý nový den založený v 00:00 UTC. Tabulka se používá k ukládání opakujících se hodnot, jako je například požití přenosového toku v daném časovém okně, odeslaných bajtů atd. 

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|{ID účtu}_{ID entity}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>ID účtu je součástí klíče oddílu pro zjednodušení pracovních postupů, kde více účtů Media Services zapisuje do stejného účtu úložiště.
RowKey|{sekunddo půlnoci}_{náhodná hodnota}|01688_00199<br/><br/>Klíč řádku začíná s počtem sekund do půlnoci povolit horní n styl dotazy v rámci oddílu. Další informace naleznete v [tomto](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) článku. 
Časové razítko|Datum/Čas|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ entity poskytující telemetrická data|Kanál/StreamingEndpoint/Archiv<br/><br/>Typ události je pouze řetězcová hodnota.
Name (Název)|Název události telemetrie|Kanál tlukot srdce/streamováníEndpointRequestLog
Doba pozorování|Čas, kdy došlo k události telemetrie (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Pozorovaný čas poskytuje entita odesílající telemetrii (například kanál). Mezi součástmi mohou napodobuji napodobují problémy se synchronizací, takže tato hodnota je přibližná.
ID služby|{ID služby}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Vlastnosti specifické pro entitu|Jak je definováno událostí|StreamName: stream1, Bitrate 10123, ...<br/><br/>Zbývající vlastnosti jsou definovány pro daný typ události. Obsah tabulky Azure je dvojice hodnot klíče.  (to znamená, že různé řádky v tabulce mají různé sady vlastností).

### <a name="entity-specific-schema"></a>Schéma specifické pro entitu

Existují tři typy telemetrických datových položek specifických pro entitu, z nichž každá je nabízena s následující frekvencí:

- Koncové body streamování: Každých 30 sekund
- Živé kanály: Každou minutu
- Živý archiv: Každou minutu

**Koncový bod streamování**

Vlastnost|Hodnota|Příklady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Datový proudEndpoint
Name (Název)|Name (Název)|Parametr StreamingEndpointRequestLog
Doba pozorování|Doba pozorování|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Název hostitele koncového bodu|builddemoserver.origin.mediaservices.windows.net
Statuscode|Záznamy stavu HTTP|200
Výsledekkód|Detail kódu výsledku|S_OK
RequestCount|Celkový požadavek v agregaci|3
BajtůOdeslání|Agregované bajty odeslané|2987358
Latence serveru|Průměrná latence serveru (včetně úložiště)|129
E2ELatency|Průměrná latence od konce do konce|250

**Živý kanál**

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Channel
Name (Název)|Name (Název)|Kanál tlukot srdce
Doba pozorování|Doba pozorování|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ stopy videa/zvuku/textu|video/zvuk
Název skladby|Název stopy|video/audio_1
Datový tok|Sledovat přenosovou rychlost|785000
Customattributes||   
PříchozíBitrate|Skutečný příchozí přenosový tok|784548
OverlapCount|Překrytí v ingestování|0
Počet nespojitosti|Diskontinuita pro trať|0
LastTimestamp|Časové razítko naposledy ingemdatných dat|1800488800
NonincreasingCount|Počet fragmentů vyřazených z důvodu nezvyšujícího se časového razítka|2
Nezarovnané snímky klíčů|Zda jsme obdrželi fragmenty (napříč úrovněmi kvality), kde klíčové snímky nejsou zarovnány |True
Nezarovnaný čas prezentace|Zda jsme obdrželi fragmenty (napříč úrovněmi kvality/stopami), kde není doba prezentace zarovnána|True
Neočekávaná bitová rychlost|Pravda, pokud je vypočtena/skutečný přenosový tok pro zvuk/video stopy > 40 000 b/s a IncomingBitrate == 0 NEBO PříchozíPřenosná rychlost a actualBitrate se liší o 50 % |True
V pořádku|Pravda, pokud <br/>overlapCount, <br/>Počet nespojitostiCount, <br/>NonIncreasingCount, <br/>Nezarovnané snímky Klíčů, <br/>NezarovnanýPresentationTime, <br/>Neočekávaná bitová rychlost<br/> jsou všechny 0|True<br/><br/>V pořádku je složená funkce, která vrací false, pokud některá z následujících podmínek podržte:<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- Nezarovnané klíčové rámce == True<br/>- NezarovnanýPresentationTime == True<br/>- UnexpectedBitrate == True

**Živý archiv**

Vlastnost|Hodnota|Příklady/poznámky
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Časové razítko|Časové razítko|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Archiv
Name (Název)|Name (Název)|Archivheartbeat
Doba pozorování|Doba pozorování|2016-09-09T22:42:36.924Z
ID služby|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Název manifestu|Adresa URL programu|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
Název skladby|Název stopy|audio_1
TrackType|Typ koleje|Zvuk/video
Vlastní atribut|Šestnáctkový řetězec, který rozlišuje mezi různými stopami se stejným názvem a přenosovým tokem (více úhlů kamery)|
Datový tok|Sledovat přenosovou rychlost|785000
V pořádku|Pravda, pokud FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False|True (tyto dvě hodnoty nejsou k dispozici v metrice, ale jsou k dispozici ve zdrojové události)<br/><br/>V pořádku je složená funkce, která vrací false, pokud některá z následujících podmínek podržte:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>Obecné Q&A

### <a name="how-to-consume-metrics-data"></a>Jak využívat data metrik?

Data metrik se ukládají jako řada tabulek Azure v účtu úložiště zákazníka. Tato data lze spotřebovat pomocí následujících nástrojů:

- AMS SDK
- Microsoft Azure Storage Explorer (podporuje export do formátu hodnoty oddělené čárkami a zpracovaný v Excelu)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak zjistit průměrnou spotřebu šířky pásma?

Průměrná spotřeba šířky pásma je průměr BajtůOdeslat v časovém rozpětí.

### <a name="how-to-define-streaming-unit-count"></a>Jak definovat počet jednotek streamování?

Počet jednotek streamování lze definovat jako propustnost špičky z koncových bodů služby streamování děleno propustností špičky jednoho koncového bodu streamování. Maximální využitelná propustnost jednoho koncového bodu streamování je 160 Mb/s.
Předpokládejme například, že propustnost vrcholu ze služby zákazníka je 40 Mb/s (maximální hodnota BajtůOdeslavá v průběhu času). Potom je počet jednotek streamování roven (40 MB/s)*(8 bitů/bajt)/(160 Mb/s) = 2 jednotky streamování.

### <a name="how-to-find-average-requestssecond"></a>Jak najít průměrné požadavky za sekundu?

Chcete-li najít průměrný počet požadavků za sekundu, vypočítat průměrný počet požadavků (RequestCount) v průběhu času.

### <a name="how-to-define-channel-health"></a>Jak definovat stav kanálu?

Stav kanálu lze definovat jako složené logické funkce tak, že je false, pokud některá z následujících podmínek držet:

- OverlapCount > 0
- Počet nespojitostiPočet > 0
- NonincreasingCount > 0
- Nezarovnané klíčové rámce == True 
- NezarovnanýPresentationTime == True 
- Neočekávaná přenosová rychlost == True


### <a name="how-to-detect-discontinuities"></a>Jak zjistit nespojitosti?

Chcete-li zjistit nespojitosti, vyhledejte všechny položky dat kanálu, kde DiscontinuityCount > 0. Odpovídající časové razítko ObservedTime označuje časy, kdy došlo k nespojitosti.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak zjistit časové razítko překrývání?

Chcete-li zjistit překrytí časového razítka, vyhledejte všechny datové položky kanálu, ve kterých > 0 OverlapCount. Odpovídající časové razítko ObservedTime označuje časy, kdy došlo k překrytí časového razítka.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak najít selhání a důvody požadavku na streamování?

Chcete-li najít selhání požadavku streamování a důvody, vyhledejte všechny položky dat koncového bodu streamování, kde resultcode není rovno S_OK. Odpovídající pole StatusCode označuje důvod selhání požadavku.

### <a name="how-to-consume-data-with-external-tools"></a>Jak využívat data pomocí externích nástrojů?

Telemetrická data lze zpracovávat a vizualizovat pomocí následujících nástrojů:

- Power BI
- Application Insights
- Azure Monitor (dříve Shoebox)
- Řídicí panel AMS Live
- Azure Portal (čekající na vydání)

### <a name="how-to-manage-data-retention"></a>Jak spravovat uchovávání dat?

Telemetrický systém neposkytuje správu uchovávání dat nebo automatické odstranění starých záznamů. Proto je třeba spravovat a odstranit staré záznamy ručně z tabulky úložiště. Můžete odkazovat na úložiště SDK postup.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
