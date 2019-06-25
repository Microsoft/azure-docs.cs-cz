---
title: Azure Media Services Livestream typů | Dokumentace Microsoftu
description: Tento článek popisuje podrobný tabulku, která srovnává Livestream typy.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150386"
---
# <a name="live-event-types-comparison"></a>Porovnání typů živé události

Ve službě Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: kódování v reálném čase a předávací. 

## <a name="types-comparison"></a>Porovnání typů 

Následující tabulka obsahuje porovnání funkcí typů živá událost. Typy mají nastavený během vytváření pomocí [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** – místní kodér služby live Encoding odešle datový proud více přenosovými rychlostmi. Ingestované datové proudy prochází živá událost bez dalšího zpracování. 
* **LiveEventEncodingType.Standard** – místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí živá událost a služba Media Services vytvoří různých datových proudů s přenosovou rychlostí. Pokud je příspěvek kanál 720p nebo vyšší rozlišení **Default720p** se předvolba kódování sada párů 6 řešení/s přenosovou rychlostí (podrobnosti postupujte dále v tomto článku).
* **LiveEventEncodingType.Premium1080p** – místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí živá událost a služba Media Services vytvoří různých datových proudů s přenosovou rychlostí. Přednastavení Default1080p Určuje výstup sadu párů řešení/s přenosovou rychlostí (podrobnosti postupujte dále v tomto článku). 

| Funkce | Předávací živou událost | Standardní nebo Premium1080p živou událost |
| --- | --- | --- |
| Vstup s jednou přenosovou rychlostí je zakódován do více přenosových rychlostí v cloudu |Ne |Ano |
| Maximální rozlišení příspěvek informačního kanálu |4K (4096 × 2160 na 60 snímků za sekundu) |1080p (1920 × 1088 na 30 snímků za sekundu)|
| Doporučená maximální vrstvy v příspěvku informačního kanálu|Až 12|Jedno audio|
| Maximální vrstvy ve výstupu| Stejné jako vstup|Až 6 (viz níže přednastavení systému)|
| Maximální agregovaná šířka pásma příspěvku informačního kanálu|60 MB/s|neuvedeno|
| Maximální přenosovou rychlost pro jednu vrstvu v příspěvku |20 MB/s|20 MB/s|
| Podpora pro více jazyka zvukové stopy|Ano|Ne|
| Podporované kodeky vstupního videa |H.264/AVC a H.265/HEVC|H.264/AVC|
| Video kodeků podporované výstupu|Stejné jako vstup|H.264/AVC|
| Podporované videa bitová hloubka, vstupu a výstupu|Až 10-bit včetně HDR 10/HLG|8 bitů|
| Podporované kodeky vstupního zvuku|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Podporované výstup zvukových kodeků|Stejné jako vstup|AAC-LC|
| Maximální rozlišení výstup videa|Stejné jako vstup|Standard – 720p Premium1080p - 1080p|
| Maximální Snímková frekvence vstupního videa|60 snímků za sekundu|Standardní nebo Premium1080p – 30 snímků za sekundu|
| Vstupní protokoly|RTMP, fragmentovaný soubor MP4 (technologie Smooth Streaming)|RTMP, fragmentovaný soubor MP4 (technologie Smooth Streaming)|
| Cena|Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video"|Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video"|
| Maximální doba běhu| 24 hodin x 365 dní živé lineární | Až 24 hodin|
| Možnost předat prostřednictvím vloženého 608 skryté titulky CEA/708 popisky dat|Ano|Ano|
| Podpora vkládaní slaty|Ne|Ne|
| Podpora ad signalizace přes rozhraní API| Ne|Ne|
| Podpora ad signalizace prostřednictvím SCTE 35 integrovaných zprávy|Ano|Ano|
| Schopnost zotavení z stručný přepínání v příspěvku informačního kanálu|Ano|Částečně|
| Podpora pro vstupní GOPs nerovnoměrné|Ano|Ne – vstup musí opravily GOP doba trvání|
| Podpora pro vstup míra proměnné rámce|Ano|Ne – vstup musí být stanovena snímkovou frekvenci. Malé změny jsou například tolerovat při vysoké pohybu scény. Ale příspěvek informačního kanálu nelze vyřadit, frekvence snímků (například 15 snímků za sekundu).|
| Auto přístupnými živé události při zadání kanálu dojde ke ztrátě|Ne|Po 12 hodinách, pokud neexistuje žádný LiveOutput spuštění|

## <a name="system-presets"></a>Předvolby systému

Rozlišení a přenosových rychlostí obsažené ve výstupu kodér služby live Encoding jsou určeny [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Pokud se používá **standardní** live encoder (LiveEventEncodingType.Standard), pak bude *Default720p* přednastavení určuje sadu párů 6 řešení/s přenosovou rychlostí je popsáno níže. Jinak, pokud se používá **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), pak bude *Default1080p* přednastavení určuje výstupní sada párů řešení/s přenosovou rychlostí.

> [!NOTE]
> Nelze použít Default1080p přednastaveny tak, aby živá událost, pokud bylo nastavení standardní kódování v reálném čase – se zobrazí chyba. Pokud se pokusíte použít Default720p přednastaveny tak, aby Premium1080p live encoder získají k chybě.

### <a name="output-video-streams-for-default720p"></a>Výstupní datové proudy videa pro Default720p

Pokud je příspěvek kanál 720p nebo vyšší rozlišení **Default720p** bude předvolbu kódování informačního kanálu do následujících 6 vrstev. V následující tabulce je s přenosovou rychlostí v kb/s, MaxFPS představuje tuto maximální povolené snímkovou frekvenci (v snímků za sekundu), profil představuje H.264 profil použít.

| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoká |
| 2200 |960 |540 |30 |Vysoká |
| 1350 |704 |396 |30 |Vysoká |
| 850 |512 |288 |30 |Vysoká |
| 550 |384 |216 |30 |Vysoká |
| 200 |340 |192 |30 |Vysoká |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování předvolbu, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva s rozlišením 720p a maximálně 6 vrstev celkem. Také určete, že žádáte přednastavený kontext pro kodér úrovně Standard za provozu.
> Konkrétní hodnoty přenosových rychlostí a jejich řešení může být upraveno v čase

### <a name="output-video-streams-for-default1080p"></a>Výstupní datové proudy videa pro Default1080p

Pokud je příspěvek kanál rozlišení 1080p **Default1080p** bude předvolbu kódování informačního kanálu do následujících 6 vrstev.

| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Vysoká |
| 3000 |1280 |720 |30 |Vysoká |
| 1600 |960 |540 |30 |Vysoká |
| 800 |640 |360 |30 |Vysoká |
| 400 |480 |270 |30 |Vysoká |
| 200 |320 |180 |30 |Vysoká |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování předvolbu, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva na 1080p a maximálně 6 vrstvy. Také určete, že žádáte přednastavení Premium1080p live encoder.
> Konkrétní hodnoty přenosových rychlostí a jejich řešení může být upraveno v čase.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Výstup zvukový Stream Default720p a Default1080p

U obou *Default720p* a *Default1080p* přednastavení, zvuku je zakódován do stereo AAC-LC na 128 kb/s. Vzorkovací frekvence následně jejich zvukové stopy v příspěvku informačního kanálu.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicitní vlastnosti kodér služby live Encoding

Vlastnosti live Encoder, který se dá řídit explicitně, prostřednictvím přednastavení – například počet vrstev, rozlišení a přenosových rychlostí popsaného v předchozí části. Tato část vysvětluje implicitní vlastnosti.

### <a name="group-of-pictures-gop-duration"></a>Skupiny trvání obrázky (GOP)

Následuje za kodér [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) struktura příspěvek kanál –, což znamená, že výstup vrstvy budou mít stejnou dobu trvání GOP. Proto doporučujeme, abyste nakonfigurovali místní kodér pro vytvoření kanálu příspěvek, který se má vyřešit GOP doba trvání (obvykle 2 sekundy). Tím se zajistí, že odchozí HLS a MPEG DASH datové proudy ze služby také chyba opravena GOP doby trvání. Malé změny dob trvání GOP budou pravděpodobně tolerovat většina zařízení.

### <a name="frame-rate"></a>Snímková frekvence

Kodér služby live Encoding se také řídí doby trvání jednotlivých snímky videí v příspěvku kanál – to znamená, že výstup vrstvy budou mít snímků se stejnou dobu trvání. Proto doporučujeme, abyste nakonfigurovali místní kodér vytvoří příspěvek informačního kanálu, který má pevnou snímkovou frekvenci (nejvýše 30 snímků za sekundu). Tím se zajistí, že odchozí HLS a MPEG DASH datové proudy ze služby také chyba opravena dob trvání sazby rámce. Malé změny snímkových může tolerovat většina zařízení, ale neexistuje žádná záruka, kodér služby live Encoding vytvoří výstup, která bude přehrána správně. Vaše místní kodér služby live Encoding by neměl být vyřadit snímků (např.) za podmínek při nízkém stavu baterie) nebo různé Snímková frekvence žádným způsobem.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozlišení příspěvku datového kanálu a výstupu vrstvy

