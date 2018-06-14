---
title: Azure Media Services Telemetrie | Microsoft Docs
description: Tento článek nabízí přehled Azure Media Services telemetrie.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 1b26d7925fe5bd39905d9f51d22433b1eea43af6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23861106"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services Telemetrie

Azure Media Services (AMS) umožňuje přístup k datům telemetrie/metriky pro jeho služby. Aktuální verze AMS umožňuje za provozu shromažďování telemetrických dat pro **kanál**, **StreamingEndpoint**a za chodu **archivu** entity. 

Telemetrie je zapsán do tabulky úložiště v účtu Azure Storage, který zadáte (většinou použijete účet úložiště, které jsou přidružené k účtu AMS). 

Systém telemetrie nespravuje uchovávání. Stará data telemetrie můžete odebrat odstraněním úložiště tabulek.

Toto téma popisuje postup konfigurace a využívat telemetrie AMS.

## <a name="configuring-telemetry"></a>Konfigurace telemetrie

Telemetrie můžete konfigurovat na úrovni rozlišením součásti. Existují dvě úrovně podrobností "Normální" a "Podrobné". V současné době obou úrovních vrátí stejné informace. Doporučuje se použít "normální. 

Následující témata ukazují, jak povolit telemetrická data:

[Povolení telemetrie s rozhraním .NET](media-services-dotnet-telemetry.md) 

