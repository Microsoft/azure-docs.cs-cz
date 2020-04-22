---
title: Metody rozhraní API přehrávače médií Azure
description: Rozhraní Azure Media Player API umožňuje interakci s videem prostřednictvím JavaScriptu bez ohledu na to, zda prohlížeč přehrává video prostřednictvím videa HTML5, flash, programu Silverlight nebo jiných podporovaných technologií přehrávání.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727265"
---
# <a name="api"></a>rozhraní API #

Rozhraní Azure Media Player API umožňuje interakci s videem prostřednictvím JavaScriptu bez ohledu na to, zda prohlížeč přehrává video prostřednictvím videa HTML5, flash, programu Silverlight nebo jiných podporovaných technologií přehrávání.

## <a name="referencing-the-player"></a>Odkazování na hráče ##

Chcete-li používat funkce rozhraní API, potřebujete přístup k objektu přehrávače. Naštěstí je snadné se dostat. Stačí se ujistit, že vaše značka videa má ID. Příklad kódu pro vložení má `vid1`ID aplikace . Pokud máte na jedné stránce více videí, ujistěte se, že každá značka videa má jedinečné ID.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Pokud přehrávač ještě nebyl inicializován pomocí atributu nastavení dat nebo jiné metody, bude to také inicializovat přehrávač.

## <a name="wait-until-the-player-is-ready"></a>Počkejte, až bude přehrávač připraven ##

Čas potřebný k nastavení videa a rozhraní API v programu Azure Media Player se bude lišit v závislosti na použité technologii přehrávání. HTML5 bude často mnohem rychlejší než flash nebo silverlight. Z tohoto důvodu by měla být použita funkce "ready" hráče k aktivaci kódu, který vyžaduje rozhraní API hráče.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

NEBO

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Metody rozhraní API ##

Nyní, když máte přístup k připravenému přehrávači, můžete ovládat video, získat hodnoty nebo reagovat na video události. Názvy funkcí rozhraní API přehrávače médií Azure Media Player se pokoušejí sledovat [rozhraní API médií HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Hlavní rozdíl je, že getter/setter funkce se používají pro vlastnosti videa.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrace na události ##
Události by měly být zaregistrovány přímo po první inicializaci přehrávače, aby bylo zajištěno, že všechny události jsou vhodně hlášeny do aplikace a měly by být provedeny mimo připravenou událost.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Další kroky ##

<!---Some context for the following links goes here--->
- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)