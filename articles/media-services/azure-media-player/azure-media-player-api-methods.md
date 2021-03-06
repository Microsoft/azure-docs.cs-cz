---
title: Azure Media Player metody rozhraní API
description: Rozhraní API pro Azure Media Player umožňuje pracovat s videem prostřednictvím JavaScriptu, ať už se jedná o video v prohlížeči, a to pomocí videa HTML5, Flash, Silverlight nebo jakékoli jiné podporované technologie přehrávání.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 3b199223c7e77f31cd4bf6e99d6a170f6868848a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448691"
---
# <a name="api"></a>Rozhraní API #

Rozhraní API pro Azure Media Player umožňuje pracovat s videem prostřednictvím JavaScriptu, ať už se jedná o video v prohlížeči, a to pomocí videa HTML5, Flash, Silverlight nebo jakékoli jiné podporované technologie přehrávání.

## <a name="referencing-the-player"></a>Odkazování na přehrávač ##

Chcete-li používat funkce rozhraní API, potřebujete přístup k objektu přehrávače. Donovanovo je snadno získat. Stačí, abyste měli jistotu, že má vaše značka videa ID. Vzorový kód pro vložení má ID `vid1` . Pokud máte na jedné stránce více videí, zajistěte, aby měla každá značka videa jedinečné ID.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Pokud přehrávač ještě nebyl inicializován pomocí atributu data-Setup nebo jiné metody, bude tento přehrávač inicializován také.

## <a name="wait-until-the-player-is-ready"></a>Počkejte, až bude přehrávač připravený. ##

Čas potřebný Azure Media Player nastavení videa a rozhraní API se bude lišit v závislosti na používané technologii přehrávání. HTML5 bude často mnohem rychlejší načíst než Flash nebo Silverlight. Z tohoto důvodu by se měla použít funkce "připravená" přehrávače k aktivaci libovolného kódu, který vyžaduje rozhraní API přehrávače.

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

Teď, když máte přístup k připravenému přehrávači, můžete řídit video, získávat hodnoty nebo reagovat na události videa. Název funkce Azure Media Player API se pokusí použít [rozhraní Media API pro HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Hlavním rozdílem je, že funkce getter/setter se používají pro vlastnosti videa.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrace pro události ##
Události by měly být registrovány přímo po inicializaci přehrávače, aby bylo zajištěno, že všechny události jsou pro aplikaci vhodně hlášeny a měly by být provedeny mimo událost připraveno.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Další kroky ##

<!---Some context for the following links goes here--->
- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)