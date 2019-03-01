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
ms.date: 02/28/2019
ms.author: juliako
ms.openlocfilehash: 9671d9f61b610a85cbf2475e045c641a29dac11b
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010612"
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
| Maximální vrstvy ve výstupu| Stejné jako vstup|Až 6|
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

## <a name="next-steps"></a>Další postup

[Přehled živého streamování](live-streaming-overview.md)
