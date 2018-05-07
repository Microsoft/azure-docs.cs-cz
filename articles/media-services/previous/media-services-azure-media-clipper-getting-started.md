---
title: "Začínáme s Azure Media výřez | Microsoft Docs"
description: "Začínáme s Azure výřez média, nástroj pro tvorbu videosoubory z AMS prostředků"
services: media-services
keywords: "klip; subclip; kódování; média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytvoření klipů pomocí Azure Media výřez
Tato část uvádí základní kroky Začínáme se službou Azure Media výřez. Části obsahují podrobnosti o tom, jak konfigurovat Azure Media výřez.

- Nejprve přidejte následující odkazy pro přehrávač médií Azure a Azure Media výřez head vašeho dokumentu. Doporučujeme, abyste explicitně určit verzi výřez a Azure Media Player v adresách URL. Nepoužívejte nejnovější verzi těchto prostředků v produkčním prostředí, protože se jedná o mohou změnit na vyžádání.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Dál přidejte následující třídy element div, kde chcete vytvořit instanci výřez.

```javascript
<div id="root" class="azure-subclipper" />
```

Volitelně můžete povolit tmavým motivem, přidejte třídu světlý vzhledu:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- V dalším kroku doložit výřez na následující volání rozhraní API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Parametry pro volání metody inicializace jsou:
- `selector`{POTŘEBY řetězec}: výběr CSS odpovídající elementu HTML, kde má být vykreslen widgetu.
- `restVersion`{POTŘEBY řetězec}: verze rozhraní API REST služby Azure Media k cíli. Verze REST definuje formát výstupu generované widgetu. V současné době se podporuje jenom 2.0.
- `submitSubclipCallback`{POVINNÉ, promise} Funkce zpětného volání, která je volána, když po kliknutí na tlačítko "Odeslat" widgetu. Funkce zpětného volání by měl očekávat výstup generovaný ve widgetu (konfigurace úlohu vykreslování nebo definici filtru). Další informace najdete v tématu Odeslání subclip zpětného volání.
- `logLevel`{VOLITELNÉ, {'informace o', "Upozornit", "Chyba"}}: úroveň protokolování, který se má zobrazit v konzole prohlížeče. Výchozí hodnota: Chyba
- `minimumMarkerGap`{VOLITELNÉ, int}: minimální velikost subclip (v sekundách). Poznámka: hodnota by měla být větší nebo rovna hodnotě 6, což je také výchozí.
- `singleBitrateMp4Profile`{Objekt volitelné, JSON} Profil mp4 jednou přenosovou rychlostí pro vykreslení úlohy konfigurace generované widgetu. Pokud není zadaná, použije [výchozí profil MP4 jednou přenosovou rychlostí](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile`{Objekt volitelné, JSON} Profil mp4 přenosovou rychlostí více pro vykreslení úlohy konfigurace generované widgetu. Pokud není zadaná, použije [výchozí profil MP4 více přenosovými rychlostmi](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap`{Objekt volitelné, json} Umožňuje přizpůsobení klávesových zkratek widgetu. Další informace najdete v tématu [přizpůsobitelné klávesové zkratky](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback`{VOLITELNÉ, promise} Funkce zpětného volání, která je volána načíst (asynchronně) novou stránku prostředků do podokna prostředky pokaždé, když uživatel posune do dolní části podokna. Další informace najdete v tématu Asset podokně zavaděč zpětného volání.
- `height`{VOLITELNÉ, number} Celková výška widgetu (minimální výška je 600 px bez podokně prostředky a 850 px s podokně prostředky).
- `subclippingMode`(Volitelné tam, {"vše", 'vykreslení' barvy, 'filtr'}): režimy subclipping povoleny. Výchozí hodnota je všechny.
- `filterAssetsTypes`(Volitelné, logická hodnota): filterAssetsTypes umožňuje zobrazit nebo skrýt rozevíracího seznamu filtrů z podokna prostředky. Výchozí hodnota je true.
- `speedLevels`(Volitelná pole): speedLevels umožňuje nastavení úrovně jinou rychlost pro přehrávání videa, najdete v části [dokumentace Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) Další informace.
- `resetOnJobDone`(Volitelné, logická hodnota): resetOnJobDone umožňuje výřez resetovat subclipper počáteční stav, když je úloha odeslána úspěšně.
- `autoplayVideo`(Volitelné, logická hodnota): autoplayVideo umožňuje výřez automatické přehrávání videa na zatížení. Výchozí hodnota je true.
- `language`{VOLITELNÝ, řetězec}: jazyk nastaví jazyk widgetu. Pokud není zadaný, pokusí se widgetu lokalizaci zprávy podle jazyka prohlížeče. Pokud žádný jazyk v prohlížeči, widgetu výchozí angličtině. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) části.
- `languages`{VOLITELNÉ, JSON}: Parametr jazyky nahradí výchozí slovník jazyky vlastní slovník definovaných uživatelem. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) části.
- `extraLanguages`(Volitelné, JSON): Parametr extraLanaguages přidá do slovníku výchozí nové jazyky. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) části.

## <a name="typescript-definition"></a>Definice typeScript
A [TypeScript](https://www.typescriptlang.org/) najdete soubor definice pro výřez [zde](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media výřez rozhraní API
Tato část popisuje plochy rozhraní API poskytované výřez.

- `ready(handler)`: poskytuje způsob, jak spustit JavaScript, jakmile výřez je plně načíst a připravené k použití.
- `load(assets)`: načte seznam prostředků do pomůcka časovou osu (nesmí použít společně s assetsPanelLoaderCallback). Toto [článku](media-services-azure-media-clipper-load-assets.md) podrobnosti o tom, jak načíst prostředky do výřez.
- `setLogLevel(level)`: Určuje úroveň protokolování, který se má zobrazit v konzole prohlížeče. Možné hodnoty jsou: `info`, `warn`, `error`.
- `setHeight(height)`: Nastaví celkový výšku widgetu v pixelech (minimální výška je 600 px bez podokně prostředky a 850 px s podokně prostředky).
- `version`: získá verzi pomůcky.

## <a name="next-steps"></a>Další kroky
Najdete v části Další kroky pro konfiguraci Azure Media výřez:
- [Načítání prostředků do Azure Media výřez](media-services-azure-media-clipper-load-assets.md)
- [Konfigurace vlastní klávesové zkratky](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Odeslání úlohy výstřižek z výřez](media-services-azure-media-clipper-submit-job.md)
- [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md)