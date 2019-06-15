---
title: Azure Media Services Livestream typů | Dokumentace Microsoftu
description: Tento článek popisuje podrobný tabulku, která porovnání Livestream typů.
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075007"
---
# <a name="live-event-types-comparison"></a>Porovnání typů živé události

Ve službě Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: kódování v reálném čase a předávací. 

## <a name="types-comparison"></a>Porovnání typů 

Následující tabulka obsahuje porovnání funkcí typů živá událost.

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

Rozlišení a přenosových rychlostí obsažené ve výstupu kodér služby live Encoding je určeno [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Pokud se používá **standardní** live encoder (LiveEventEncodingType.Standard), pak bude *Default720p* přednastavení určuje sadu párů 6 řešení/bit míra je popsáno níže. Jinak, pokud se používá **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), pak bude *Default1080p* přednastavených specifiesthe výstupní sada párů rychlost řešení/bit. 

### <a name="output-video-streams-for-default720p"></a>Výstupní datové proudy videa pro Default720p

**Default720p** vstupního videa se zakódovat do následujících 6 vrstvy.

| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil | Název výstupní Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoká |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Vysoká |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Vysoká |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Vysoká |Video_512x288_850kbps |
| 550 |384 |216 |30 |Vysoká |Video_384x216_550kbps |
| 200 |340 |192 |30 |Vysoká |Video_340x192_200kbps |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování předvolbu, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva s rozlišením 720p a maximálně 6 vrstev celkem. Také určete, že žádáte přednastavený kontext pro kodér úrovně Standard za provozu.

### <a name="output-video-streams-for-default1080p"></a>Výstupní datové proudy videa pro Default1080p

**Default1080p** vstupního videa se zakódovat do následujících 6 vrstvy.

| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil | Název výstupní Stream |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Vysoká |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Vysoká |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Vysoká |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Vysoká |Video_640x360_800kbps |
| 400 |480 |270 |30 |Vysoká |Video_480x270_400kbps |
| 200 |320 |180 |30 |Vysoká |Video_320x180_200kbps |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování předvolbu, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva na 1080p a maximálně 6 vrstvy. Také určete, že žádáte přednastavení Premium1080p live encoder.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Výstup zvukový Stream Default720p a Default1080p

U obou *Default720p* a *Default1080p* přednastavení, zvuku je zakódován do stereo AAC-LC na 128 kb/s, četnost 48 kHz.

## <a name="next-steps"></a>Další postup

[Přehled živého streamování](live-streaming-overview.md)
