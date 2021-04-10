---
title: Rychlý Start Azure Media Player
description: Seznamte se se základními kroky pro nastavení Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 0d0fa88fdf182ae2214da40ee3fe8b20ced025e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956056"
---
# <a name="azure-media-player-quickstart"></a>Rychlé zprovoznění Azure Media Playeru
Nastavení Azure Media Player je snadné. Získání základního přehrávání mediálního obsahu z vašeho účtu Azure Media Services trvá jenom několik minut. Tato část ukazuje základní kroky a nezachází do podrobností. Následující části vám poskytnou konkrétní informace o tom, jak nastavit a nakonfigurovat Azure Media Player.  Stačí přidat následující příkazy include do části `<head>` vašeho dokumentu:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Tuto verzi  byste neměli používat `latest` v produkčním prostředí, protože se tato verze může změnit na vyžádání. Nahraďte `latest` verzí Azure Media Player, například nahraďte parametrem `latest` `1.0.0` . Z [tohoto místa](azure-media-player-changelog.md)se dá dotazovat na Azure Media Player verze.

## <a name="use-the-video-element"></a>Použití prvku video

V dalším kroku jednoduše použijte `<video>` prvek, jako byste normálně, ale s dodatečným `data-setup` atributem, který obsahuje všechny možnosti. Tyto možnosti mohou zahrnovat libovolnou možnost Azure Media Services v platném objektu JSON.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Pokud nechcete použít automatické nastavení, můžete `data-setup` atribut vynechat a inicializovat prvek videa ručně.

```javascript
    var myPlayer = amp('vid1', { /* Options */
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
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Další kroky ##

- [Azure Media Player úplnou instalaci](./azure-media-player-full-setup.md)
