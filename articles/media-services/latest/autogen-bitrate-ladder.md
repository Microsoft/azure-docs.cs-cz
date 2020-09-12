---
title: Kódování videí pomocí standardu Encoder v Media Services – Azure | Microsoft Docs
description: V tomto tématu se dozvíte, jak používat standardní kodér v Media Services ke kódování vstupního videa s automaticky generovaným žebříkem rychlosti na základě vstupních rozlišení a přenosové rychlosti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 05accd69f1868b8b0e0f6dbd4fb5c21ee843ec5e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297711"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódování pomocí automaticky generovaného žebříku přenosové rychlosti

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak používat standardní kodér v Media Services ke kódování vstupního videa do automaticky generovaného panelu přenosové rychlosti (páry rozlišení přenosů) na základě rozlišení vstupu a přenosové rychlosti. Toto vestavěné nastavení kodéru nebo předvolba nebude nikdy přesáhnout vstupní rozlišení a přenosovou rychlost. Pokud je například vstup ve formátu 720p ve 3 MB/s, výstup bude ve formátu 720p nejlepší a bude začínat tarify nižšími než 3 MB/s.

### <a name="encoding-for-streaming"></a>Kódování pro streamování

Při použití předdefinovaného **AdaptiveStreaming** v **transformaci**získáte výstup, který je vhodný pro doručování přes protokoly streamování, jako je HLS nebo pomlčka. Při použití této předvolby služba inteligentně určuje, kolik vrstev videa se má vygenerovat a jaký přenosová rychlost a řešení. Výstupní obsah obsahuje soubory MP4, kde video s kódováním ve formátu AAC a H. 264 není prokládané.

Příklad toho, jak se tato předvolba používá, najdete v tématu [streamování souboru](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Výstup

V této části jsou uvedeny tři příklady výstupních vrstev videí vytvořených nástrojem Media Services Encoder v důsledku kódování s přednastavenou **AdaptiveStreaming** . Ve všech případech výstup obsahuje pouze zvukový soubor MP4 s stereo zvuk kódovaný při 128 kb/s.

### <a name="example-1"></a>Příklad 1
Zdroj s výškou "1080" a snímkem "29,970" vytváří 6 vrstev videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výškou "720" a snímkem "23,970" vytváří 5 vrstev videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výškou "360" a snímkem "29,970" vytvoří 3 vrstvy videa:

|Vrstva|Height (Výška)|Width (Šířka)|Přenosová rychlost (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
