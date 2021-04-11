---
title: Azure Media Player úplnou instalaci
description: Přečtěte si, jak nastavit Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: d7c103a7eba2e5da7ac040b50fcc0009a0b7237b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449864"
---
# <a name="azure-media-player-full-setup"></a>Kompletní nastavení Azure Media Playeru #

Nastavení Azure Media Player je snadné. Získání základního přehrávání mediálního obsahu přímo z vašeho účtu Azure Media Services trvá jenom chvíli. [Ukázky](https://github.com/Azure-Samples/azure-media-player-samples) jsou k dispozici také v adresáři Samples této verze.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Krok 1: zahrnutí souborů JavaScriptu a CSS do hlavičky stránky ##

Pomocí Azure Media Player máte přístup ke skriptům z hostované verze CDN. Často se doporučuje vložit JavaScript před značku konce těla `<body>` místo `<head>` , ale Azure Media Player obsahuje "HTML5 Shiv", který musí být v hlavě pro starší verze IE, aby se jako platný element mohla přicházet k označení videa.

> [!NOTE]
> Pokud již používáte HTML5 Shiv jako [modernizr](https://modernizr.com/) , můžete Azure Media Player JavaScriptu umístit kamkoli. Ujistěte se však, že vaše verze modernizr zahrnuje Shiv pro video.

### <a name="cdn-version"></a>Verze CDN ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Tuto verzi  byste neměli používat `latest` v produkčním prostředí, protože se tato verze může změnit na vyžádání. Nahraďte `latest` verzí Azure Media Player. Například Nahraďte parametr `latest` `2.1.1` . Z [tohoto místa](https://amp.azure.net/libs/amp/latest/docs/changelog.html)se dá dotazovat na Azure Media Player verze.

> [!NOTE]
> Od `1.2.0` verze už není potřeba, aby zahrnovala umístění pro záložní pracovníky (z relativní cesty azuremediaplayer.min.js souboru se automaticky vybralo umístění). Umístění záložních techniků můžete upravit přidáním následujícího skriptu do `<head>` výše uvedených skriptů.

> [!NOTE]
> Vzhledem k povaze modulů plug-in Flash a Silverlight by se soubory SWF a XAP měly hostovat v doméně bez citlivých informací nebo dat – to se automaticky stará o Azure CDN hostované verze.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Krok 2: Přidání značky videa HTML5 na stránku ##

Pomocí Azure Media Player můžete použít značku videa HTML5 pro vložení videa. Azure Media Player pak přečte značku a zpřístupní ji ve všech prohlížečích, nikoli jenom těch, které podporují video HTML5. Kromě základních značek Azure Media Player potřebuje několik dalších částí.

1. `<data-setup>`Atribut na `<video>` Azure Media Player informuje o tom, že se video automaticky nastaví, když je stránka připravená, a z atributu si přečte všechny (ve formátu JSON).
1. `id`Atribut: by měl být použit a jedinečný pro každé video na stejné stránce.
1. `class`Atribut obsahuje dvě třídy:
    - `azuremediaplayer` použije styly, které jsou vyžadovány pro Azure Media Player funkce uživatelského rozhraní.
    - `amp-default-skin` použije výchozí vzhled pro ovládací prvky HTML5.
1. `<source>`Obsahuje dva povinné atributy.
    - `src` atribut může obsahovat soubor **. ISM/manifest* z Azure Media Services je přidána Azure Media Player automaticky přidá adresy URL pro pomlčky, vyhlazení a HLS do přehrávače.
    - `type` atribut je požadovaný typ MIME datového proudu. Typ MIME přidružený k *". ISM/manifest"* je *"application/vnd. MS-sstr + XML"*
1. *Volitelný* `<data-setup>` atribut na stránce `<source>` oznamuje Azure Media Player, jestli existují jedinečné zásady doručování pro datový proud z Azure Media Services, včetně, ale ne omezení na typ šifrování (AES nebo PlayReady, Widevine nebo FairPlay) a token.

Zahrňte nebo vylučte atributy, nastavení, zdroje a stopy přesně tak, jak byste chtěli pro video HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Ve výchozím nastavení se velké tlačítko pro přehrávání nachází v levém horním rohu, takže se nezabývá zajímavou částí plakátu. Pokud budete chtít vycentrovat velké tlačítko pro přehrávání, můžete `amp-big-play-centered` `class` do svého prvku přidat další `<video>` .

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternativní nastavení pro dynamicky načtený kód HTML ###

Pokud vaše webová stránka nebo aplikace načte značku videa dynamicky (AJAX, appendChild atd.), takže když se stránka načte, budete chtít ručně nastavit přehrávač místo toho, aby se musel spoléhat na atribut data-Setup. K tomu je třeba nejprve odebrat atribut pro nastavení dat ze značky, aby při inicializaci přehrávače nedošlo k nejasnostem. Dále spusťte následující JavaScript po načtení Azure Media Player JavaScript a poté, co byla značka videa načtena do modelu DOM.

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
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

První argument `amp` funkce je ID značky videa. Nahraďte ji vlastními.

Druhý argument je objekt Options. Umožňuje nastavit další možnosti, jako je například s použitím atributu data-Setup.

Třetí argument je zpětné volání "připraveno". Jakmile se Azure Media Player inicializuje, zavolá tuto funkci. V připraveném zpětném volání odkazuje objekt this na instanci přehrávače.

Namísto použití ID elementu můžete také předat odkaz na samotný element.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
