---
title: Úplné nastavení přehrávače médií Azure
description: Přečtěte si, jak nastavit Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727239"
---
# <a name="azure-media-player-full-setup"></a>Úplné nastavení programu Azure Media Player #

Azure Media Player se snadno nastavuje. Získání základního přehrávání mediálního obsahu přímo z vašeho účtu Azure Media Services trvá jen několik okamžiků. [Ukázky](https://github.com/Azure-Samples/azure-media-player-samples) jsou také k dispozici v adresáři ukázky vydání.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Krok 1: Zahrnout JavaScript a CSS soubory v hlavě vaší stránky ##

Pomocí programu Azure Media Player máte přístup ke skriptům z hostované verze CDN. Často se doporučuje, aby javascript před značku `<body>` koncového `<head>`těla místo , ale Azure Media Player obsahuje 'HTML5 Shiv', který musí být v hlavě pro starší verze IE respektovat video tag jako platný prvek.

> [!NOTE]
> Pokud už používáte html5 shiv jako [Modernizr,](http://modernizr.com/) můžete zahrnout JavaScript Přehrávače médií Azure Media kdekoli. Nicméně ujistěte se, že vaše verze Modernizr obsahuje nůž pro video.

### <a name="cdn-version"></a>Verze CDN ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> **Neměli** `latest` byste používat verzi v produkčním prostředí, protože to může změnit na vyžádání. Nahraďte `latest` ji verzí programu Azure Media Player. Například nahraďte . `latest` `2.1.1` Verze programu Azure Media Player lze dotazovat [zde](azure-media-player-changelog.md).

> [!NOTE]
> Od `1.2.0` vydání již není nutné zahrnout umístění záložních techniků (automaticky převezme umístění z relativní cesty souboru azuremediaplayer.min.js). Umístění záložních techniků můžete upravit přidáním následujícího `<head>` skriptu do výše uvedených skriptů.

> [!NOTE]
> Vzhledem k povaze pluginů Flash a Silverlight by soubory swf a xap měly být hostovány na doméně bez citlivých informací nebo dat - o to se automaticky postaráte s hostovnou verzí Azure CDN.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Krok 2: Přidání značky videa HTML5 na stránku ##

Pomocí Programu Azure Media Player můžete k vložení videa použít značku videa HTML5. Azure Media Player pak značku přečte a bude fungovat ve všech prohlížečích, ne jen ve všech prohlížečích, které podporují video HTML5. Kromě základních značek potřebuje Azure Media Player pár kousků navíc.

1. Atribut `<data-setup>` na `<video>` Azure Media Player automaticky nastavit video, když je stránka připravena a přečtěte si všechny (ve formátu JSON) z atributu.
1. Atribut: `id` By měl být použit a jedinečný pro každé video na stejné stránce.
1. Atribut `class` obsahuje dvě třídy:
    - `azuremediaplayer`Aplikuje styly, které jsou vyžadovány pro funkce práva nastavení Azure Media Player
    - `amp-default-skin`Použije výchozí vzhled na ovládací prvky HTML5.
1. Obsahuje `<source>` dva požadované atributy
    - `src`atribut může obsahovat **.ism/manifest* soubor z Azure Media Services je přidán, Azure Media Player automaticky přidá adresy URL pro DASH, SMOOTH a HLS do přehrávače
    - `type`atribut je požadovaný typ MIME datového proudu. Typ MIME přidružený k *".ism/manifest"* je *"application/vnd.ms-sstr+xml"*
1. *Volitelný* `<data-setup>` atribut na `<source>` Azure Media Player říká, pokud existují nějaké jedinečné zásady doručování pro datový proud z Azure Media Services, včetně, ale bez omezení na typ šifrování (AES nebo PlayReady, Widevine nebo FairPlay) a token.

Zahrnout/vyloučit atributy, nastavení, zdroje a stopy přesně tak, jak byste to udělali u videa HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Ve výchozím nastavení je velké tlačítko přehrávání umístěno v levém horním rohu, takže nezakrývá zajímavé části plakátu. Pokud dáváte přednost vystředění velkého tlačítka `amp-big-play-centered` `class` přehrávání, `<video>` můžete k prvku přidat další.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternativní nastavení pro dynamicky načtený kód HTML ###

Pokud vaše webová stránka nebo aplikace načte značku videa dynamicky (ajax, appendChild atd.), takže nemusí existovat při načtení stránky, budete chtít ručně nastavit přehrávač namísto spoléhání se na atribut nastavení dat. Chcete-li to provést, nejprve odeberte atribut nastavení dat ze značky, aby nedošlo k záměně při inicializování přehrávače. Dále spusťte následující JavaScript po načtení JavaScriptu programu Azure Media Player a po načtení značky videa do režimu do m.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Prvním argumentem `amp` ve funkci je ID značky videa. Nahraďte ho svým vlastním.

Druhý argument je options objekt. To vám umožní nastavit další možnosti, jako můžete s atributem nastavení dat.

Třetí argument je 'ready' zpětné volání. Po inicializování programu Azure Media Player bude tuto funkci volat. V ready callback , 'this' objekt odkazuje na instanci přehrávače.

Namísto použití ID prvku můžete také předat odkaz na samotný prvek.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)