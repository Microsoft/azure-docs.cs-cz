---
title: Azure Media Player přepisu adresy URL
description: Azure Media Player přepíše zadanou adresu URL z Azure Media Services a poskytne streamování pro hladké, PŘERUŠOVANé, HLS v3 a HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: efa77ce7416b94331dce2bb4e7f77dd50c20d450
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448572"
---
# <a name="url-rewriter"></a>Přepisovač adres URL #

Ve výchozím nastavení Azure Media Player přepíše zadanou adresu URL z Azure Media Services, aby poskytovala proudy pro hladké, PŘERUŠOVANé, HLS v3 a HLS v4. Například pokud je zdroj uveden následujícím způsobem, Azure Media Player zajistěte, aby se pokusy o přehrání všech výše uvedených protokolů:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Pokud ale chcete přepsat adresu URL, můžete to udělat tak, že `disableUrlRewriter` do parametru přidáte vlastnost. To znamená, že všechny informace, které jsou předány do zdrojů, jsou přímo předány do přehrávače beze změny.  Tady je příklad přidání dvou zdrojů do přehrávače, na PŘERUŠOVANém a jednom HLADKÉm streamování.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

nebo

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Pokud chcete, můžete také zadat konkrétní formáty streamování, které chcete přepsat, aby bylo možné tento parametr Azure Media Player použít `streamingFormats` . Mezi možnosti patří `DASH` , `SMOOTH` ,, `HLSv3` `HLSv4` , `HLS` . Rozdíl mezi HLS a HLSv3 & v4 je v tom, že formát HLS podporuje přehrávání FairPlay obsahu. Verze v3 a v4 nepodporují FairPlay. To je užitečné, pokud nemáte zásady doručování pro konkrétní protokol k dispozici.  Tady je příklad, kdy není pro váš Asset povolený protokol typu SPOJOVNÍK.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

nebo

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Výše uvedené dvě lze v kombinaci vzájemně použít pro více okolností na základě vašeho konkrétního assetu.

> [!NOTE]
> Informace o ochraně Widevine trvají jenom u protokolu POMLČKy.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)