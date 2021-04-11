---
title: Použití přehrávače Video.js s Azure Media Services
description: Tento článek vysvětluje, jak používat objekt HTML video a JavaScript s Azure Media Services
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
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281766"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Jak používat Video.js Player s Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Přehled

Video.js je webový přehrávač, který je sestaven pro založení na světě HTML5. V prohlížeči hraje adaptivní formáty médií (například POMLČKy a HLS), aniž by používaly moduly plug-in nebo Flash. Místo toho Video.js používá rozšíření MediaSource Web Standards a Encrypted Media Extensions. Kromě toho podporuje přehrávání videa na stolních počítačích a mobilních zařízeních.

Jeho oficiální dokumentaci najdete na adrese [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Ukázka kódu
Vzorový kód tohoto článku je k dispozici v [Azure-Samples/Media-Services-3rdParty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementace přehrávače

1. Vytvořte `index.html` soubor, do kterého budete hostovat přehrávač. Přidejte následující řádky kódu (v případě potřeby můžete nahradit verze novější):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Přidejte `index.js` soubor s následujícím kódem:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Nahraďte `manifestUrl` HLS nebo pomlčkou URL z lokátoru streamování vašeho assetu, který najdete na stránce Lokátor streamování v Azure Portal.
    ![Adresy URL lokátoru streamování](media/player-shaka-player-how-to/streaming-urls.png)

4. Nahraďte `protocolType` následujícími možnostmi:

    - "application/x-mpegURL" pro protokoly HLS
    - "Application/pomlčka + XML" pro POMLČKové protokoly

### <a name="set-up-captions"></a>Nastavit titulky

Spusťte `addRemoteTextTrack` metodu a nahraďte:

- `subtitleKind` pomocí obou `"captions"` , `"subtitles"` , `"descriptions"` nebo `"metadata"`  
- `caption` s cestou k souboru. vtt (soubor VTT musí být ve stejném hostiteli, aby nedošlo k chybě CORS).
- `subtitleLang` s kódem BCP 47 pro jazyk, například `"eng"` pro angličtinu nebo `"es"` španělštinu
- `subtitleLabel` s požadovaným zobrazovaným názvem titulku

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Nastavení ověřování tokenů

Token musí být nastaven v poli Authorization v hlavičce požadavku. Aby nedocházelo k potížím s CORS, musí být tento token nastavený jenom v těchto žádostech s `'keydeliver'` adresou URL. Následující řádky kódu by měly provádět práci:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Potom musí být výše uvedená funkce připojena k `videojs.Hls.xhr.beforeRequest` události.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Nastavení šifrování AES-128

Video.js podporuje šifrování AES-128 bez jakékoli další konfigurace. 

> [!NOTE] 
> V současné době dochází k [potížím](https://github.com/videojs/video.js/issues/6717) s šifrováním a HLSm a CMAFm obsahem, který neumožňuje jejich případné přehrání.

### <a name="set-up-drm-protection"></a>Nastavení ochrany DRM

Aby bylo možné podporovat ochranu DRM, musíte přidat oficiální rozšíření [videojs-contrib-EME](https://github.com/videojs/videojs-contrib-eme) . Verze CDN funguje i dál.

1. V `index.js` podrobném souboru je třeba inicializovat rozšíření EME přidáním `videoJS.eme();` *před* přidáním zdroje videa:

   ```javascript
    videoJS.eme();
   ```

2. Nyní můžete definovat adresy URL služeb DRM a adresy URL odpovídajících licencí následujícím způsobem:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Získává se adresa URL licence.

Pokud chcete získat adresu URL licence, můžete:

- Projděte si konfiguraci poskytovatele DRM.
- nebo pokud používáte ukázku, Projděte si `output.json` dokument vygenerovaný při spuštění skriptu *setup-vod.ps1* PowerShellu pro VODs, nebo *start-live.ps1* skriptu pro živé streamy. V tomto souboru najdete také děti.

#### <a name="using-tokenized-drm"></a>Použití tokenu DRM

Aby bylo možné podporovat tokeny DRM, je nutné přidat následující řádek do `src` Vlastnosti přehrávače:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Další kroky

- [Použití Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Rychlý Start: šifrování obsahu](drm-encrypt-content-how-to.md)
