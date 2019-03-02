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
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243886"
---
# <a name="live-event-types-comparison"></a>Porovnání typů živé události

Ve službě Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: kódování v reálném čase a předávací. 

## <a name="types-comparison"></a>Porovnání typů 

Následující tabulka obsahuje porovnání funkcí těchto dvou typů živá událost.

| Funkce | Předávací živou událost | Standardní živou událost |
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
| Maximální rozlišení výstup videa|Stejné jako vstup|720p (na 30 snímků za sekundu)|
| Vstupní protokoly|RTMP, fragmentovaný soubor MP4 (technologie Smooth Streaming)|RTMP, fragmentovaný soubor MP4 (technologie Smooth Streaming)|
| Cena|Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video"|Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video"|
| Maximální doba běhu| 24 hodin x 365 dní živé lineární | Až 24 hodin|
| Možnost předat prostřednictvím vloženého 608 skryté titulky CEA/708 popisky dat|Ano|Ano|
| Podpora vkládaní slaty|Ne|Ne|
| Podpora ad signalizace přes rozhraní API| Ne|Ne|
| Podpora ad signalizace prostřednictvím SCTE 35 integrovaných zprávy|Ano|Ano|
| Schopnost zotavení z stručný přepínání v příspěvku informačního kanálu|Ano|Ne (živá událost začne slating po sekundách 6 bez vstupních dat)|
| Podpora pro vstupní GOPs nerovnoměrné|Ano|Ne – vstup musí opravily GOP doba trvání|
| Podpora pro vstup míra proměnné rámce|Ano|Ne – vstup musí být stanovena snímkovou frekvenci. Malé změny jsou například tolerovat při vysoké pohybu scény. Ale příspěvek informačního kanálu nelze vyřadit, frekvence snímků (například do 15 snímků za sekundu).|
| Auto přístupnými živé události při zadání kanálu dojde ke ztrátě|Ne|Po 12 hodinách, pokud neexistuje žádný LiveOutput spuštění|

## <a name="system-presets"></a>Předvolby systému

Při použití kódování v reálném čase (živá událost nastavena na **standardní**), použitá předvolba kódování definuje, jak příchozím datovém proudu je zakódován do více přenosových rychlostí nebo vrstvy. V současné době jediná povolená hodnota je přednastavený kontext *Default720p* (výchozí).

**Default720p** bude kódování videa do následujících 6 vrstev.

### <a name="output-video-stream"></a>Výstupní Stream videa

| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil | Název výstupní Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoký |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Vysoký |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Vysoký |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Vysoký |Video_512x288_850kbps |
| 550 |384 |216 |30 |Vysoký |Video_384x216_550kbps |
| 200 |340 |192 |30 |Vysoký |Video_340x192_200kbps |

> [!NOTE]
> Pokud musíte použít vlastní předvolba živé kódování, obraťte se prosím amshelp@microsoft.com. Měli byste určit požadovanou tabulku rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva na 720p a maximálně 6 vrstvy.

### <a name="output-audio-stream"></a>Výstup zvukový Stream

Zvuk je zakódován do stereo AAC-LC na 128 kb/s, vzorkovací frekvenci 48 kHz.

## <a name="next-steps"></a>Další postup

[Přehled živého streamování](live-streaming-overview.md)
