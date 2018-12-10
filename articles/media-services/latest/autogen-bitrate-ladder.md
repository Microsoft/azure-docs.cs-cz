---
title: Kódování videa s využitím kodér úrovně Standard ve službě Media Services – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak použít kodér úrovně Standard ve službě Media Services ke kódování vstupní video automaticky generovaný adaptivní žebříček přenosových rychlostí, na základě vstupního rozlišení a s přenosovou rychlostí.
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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 976e1ea013c6a30615bdc742ed79e76ec35258a1
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132802"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódování pomocí automaticky generovaný adaptivní žebříček přenosových rychlostí

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak můžete zakódovat jako vstupní video automaticky generovaný adaptivní žebříček přenosových rychlostí (řešení s přenosovou rychlostí páry) na základě vstupního rozlišení a s přenosovou rychlostí pomocí kodér úrovně Standard ve službě Media Services. Toto integrované kodér nastavení nebo předvolbu, se nikdy nepřekročí vstupního rozlišení a s přenosovou rychlostí. Například pokud je vstup 720p na 3 MB/s, výstup zůstane 720p nejlépe a začne sazby nižší než 3 MB/s.

### <a name="encoding-for-streaming"></a>Kódování pro streamování

Při použití **AdaptiveStreaming** přednastavených v **transformace**, získáte výstup, který je vhodný pro doručení přes datový proud protokolů, jako jsou HLS a DASH. Při použití tohoto nastavení, služby inteligentně Určuje, kolik vrstvy videa k vygenerování a na jaké s přenosovou rychlostí a řešení. Výstup obsahuje soubory MP4, kde není prokládané kódováním AAC zvuku a videa s kódováním H.264.

Příkladem použití tohoto nastavení najdete v tématu [Stream souboru](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Výstup

Tato část ukazuje tři příklady videa vrstvy výstup vytvořený kodér Media Services v důsledku kódování s **AdaptiveStreaming** předvolby. Ve všech případech se výstup obsahuje pouze zvukový soubor MP4 s stereo zvuk kódování na 128 kb/s.

### <a name="example-1"></a>Příklad 1
Zdroj s výšku "1080" a "29.970" snímkovou vytvoří 6 videa vrstvy:

|Vrstva|Výška|Šířka|S přenosovou rychlostí (kb/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Příklad 2
Zdroj s výšku "720" a "23.970" snímkovou vytvoří 5 videa vrstvy:

|Vrstva|Výška|Šířka|S přenosovou rychlostí (kb/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Příklad 3
Zdroj s výšku "360" a "29.970" snímkovou vytvoří vrstvy 3 videa:

|Vrstva|Výška|Šířka|S přenosovou rychlostí (kb/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
