---
title: Začínáme s Azure Media Clipperem | Dokumentace Microsoftu
description: Začínáme s Azure Media Clipperem, nástroj pro vytváření videoklipy ze prostředky AMS
services: media-services
keywords: Galerie, dílčí klip, kódování, médií
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 27a330fa4d4f242a58d15ab3f08b70cef8b66d11
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810733"
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytvářet s Azure Media Clipperem
Tato část popisuje, jak začít s Azure Media Clipperem základní kroky. Oddíly, které následují poskytují podrobnosti o tom, jak konfigurovat Azure Media Clipperem.

- Nejprve přidejte následující odkazy pro Azure Media Player a Azure Media Clipperem dokumentu, a head. Doporučujeme, abyste explicitní určení verze Clipperem a Azure Media Player v adresách URL. Nejnovější verze těchto prostředků nepoužívejte v produkčním prostředí, protože se může změnit na vyžádání.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Dále přidejte následující třídy do elementu div kde chcete vytvořit instanci Clipperem.

```javascript
<div id="root" class="azure-subclipper" />
```

Volitelně přidáte umožňující tmavý motiv tmavý vzhled třídy:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- V dalším kroku vytvoření instance Clipperem pomocí následujícího volání rozhraní API:

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
- `selector` {POŽADOVÁNO, řetězec}: Selektor šablon stylů CSS z odpovídající prvek HTML, ve kterém má být vykreslen widgetu.
- `restVersion` {POŽADOVÁNO, řetězec}: Verze rozhraní API REST pro Azure Media Services k cíli. Verze REST definuje formát výstupu generovaného ve widgetu. V současné době se podporuje jenom 2.0.
- `submitSubclipCallback` {POTŘEBY promise} Funkce zpětného volání, vyvolá se, pokud dojde ke kliknutí na tlačítko "Odeslat" widgetu. Funkce zpětného volání byste očekávat, že výstup generovaný ve widgetu (konfigurace úlohy vykreslování nebo definici filtru). Další informace najdete v tématu odeslat dílčí klip zpětného volání.
- `logLevel` {VOLITELNÝ parametr, {"informace", "varování", "Chyba"}}: Úroveň protokolování, který se má zobrazit v konzole prohlížeče. Výchozí hodnota: Chyba
- `minimumMarkerGap` {VOLITELNÉ, int}: Minimální velikost dílčího klipu (v sekundách). Poznámka: hodnota by měla být větší nebo rovna hodnotě 6, která je také výchozí.
- `singleBitrateMp4Profile` {NEPOVINNÝ, objekt JSON} S jednou přenosovou rychlostí mp4 profil, který chcete použít pro konfigurace úlohy vykreslování generovaných widgetu. Pokud se nezadá, použije [výchozí profil MP4 s jednou přenosovou rychlostí](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {NEPOVINNÝ, objekt JSON} Profil mp4 s více s přenosovou rychlostí má použít pro vykreslení úlohy konfigurace vygeneruje ve widgetu. Pokud se nezadá, použije [výchozí profil MP4 s více přenosovými rychlostmi](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {NEPOVINNÝ, objekt json} Umožňuje přizpůsobení klávesových zkratek widgetu. Další informace najdete v tématu [přizpůsobitelné klávesové zkratky](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {VOLITELNÉ, příslib} Funkce zpětného volání k načtení (asynchronně) novou stránku prostředků do panelu aktiva pokaždé, když uživatel přesune do dolní části podokna. Další informace najdete v tématu Asset podokně zavaděč zpětného volání.
- `height` {VOLITELNÉ, number} Celkový počet výšku widgetu (minimální výška bude 600 px bez podokno prostředky a 850 px s podoknem prostředky).
- `subclippingMode` (Volitelné tam,: {"vše", "vykreslení", "filtrování"}): Režimy oříznutím, povolené. Výchozí hodnota je vše.
- `filterAssetsTypes` (Volitelné, logická hodnota): filterAssetsTypes umožňuje zobrazit nebo skrýt filtry rozevíracího seznamu v podokně prostředků. Výchozí hodnota je true.
- `speedLevels` (Volitelné, pole): speedLevels umožňuje nastavení úrovně jinou rychlost pro přehrávač videa, naleznete v tématu [dokumentace ke službě Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) pro další informace.
- `resetOnJobDone` (Volitelné, logická hodnota): umožňuje resetOnJobDone Clipperem obnovit subclipperu počáteční stav, když se úloha se úspěšně odeslala.
- `autoplayVideo` (Volitelné, logická hodnota): autoplayVideo umožňuje Clipperem pro automatické přehrání videa na zatížení. Výchozí hodnota je true.
- `language` {VOLITELNÝ, řetězec}: jazyk nastaví jazyk widgetu. Pokud není zadán, pokusí se widgetu lokalizovat zprávy podle jazyka prohlížeče. Pokud žádný jazyk se detekuje v prohlížeči, ve widgetu výchozí hodnota je angličtina. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) oddílu.
- `languages` {VOLITELNÉ, JSON}: Parametr jazyky nahradí výchozí slovník jazyků vlastní slovník definovaný uživatelem. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) oddílu.
- `extraLanguages` (Volitelné, JSON): Parametr extraLanguages přidá do slovníku výchozí. nové jazyky. Další informace najdete v tématu [konfigurace lokalizace](media-services-azure-media-clipper-localization.md) oddílu.

## <a name="typescript-definition"></a>Definice TypeScript
A [TypeScript](https://www.typescriptlang.org/) najdete soubor definice pro Clipperem [tady](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
Tato část popisuje plochy rozhraní API poskytovaných Clipperem.

- `ready(handler)`: poskytuje způsob, jak JavaScript spustit hned Clipperem plně načtený a připravená k použití.
- `load(assets)`: načte seznam prostředků do widgetu osy (nesmí být použito spolu s assetsPanelLoaderCallback). Najdete v tomto [článku](media-services-azure-media-clipper-load-assets.md) podrobnosti o tom, jak načíst aktiva do Clipperem.
- `setLogLevel(level)`: Nastaví úroveň protokolování, který se má zobrazit v konzole prohlížeče. Možné hodnoty jsou: `info`, `warn`, `error`.
- `setHeight(height)`: Nastaví widgetu celková výška v pixelech (minimální výška bude 600 px bez prostředků podokna a 850 px s podoknem prostředky).
- `version`: získá verzi widgetu.

## <a name="next-steps"></a>Další postup
Informace o tom další kroky pro konfiguraci Azure Media Clipperem:
- [Načítání prostředků do Azure Media Clipperu](media-services-azure-media-clipper-load-assets.md)
- [Konfigurace vlastních klávesových zkratek](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Odesílání úloh oříznutí z Clipperem](media-services-azure-media-clipper-submit-job.md)
- [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md)