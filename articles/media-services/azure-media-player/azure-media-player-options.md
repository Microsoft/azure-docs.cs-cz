---
title: Možnosti přehrávače médií Azure
description: Kód pro vložení přehrávače médií Azure Media Player je jednoduše značka videa HTML5, takže pro mnoho možností můžete použít standardní atributy značky k nastavení možností.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727161"
---
# <a name="options"></a>Možnosti #

## <a name="setting-options"></a>Možnosti nastavení ##

Kód pro vložení přehrávače médií Azure Media Player je jednoduše značka videa HTML5, takže pro mnoho možností můžete použít standardní atributy značky k nastavení možností.

`<video controls autoplay ...>`

Případně můžete použít atribut nastavení dat k poskytnutí možností ve formátu [JSON.](http://json.org/example.html) To je také, jak byste nastavit možnosti, které nejsou standardní značky videa.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

A konečně, pokud nepoužíváte atribut nastavení dat k aktivaci nastavení přehrávače, můžete předat objekt s možnostmi přehrávače jako druhý argument ve funkci nastavení JavaScriptu.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Možnosti v konstruktoru jsou nastaveny pouze při první inicializaci před nastavením zdroje.  Pokud chcete upravit možnosti na stejný inicializovaný prvek Azure Media Player, je nutné aktualizovat možnosti před změnou zdroje. Možnosti v jazyce JavaScript `myPlayer.options({/*updated options*/});`můžete aktualizovat pomocí aplikace . Všimněte si, že budou ovlivněny pouze změněné možnosti, všechny ostatní dříve nastavené možnosti zůstanou zachovány.

## <a name="individual-options"></a>Jednotlivé možnosti ##

> [!NOTE]
>Atributy značek videa mohou být pouze pravdivé nebo nepravdivé (logické), jednoduše zahrnete atribut (bez znaménko rovná se), abyste jej zapnuli nebo vyloučili, abyste jej vypnuli. Chcete-li například zapnout `<video controls="true" ...>` `<video controls ...>` ovládací prvky: WRONG RIGHT Největší problém, do kterého lidé narazíte, se pokoušíte nastavit tyto hodnoty na false pomocí false jako hodnoty (např. controls="false"), která ve skutečnosti dělá opak a nastaví hodnotu na hodnotu true, protože atribut je stále zahrnut.

### <a name="controls"></a>ovládací prvky ###

Možnost ovládacích prvků určuje, zda má přehrávač ovládací prvky, se kterými může uživatel pracovat. Bez ovládacích prvků je jediným způsobem, jak spustit přehrávání videa, atribut automatického přehrávání nebo prostřednictvím rozhraní API.

`<video controls ...>` nebo `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Pokud je automatické přehrávání pravdivé, video se začne přehrávat, jakmile se stránka načte (bez jakékoli interakce uživatele).

> [!NOTE]
> Tato možnost není podporována mobilními zařízeními, jako je Windows Phone, Apple iOS a Android. Mobilní zařízení blokují funkci automatického přehrávání, aby se zabránilo nadměrnému používání měsíčních datových tarifů spotřebitele (často drahých). Pro spuštění videa je v tomto případě vyžadováno dotykové ovládání uživatelem.

`<video autoplay ...>`Nebo`{ "autoplay": true }`

### <a name="poster"></a>Plakát ###
Atribut titulního obrázku nastaví obraz, který se zobrazí před zahájením přehrávání videa. Toto je často snímek videa nebo vlastní titulní obrazovka. Jakmile uživatel klikne na tlačítko přehrát obraz zmizí.

`<video poster="myPoster.jpg" ...>` nebo `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>šířka ###

Atribut šířky nastaví šířku zobrazení videa.

`<video width="640" ...>` nebo `{ "width": 640 }`

### <a name="height"></a>height ###

Atribut výšky nastavuje výšku zobrazení videa.

`<video height="480" ...>` nebo `{ "height": 480 }`

### <a name="plugins"></a>Pluginy ###

Pluginy JSON určuje, které pluginy se načítají s tou instancí AMP, vám umožní nakonfigurovat všechny možnosti, které plugin může mít.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Další informace o vývoji a používání pluginů naleznete v [tématu psaní pluginů](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>další možnosti ###

Další možnosti lze `<video>` nastavit na `data-setup` značku pomocí parametru, který trvá JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativníOvládací prvkyForTouch ####

To je explicitně nastavena na false. Nastavením false, umožní Azure Media Player vzhled, který má být vykreslen stejný napříč platformami.  Kromě toho, na rozdíl od názvu, bude touch stále povolen.

### <a name="fluid"></a>Tekutiny ###

Nastavením této možnosti na skutečný prvek videa bude mít plnou šířku nadřazeného kontejneru a výška bude upravena tak, aby se vešla do videa se standardním poměrem stran 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`volba přepíše `width` `height` explicitní a nastavení. Tato možnost je dostupná jenom ve verzi `2.0.0` přehrávače Azure Media Player a novější.

### <a name="playbackspeed"></a>rychlost přehrávání ###

`playbackSpeed`volba řídí ovládání rychlosti přehrávání a nastavení rychlosti přehrávání, které je k dispozici pro uživatele. `playbackSpeed`přebere objekt. Aby bylo možné povolit kontrolu rychlosti `enabled` přehrávání na ovládacím panelu, musí být vlastnost objektu nastavena na hodnotu true. Příklad povolení rychlosti přehrávání ve značkách:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Další vlastnosti `playbackSpeed` nastavení jsou dány objektem [PlaybackSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

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

Tato možnost je dostupná jenom v přehrávači Azure Media Player verze 2.0.0 a novějším.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

Tato `staleDataTimeLimitInSec` možnost je optimalizace, která umožňuje nakonfigurovat, kolik sekund v hodnotě zastaralých dat, které chcete zachovat ve vyrovnávacích vyrovnávacích paměti mediaSource. Tato možnost je ve výchozím nastavení zakázána.

### <a name="cea708captionssettings"></a>cea708TitulkyNastavení ###

Nastavení povoleno na hodnotu true umožňuje zobrazit živé CEA titulky ve vašich živých proudů a živých archivů. Atribut label není vyžadován, pokud není zahrnut, hráč se vrátí na výchozí štítek.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Tato možnost je dostupná jenom v přehrávači Azure Media Player verze 2.1.1 a novějším.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)