[Povolení telemetrie se zbytkem](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Využívání telemetrické informace

Telemetrie je zapsán do tabulky úložiště služby Azure v účtu úložiště, který jste zadali při konfiguraci telemetrie pro účet Media Services. Tato část popisuje tabulky úložiště pro metriku.

Můžete využívat telemetrická data v jednom z následujících způsobů:

- Číst data přímo z úložiště tabulek Azure (např. pomocí sady SDK úložiště). Popis telemetrie úložiště tabulek naleznete v tématu **využívání telemetrické informace** v [to](https://msdn.microsoft.com/library/mt742089.aspx) tématu.

Nebo

- Použít podporu v .NET SDK služby Media Services pro čtení dat úložiště, jak je popsáno v [to](media-services-dotnet-telemetry.md) tématu. 


Umožnit dobrý výkon v rámci Azure Table Storage je navržen tak schéma telemetrie popsané dál:

- Data je rozdělena na oddíly pomocí účtu ID a ID služby povolit telemetrie z každé služby prohledána nezávisle.
- Oddíly obsahovat datum umožnit přiměřené horní mez na požadovanou velikost oddílu.
- Řádek klíče jsou v pořadí zpětné čas umožňující nejaktuálnějších telemetrii položky, které chcete zadat dotaz pro danou službu.

To by mělo umožnit řadu běžných dotazů účinný:

- Paralelní, nezávislé stahování dat pro samostatné služby.
- Načítání všech dat pro danou službu v časovém období.
- Načítání nejnovější data pro službu.

### <a name="telemetry-table-storage-output-schema"></a>Telemetrie tabulky úložiště výstupního schématu

Telemetrická data jsou uložena v agregační funkci v jedné tabulce, "TelemetryMetrics20160321", kde "20160321" je datum vytvoření tabulky. Telemetrie systém vytvoří pro každý nový den založené na UTC 00:00 do samostatné tabulky. Tabulka se používá k ukládání opakované hodnoty jako například ingestování přenosovou rychlostí v rámci daného časového období čas, počet odeslaných bajtů, atd. 

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
Klíč oddílu|{ID účtu} _ {entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66 < br /<br/>ID účtu je součástí klíče oddílu pro zjednodušení pracovní postupy, kde jsou více účtů Media Services zápis do stejného účtu úložiště.
RowKey|{sekund půlnoc} _ {náhodná hodnota}|01688_00199<br/><br/>Klíč řádku začíná počet sekund, po půlnoci tak, aby dotazy top n styl v rámci oddílu. Další informace najdete v tématu [to](../cosmos-db/table-storage-design-guide.md#log-tail-pattern) článku. 
časové razítko|Datum a čas|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ entity, který poskytuje data telemetrie|Kanál/StreamingEndpoint/archivu<br/><br/>Typ události je právě hodnotu řetězce.
Name (Název)|Název události telemetrie|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Čas (UTC) došlo k události telemetrie|2016-09-09T22:42:36.924Z<br/><br/>Sledovaného časového zajišťuje jednotku, která odesílá telemetrická data (například kanál). Může být v době Přibližná problémy synchronizace mezi součástmi, tato hodnota je
ServiceID|{ID služby}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Vlastnosti specifické pro entitu|Podle definice události|StreamName: stream1, přenosovou rychlostí 10123...<br/><br/>Ostatní vlastnosti jsou definovány pro typ dané události. Azure obsahu tabulky je párů klíčových hodnot.  (to znamená, že různé řádky v tabulce mají různé sady vlastností).

### <a name="entity-specific-schema"></a>Specifické pro entitu schématu

Existují tři typy telemetrickými záznamovými data specifická pro entity položek vložena se následující frekvence:

- Koncové body streamování: každých 30 sekund.
- Live kanály: každou minutu
- Live archivu: každou minutu

**Koncový bod streamování**

Vlastnost|Hodnota|Příklady
---|---|---
Klíč oddílu|Klíč oddílu|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
časové razítko|časové razítko|Automatické časové razítko z Azure Table 2016-09-09T22:43:42.241Z
Typ|Typ|StreamingEndpoint
Name (Název)|Name (Název)|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Název hostitele|Název hostitele koncového bodu|builddemoserver.Origin.mediaservices.Windows.NET
statusCode|Stav záznamů protokolu HTTP|200
resultCode|Podrobnosti výsledku kódu|S_OK
RequestCount|Celkový počet požadavku v agregace|3
BytesSent|Agregované počet odeslaných bajtů|2987358
ServerLatency|Server Průměrná latence (včetně úložiště)|129
E2ELatency|Průměrná latence začátku do konce|250

**Živého kanálu**

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
Klíč oddílu|Klíč oddílu|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
časové razítko|časové razítko|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Kanál
Name (Název)|Name (Název)|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ sledování video nebo zvuk nebo textu|video nebo zvuk
TrackName|Název dráhy|video nebo audio_1
Přenosovou rychlostí|Sledování přenosovou rychlostí|785000
CustomAttributes –||   
IncomingBitrate|Skutečné příchozí přenosovou rychlostí|784548
OverlapCount|Překrývat v ingestování|0
DiscontinuityCount|Nespojitost pro sledování|0
LastTimestamp|Časové razítko poslední ingestovaný dat|1800488800
NonincreasingCount|Počet fragmentů zrušeny kvůli bez zvýšení časového razítka|2
UnalignedKeyFrames|Zda dostali jsme fragment(s) (v rámci úrovně kvality), kde klíč rámce nejsou zarovnány |True
UnalignedPresentationTime|Jestli dostali jsme fragment(s) (v rámci úrovně kvality nebo sleduje), kde není zarovnána čas prezentace|True
UnexpectedBitrate|Hodnotu True pokud vypočítat skutečné přenosovou rychlostí pro zvuk a video sledovat > 40 000 počtu bitů za sekundu a IncomingBitrate == nebo IncomingBitrate a actualBitrate lišit o 50 % 0 |True
V pořádku|Hodnota TRUE, pokud <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> jsou všechny 0|True<br/><br/>V pořádku je složené funkce, která vrátí hodnotu false, pokud obsahovat žádné z následujících podmínek:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Za provozu archivu**

Vlastnost|Hodnota|Příklady a poznámky
---|---|---
Klíč oddílu|Klíč oddílu|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
časové razítko|časové razítko|Automatické časové razítko z tabulky Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Archiv
Name (Název)|Name (Název)|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID služby|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Adresa url programu|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba.ISM
TrackName|Název dráhy|audio_1
TrackType|Typ dráhy|Zvuku a videa
Atribut CustomAttribute|Hex řetězec, který se odlišuje mezi různé sledování se stejným názvem a přenosovou rychlostí (více fotoaparát úhel)|
Přenosovou rychlostí|Sledování přenosovou rychlostí|785000
V pořádku|Hodnota TRUE, pokud FragmentDiscardedCount == 0 & & ArchiveAcquisitionError hodnotu false|True (tyto dvě hodnoty se nenacházejí v metriku, ale jsou přítomna v zdroj události)<br/><br/>V pořádku je složené funkce, která vrátí hodnotu false, pokud obsahovat žádné z následujících podmínek:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Obecné otázky a odpovědi

### <a name="how-to-consume-metrics-data"></a>Jak využívat data metriky?

Metriky data se ukládají jako řadu tabulky Azure v účtu úložiště zákazníka. Tato data mohou být využívány pomocí následující nástroje:

- AMS SDK
- Microsoft Azure Storage Explorer (podporuje export do formátu hodnot oddělených čárkami a zpracování v aplikaci Excel)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Postup nalezení průměrné využití šířky pásma?

Průměrné využití šířky pásma je průměr BytesSent přes časový rozsah, v.

### <a name="how-to-define-streaming-unit-count"></a>Jak definovat streamování počet jednotku?

Počet jednotek streamování, může být definováno jako propustnost ve špičce ze služby koncových bodů streamování dělený ve špičce propustnost jeden koncový bod streamování. Propustnost použitelné ve špičce jeden koncový bod streamování je 160 MB/s.
Předpokládejme například, že ve špičce propustnost ze služby zákazníka je 40 MB/s (maximální hodnota, která BytesSent přes časový rozsah, v). Potom streamování počet jednotky je rovna (40 MB/s) * (8 bitů/bajtů) /(160 Mbps) = 2 jednotek streamování.

### <a name="how-to-find-average-requestssecond"></a>Postup nalezení průměrný počet požadavků za sekundu?

Pokud chcete vyhledat průměrný počet požadavků za sekundu, výpočetním přes časový rozsah, v průměrný počet požadavků (RequestCount).

### <a name="how-to-define-channel-health"></a>Jak definovat kanál stavu?

Stavu kanálu je možné definovat jako složené logická funkce tak, že je false v případě, že obsahovat žádné z následujících podmínek:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Jak zjistit nespojitosti?

Ke zjištění nespojitosti, najít všechny záznamy dat kanál kde DiscontinuityCount > 0. Odpovídající časové razítko ObservedTime Určuje dobu, kdy nespojitosti došlo.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak zjistit časové razítko překrytí?

Ke zjištění překrytí. časové razítko, najít všechny záznamy dat kanál kde OverlapCount > 0. Odpovídající časové razítko ObservedTime označuje, že došlo k chybě dobu, na kterých časové razítko překrývá.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Postup nalezení streamování chyby požadavků a důvodů?

Streamování chyby požadavků a důvodech najdete najděte všechny záznamy dat koncový bod streamování, kde ResultCode není roven S_OK. Odpovídající pole StatusCode označuje příčinu selhání požadavku.

### <a name="how-to-consume-data-with-external-tools"></a>Jak využívají data pomocí externích nástrojů?

Telemetrickými záznamovými dat můžete zpracovat a vizualizována s následující nástroje:

- PowerBI
- Application Insights
- Azure monitorování (dříve materiálů uložených)
- AMS Live řídicí panel
- Portál Azure (čeká na uvolnění)

### <a name="how-to-manage-data-retention"></a>Jak spravovat uchovávání dat?

Systém telemetrie neposkytuje Správa uchovávání dat nebo automatické odstranění starých záznamů. Proto musíte ručně odstranit staré záznamy z tabulky úložiště a spravovat. Může být sada SDK úložiště pro postupujte následovně.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
