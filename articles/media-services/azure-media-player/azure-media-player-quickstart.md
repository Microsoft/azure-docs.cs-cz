---
title: Rychlý start přehrávače médií Azure
description: Přečtěte si základní kroky k nastavení Azure Media Playeru.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726463"
---
# <a name="azure-media-player-quickstart"></a>Rychlý start programu Azure Media Player
Azure Media Player se snadno nastavuje. Získání základního přehrávání mediálního obsahu z účtu Azure Media Services trvá jen několik minut. Tato část ukazuje základní kroky a nezachází do podrobností. Následující části poskytují podrobnosti o tom, jak nastavit a nakonfigurovat Azure Media Player.  Stačí přidat následující příkazy include do části `<head>` vašeho dokumentu:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **Neměli** `latest` byste používat verzi v produkčním prostředí, protože to může změnit na vyžádání. Nahraďte `latest` verzí programu Azure Media Player. například `latest` nahradit `1.0.0`. Verze programu Azure Media Player lze dotazovat [zde](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Použití prvku videa

Dále jednoduše použijte `<video>` prvek jako obvykle, ale `data-setup` s dalším atributem obsahujícím všechny možnosti. Tyto možnosti mohou zahrnovat libovolnou možnost Azure Media Services v platném objektu JSON.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Pokud nechcete používat automatické nastavení, můžete `data-setup` atribut vynechat a inicializovat prvek videa ručně.

```html
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
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)