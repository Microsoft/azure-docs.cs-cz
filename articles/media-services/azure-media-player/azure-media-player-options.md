---
title: Možnosti Azure Media Player
description: Kód Azure Media Player pro vložení je prostě značka videa HTML5, takže pro celou řadu možností můžete použít standardní atributy značek k nastavení možností.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 67f1f2fe3f6ac1061f1edcd22532bffaf0a1e815
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366866"
---
# <a name="options"></a>Možnosti #

## <a name="setting-options"></a>Možnosti nastavení ##

Kód Azure Media Player pro vložení je prostě značka videa HTML5, takže pro celou řadu možností můžete použít standardní atributy značek k nastavení možností.

`<video controls autoplay ...>`

Alternativně můžete použít atribut data-Setup k poskytnutí možností ve formátu [JSON](http://json.org/example.html) . To je také způsob, jakým byste nastavili možnosti, které nejsou standardní pro značku videa.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Nakonec, pokud nepoužíváte atribut pro nastavení dat pro aktivaci instalace přehrávače, můžete předat objekt s možnostmi přehrávače jako druhý argument ve funkci Nastavení JavaScriptu.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Možnosti v konstruktoru jsou nastaveny pouze při první inicializaci před nastavením zdroje.  Chcete-li upravit možnosti na stejném inicializovaném Azure Media Player elementu, je nutné před změnou zdroje aktualizovat možnosti. Možnosti v JavaScriptu můžete aktualizovat pomocí `myPlayer.options({/*updated options*/});` . Všimněte si, že budou ovlivněny jenom změněné možnosti, všechny ostatní možnosti, které jste nastavili, budou trvalé.

## <a name="individual-options"></a>Jednotlivé možnosti ##

> [!NOTE]
>Atributy značky videa můžou mít jenom hodnotu true nebo false (logická hodnota). k jejímu zapnutí stačí přidat atribut (bez znaménka rovná se), nebo je vyloučit, aby se vypnul. Chcete-li například zapnout ovládací prvky: nesprávné `<video controls="true" ...>` právo `<video controls ...>` na největší problémy, které lidé spouštějí do, se pokouší nastavit tyto hodnoty na hodnotu false pomocí hodnoty false jako hodnotu (například Controls = "false"), která ve skutečnosti dělá opak, a nastaví hodnotu na hodnotu true, protože atribut je stále zahrnutý.

### <a name="controls"></a>ovládací prvky ###

Možnost Controls nastaví, zda má hráč ovládací prvky, se kterými může uživatel pracovat. Bez řízení jediného způsobu, jak spustit přehrávání videa, je atributem AutoPlay nebo prostřednictvím rozhraní API.

`<video controls ...>` nebo `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Pokud má funkce automatického přehrávání hodnotu true, začne se video přehrávat hned po načtení stránky (bez jakékoli interakce uživatele).

> [!NOTE]
> Tato možnost není podporována mobilními zařízeními, jako jsou Windows Phone, Apple iOS a Android. Mobilní zařízení zablokují funkci automatického přehrávání, která zabraňuje využívání měsíčních plánů dat pro zákazníky (často nákladné). Pro spuštění videa v tomto případě je nutná možnost dotykového nebo kliknutí uživatele.

`<video autoplay ...>`ani `{ "autoplay": true }`

### <a name="poster"></a>Rozměry ###
Atribut plakát nastaví obrázek, který se zobrazí před zahájením přehrávání videa. Často se jedná o rámec videa nebo vlastní obrazovky s nadpisem. Jakmile uživatel klikne na tlačítko Přehrát, obrázek zmizí.

`<video poster="myPoster.jpg" ...>` nebo `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>šířka ###

Atribut Width nastaví šířku zobrazení videa.

`<video width="640" ...>` nebo `{ "width": 640 }`

### <a name="height"></a>výška ###

Atribut Height nastaví výšku zobrazení videa.

`<video height="480" ...>` nebo `{ "height": 480 }`

### <a name="plugins"></a>zobecněn ###

JSON pro moduly plug-in určuje, které moduly plug-in se načítají pomocí této instance AMP, umožní nakonfigurovat všechny možnosti, které modul plug-in může mít

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Další informace o vývoji a používání modulů plug-in najdete v tématu [psaní modulů plug-](azure-media-player-writing-plugins.md) in.

### <a name="other-options"></a>Další možnosti ###

Další možnosti lze nastavit u `<video>` značky pomocí `data-setup` parametru, který přebírá JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Tato hodnota je explicitně nastavená na false. Nastavením na hodnotu false umožníte, aby byl vzhled Azure Media Player vykreslen na různých platformách.  V opačném případě bude i nadále povolen dotyk.

### <a name="fluid"></a>unce ###

Nastavením této možnosti na true video element bude trvat celou šířku nadřazeného kontejneru a výška se upraví tak, aby se vešlo na video s poměrem stran standard 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid` možnost Přepisuje explicitní `width` a `height` nastavení. Tato možnost je k dispozici pouze ve verzi Azure Media Player `2.0.0` a novější.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` možnost řídí playbackSpeed řízení a sadu nastavení rychlosti přehrávání, které jsou pro uživatele k dispozici. `playbackSpeed` vezme objekt. Aby bylo možné povolit ovládací prvek rychlost přehrávání na ovládacím panelu, `enabled` musí být vlastnost objektu nastavena na hodnotu true. Příklad povolení rychlosti přehrávání v kódu:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Další vlastnosti `playbackSpeed` nastavení jsou dány objektem [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/playbackspeedoptions) .

Příklad nastavení možností rychlosti přehrávání v JavaScriptu:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Tato možnost je k dispozici pouze ve verzi Azure Media Player 2.0.0 a novější.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

Tato `staleDataTimeLimitInSec` možnost je optimalizace, která umožňuje nakonfigurovat, kolik sekund má zastaralá data, která chcete zachovat v mediaSource vyrovnávací paměti. Tato možnost je ve výchozím nastavení zakázána.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Nastavením enabled na true (pravda) můžete zobrazit CEA titulků ve vašich živých streamech a živých archivech. Atribut Label není povinný, pokud není zahrnutý v přehrávači, aby se vrátil do výchozího popisku.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Tato možnost je k dispozici pouze ve verzi Azure Media Player verze 2.1.1 a novější.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
