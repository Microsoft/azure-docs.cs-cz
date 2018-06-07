---
title: Použijte standardní kodér ve službě Azure Media Services ke kódování videa pomocí automaticky generovaný přenosovou rychlostí žebříku | Microsoft Docs
description: Toto téma ukazuje, jak používat standardní kodér ve službě Media Services ke kódování vstup videa s žebříku automaticky generovaný přenosovou rychlostí na základě vstupních řešení a přenosovou rychlostí. Vstupní řešení a přenosovou rychlostí se nikdy překročena. Například pokud je vstupní 720p na 3 MB/s, bude výstup zůstat v nejlépe 720p a spustí tempem nižší než 3 MB/s.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6e447c04f4a94f2fb534ecb0605595a90816431e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638292"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódování s žebříku automaticky generovaný přenosovou rychlostí

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak používat standardní kodér ve službě Media Services k zakódování vstupní video do žebříku automaticky generovaný přenosovou rychlostí (přenosovou rychlostí řešení páry) na základě vstupních řešení a přenosovou rychlostí. Tento předdefinovaný kodér nastavení nebo přednastavení, se nikdy překročit vstupní řešení a přenosovou rychlostí. Například pokud vstupní 720p 3 MB/s, výstup v nejlépe zůstane 720p a začne tempem nižší než 3 MB/s.

### <a name="encoding-for-streaming"></a>Kódování pro streamování

Při použití **AdaptiveStreaming** přednastavené v **transformace**, získat výstup, který je vhodný pro doručení pomocí vysílání datového proudu protokoly jako HLS a POMLČKY. Při použití této přednastavených, službu inteligentně Určuje, kolik vrstvy videa pro generování na jaké přenosovou rychlostí a řešení. Výstup obsahu obsahuje soubory MP4, kde není prokládaný kódováním AAC audio a video kódováním H.264.

Příklad použití tato předvolba najdete v sekci [datového proudu souboru](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Výstup

Tato část uvádí příklady tři vrstvy výstupu videa vytvořeného pomocí kodéru Media Services v důsledku kódování s **AdaptiveStreaming** přednastavené. Výstup ve všech případech, obsahuje pouze zvukový soubor MP4 s stereofonní zvuk kódovaný v 128 kb/s.

### <a name="example-1"></a>Příklad 1
Zdroj s výšky "1080" a "29.970" kmitočet snímků vytváří 6 video vrstvy:

|Vrstva|Výška|Šířka|Přenosovou rychlostí (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výšky "720" a "23.970" kmitočet snímků vytváří 5 video vrstvy:

|Vrstva|Výška|Šířka|Přenosovou rychlostí (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výšky "360" a "29.970" kmitočet snímků vytváří vrstvy 3 videa:

|Vrstva|Výška|Šířka|Přenosovou rychlostí (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
