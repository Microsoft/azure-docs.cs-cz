---
title: Kódování videí pomocí standardního kodéru ve službách Media Services – Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí standardního kodéru ve službě Media Services kódovat vstupní video pomocí automaticky generovaného žebříku přenosové rychlosti na základě vstupního rozlišení a přenosového toku.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733313"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódování pomocí automaticky generovaného žebříku s přenosovou rychlostí

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak pomocí standardního kodéru ve službě Media Services kódovat vstupní video do automaticky generovaného žebříku přenosové rychlosti (dvojice bitrate-rozlišení) na základě vstupního rozlišení a přenosového toku. Toto nastavení vestavěného kodéru nebo přednastavení nikdy nepřekročí vstupní rozlišení a přenosový tok. Například pokud je vstup 720p při 3 Mbps, výstup zůstane v nejlepším případě 720p a začne rychlostí nižší než 3 Mbps.

### <a name="encoding-for-streaming"></a>Kódování pro streamování

Při použití **AdaptiveStreaming** přednastavení v **transformaci**, získáte výstup, který je vhodný pro doručení prostřednictvím streaming protokoly jako HLS a DASH. Při použití tohoto přednastavení služba inteligentně určuje, kolik vrstev videa má generovat a při jakém přenosovém toku a rozlišení. Výstupní obsah obsahuje soubory MP4, kde není prokládáno zvuk kódovaný AAC a video kódované H.264.

Příklad použití tohoto přednastavení najdete v [tématu Streamování souboru](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Výstup

Tato část ukazuje tři příklady výstupních vrstev videa vytvořených kodérem Mediálních služeb v důsledku kódování s **přednastavením AdaptiveStreaming.** Ve všech případech, výstup obsahuje audio-pouze MP4 soubor se stereofonním zvukem kódované na 128 kbps.

### <a name="example-1"></a>Příklad 1
Zdroj s výškou "1080" a snímkovou frekvencí "29.970" vytváří 6 vrstev videa:

|Vrstva|Vlastnost Height|impulzu|Bitová rychlost (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výškou "720" a snímkovou frekvencí "23.970" vytváří 5 vrstev videa:

|Vrstva|Vlastnost Height|impulzu|Bitová rychlost (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výškou "360" a snímkovou frekvencí "29.970" vytváří 3 vrstvy videa:

|Vrstva|Vlastnost Height|impulzu|Bitová rychlost (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
