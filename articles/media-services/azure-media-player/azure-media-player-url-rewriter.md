---
title: Vyzápis adres URL přehrávače médií Azure
description: Azure Media Player přepíše danou adresu URL ze služby Azure Media Services, aby poskytoval datové proudy pro SMOOTH, DASH, HLS v3 a HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726456"
---
# <a name="url-rewriter"></a>Vypisovač adres URL #

Ve výchozím nastavení program Azure Media Player přepíše danou adresu URL ze služby Azure Media Services, aby poskytoval datové proudy pro aplikace SMOOTH, DASH, HLS v3 a HLS v4. Pokud je například zdroj uveden následujícím způsobem, azure media player zajistí, že se pokusí přehrát všechny výše uvedené protokoly:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Pokud však nechcete nepoužívat vypisovač adres URL, můžete tak učinit přidáním vlastnosti `disableUrlRewriter` do parametru. To znamená, že všechny informace, které jsou předány zdrojům, jsou přímo předány hráči bez úprav.  Zde je příklad přidání dvou zdrojů do přehrávače, na DASH a jeden HLADKÝ Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

– nebo –

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Pokud chcete, můžete také zadat konkrétní formáty streamování, které chcete, aby `streamingFormats` program Azure Media Player přepsal na použití parametru. Mezi `DASH`možnosti patří `SMOOTH`, , `HLSv3`, `HLSv4`. `HLS`. Rozdíl mezi HLS a HLSv3 & v4 spoje na tom, že formát HLS podporuje přehrávání obsahu FairPlay. v3 a v4 nepodporují FairPlay. To je užitečné, pokud nemáte k dispozici zásady doručování pro konkrétní protokol.  Zde je příklad, kdy protokol DASH není povolen s vaším datovým zdrojem.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

– nebo –

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Výše uvedené dva mohou být použity v kombinaci s sebou pro více okolností na základě vašeho konkrétního majetku.

> [!NOTE]
> Informace o ochraně révy pouze přetrvává v protokolu DASH.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)