Kodér služby live Encoding je nakonfigurován, aby upconverting příspěvek informačního kanálu. V důsledku maximální rozlišení vrstvy výstup nesmí překročit hodnotu příspěvku informačního kanálu.

Při odesílání příspěvků datového kanálu na 720p pro živá událost nakonfigurovaný pro Default1080p živé kódování, výstup bude mít jenom 5 vrstvy, počínaje 720p na 3 MB/s, přejděte dolů na 200 kb/s 1080 p. Nebo pokud odesílání příspěvků datového kanálu na 360p do živá událost nakonfigurované pro standardní živého kódování, výstup bude obsahovat 3 vrstvy (při rozlišení 288p, 216p a 192p). V případě degenerovanou odeslat příspěvek informační kanál, třeba 160 x 90 pixelů za kodér úrovně Standard, bude obsahovat výstup jedné vrstvy rozlišením 160 x 90 na stejné přenosové rychlosti jako příspěvek informačního kanálu.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>S přenosovou rychlostí příspěvku datového kanálu a výstupu vrstvy

Kodér služby live Encoding je nakonfigurována tak bude respektovat nastavení s přenosovou rychlostí do nastavení, bez ohledu na to s přenosovou rychlostí příspěvku informačního kanálu. V důsledku přenosové rychlosti vrstvy výstup může přesáhnout přístupnost příspěvku informačního kanálu. Například pokud předáte příspěvků datového kanálu na rozlišení obrazovky 720p na 1 MB/s, vrstvy výstup zůstane stejná jako v [tabulky](live-event-types-comparison.md#output-video-streams-for-default720p) výše.

## <a name="next-steps"></a>Další postup

[Přehled živého streamování](live-streaming-overview.md)
