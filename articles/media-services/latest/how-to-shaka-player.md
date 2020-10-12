---
title: Jak používat Shaka Player s Azure Media Services
description: Tento článek vysvětluje, jak používat Shaka Player s Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: e4be3d4508d342c77507cf824fb036e32084b617
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329733"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Jak používat Shaka Player s Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Přehled

Shaka Player je open source knihovna JavaScriptu pro adaptivní média. V prohlížeči hraje adaptivní formáty médií (například POMLČKy a HLS), aniž by používaly moduly plug-in nebo Flash. Místo toho přehrávač Shaka používá rozšíření zdroje médií Open Web Standards a Encrypted Media Extensions.

Doporučujeme použít [Mux.js](https://github.com/videojs/mux.js/) jako bez něj, aby měl přehrávač Shaka podporu formátu HLS CMAF, ale ne HLS TS.

Jeho oficiální dokumentaci najdete v [dokumentaci ke službě Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Ukázka kódu
Vzorový kód tohoto článku je k dispozici v [Azure-Samples/Media-Services-3rdParty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementace přehrávače

Pokud potřebujete implementovat svou vlastní instanci přehrávače, postupujte podle těchto pokynů:

1. Vytvořte `index.html` soubor, do kterého budete hostovat přehrávač. Přidejte následující řádky kódu (v případě potřeby můžete nahradit verze novější):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Přidejte soubor JavaScriptu s následujícím kódem:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Nahraďte `manifestUrl` HLS nebo pomlčkou URL z lokátoru streamování vašeho assetu, který najdete na stránce Lokátor streamování v Azure Portal.
    ![Adresy URL lokátoru streamování](media/how-to-shaka-player/streaming-urls.png)

1. Spusťte server (například s `npm http-server` ) a přehrávač by měl pracovat...

## <a name="set-up-captions"></a>Nastavit titulky

### <a name="set-up-vod-captions"></a>Nastavení popisků VOD

Spusťte následující řádky kódu a nahraďte je `captionUrl` adresářem. vtt (soubor VTT musí být ve stejném hostiteli, aby se předešlo chybě CORS), `lang` s kódem pro jazyk a s oběma písmeny `type` `caption` nebo `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Nastavení titulků živého streamu

Povolení titulků v živém streamu se konfiguruje přidáním následujícího řádku kódu:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Nastavení ověřování tokenů

Spusťte následující řádky kódu a nahraďte `token` řetězcem tokenu:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Nastavení šifrování AES-128

Shaka Player v současné době nepodporuje šifrování AES-128.

Odkaz na [problém](https://github.com/google/shaka-player/issues/850) GitHubu, který bude sledovat stav této funkce.

## <a name="set-up-drm-protection"></a>Nastavení ochrany DRM

Shaka Player používá rozšíření EME (Encrypted Media Extensions), které vyžaduje zabezpečenou adresu URL pro použití. Proto je pro testování veškerého obsahu chráněného technologií DRM nutné používat protokol HTTPS. Pokud lokalita používá protokol HTTPS, pak manifest a každý segment budou také muset používat protokol HTTPS. Důvodem je smíšený požadavek na obsah.

Pořadí předvolbách pro Shaka správu těchto adres (e) na svých licenčních serverech:

1. ClearKey config, který se používá pro ladění, by měl přepsat všechno ostatní. (Aplikace může pořád zadat licenční server ClearKey.)
2. Servery, které jsou v nějakém případě k dispozici, by měly přepsat cokoli z manifestu.
3. Licenční servery poskytované manifestem se používají pouze v případě, že není zadán žádný jiný.

Pokud chcete zadat adresu URL licenčního serveru pro Widevine nebo PlayReady, můžeme použít následující kód:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Veškerý obsah FairPlay vyžaduje nastavení certifikátu serveru. Nastavuje se v konfiguraci přehrávače:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Další informace najdete v [dokumentaci k ochraně DRM v Shaka Playeru](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Další kroky

* [Použití Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Rychlý Start: šifrování obsahu](encrypt-content-quickstart.md)
