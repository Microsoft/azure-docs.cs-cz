---
title: Začínáme s Azure Media Clipperem | Microsoft Docs
description: Začínáme s Azure Media Clipperem – Nástroj pro vytváření videoklipů z prostředků AMS
services: media-services
keywords: Clip; dílčí klip; Encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685077"
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytváření klipů pomocí Azure Media Clipperu
V této části se dozvíte o základních krocích při zahájení práce s Azure Media Clipperem. Níže uvedené části obsahují konkrétní informace o tom, jak nakonfigurovat Azure Media Clipper.

- Nejdřív přidejte následující odkazy pro Azure Media Player a Azure Media Clipper do hlavního dokumentu. V adresách URL doporučujeme explicitně zadat verzi Clipperu a Azure Media Player. Nepoužívejte nejnovější verzi těchto prostředků v produkčním prostředí, protože se mohou změnit na vyžádání.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Dále přidejte následující třídy do prvku div, kde byste chtěli vytvořit instanci Clipperu.

```javascript
<div id="root" class="azure-subclipper" />
```

Pokud chcete povolit tmavý motiv, přidejte třídu s tmavou slupkou:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Dále vytvořte instanci Clipperu pomocí následujícího volání rozhraní API:

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

Parametry pro volání inicializační metody jsou:
- `selector` {REQUIRED; String}: Selektor šablon stylů CSS odpovídajícího elementu HTML, kde má být widget vykreslen.
- `restVersion` {REQUIRED; String}: cílová verze Azure Media Services REST API. Verze REST definuje formát výstupu vygenerovaného pomůckou. V současné době je podporována pouze 2,0.
- `submitSubclipCallback` {REQUIRED, příslib} funkce zpětného volání vyvolanou při kliknutí na tlačítko Odeslat na widgetu Funkce zpětného volání by měla očekávat výstup vygenerovaný pomůckou (konfigurace úlohy vykreslování nebo definice filtru). Další informace najdete v tématu Odeslání zpětného volání dílčího klipu.
- `logLevel` {volitelné, {' info ', ' Warn ', ' error '}}: úroveň protokolování, která se má zobrazit v konzole prohlížeče. Výchozí hodnota: Chyba
- `minimumMarkerGap` {OPTIONal, int}: minimální velikost dílčího klipu (v sekundách). Poznámka: hodnota by měla být větší nebo rovna 6, což je také výchozí hodnota.
- `singleBitrateMp4Profile` {OPTIONal, JSON Object} profil MP4 s jednou přenosovou rychlostí, který se použije pro konfiguraci úlohy vykreslování vygenerované pomůckou. Pokud není zadaný, použije se [výchozí profil MP4 s jednou přenosovou rychlostí](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONal, JSON Object} profil MP4 s více přenosovými rychlostmi, který se použije pro konfiguraci úlohy vykreslování vygenerované pomůckou. Pokud není zadaný, použije se [výchozí profil MP4 s více přenosovými rychlostmi](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONal, JSON Object} umožňuje přizpůsobení klávesových zkratek widgetu. Další informace najdete v tématu [přizpůsobitelné klávesové zkratky](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONal, Promise} funkce zpětného volání, která se vyvolala pro načtení (asynchronně) nové stránky assetů do podokna assety pokaždé, když se uživatel posune dolů k dolní části podokna. Další informace najdete v tématu zpětné volání zavaděče podokna assetů.
- `height` {OPTIONal, Number} celková výška widgetu (minimální výška je 600 pixelů bez podokna assety a 850 px s podoknem assety).
- `subclippingMode` (volitelné, {' All ', ' render ', ' filter '}): jsou povoleny dílčí výstřižky. Výchozí hodnota je ALL.
- `filterAssetsTypes` (volitelné, bool): filterAssetsTypes umožňuje zobrazit nebo skrýt rozevírací seznam filtry v podokně assety. Výchozí hodnota je true (pravda).
- `speedLevels` (volitelné, pole): speedLevels umožňuje nastavení různých úrovní rychlosti pro přehrávač videa, další informace najdete v [dokumentaci k Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) .
- `resetOnJobDone` (volitelné, bool): resetOnJobDone umožňuje, aby se po úspěšném odeslání úlohy v Clipperu obnovila subclippera na počáteční stav.
- `autoplayVideo` (volitelné, bool): autoplayVideo umožňuje, aby se video při načítání automaticky přečetlo. Výchozí hodnota je true (pravda).
- `language` {OPTIONal, String}: Language nastaví jazyk widgetu. Pokud není zadaný, pomůcka se pokusí lokalizovat zprávy na základě jazyka prohlížeče. Pokud v prohlížeči není zjištěn žádný jazyk, pomůcka je standardně anglická. Další informace najdete v části [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md) .
- `languages` {OPTIONal, JSON}: parametr languages nahrazuje výchozí slovník jazyků pomocí vlastního slovníku definovaného uživatelem. Další informace najdete v části [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md) .
- `extraLanguages` (volitelné, JSON): parametr extraLanguages přidá nové jazyky do výchozího slovníku. Další informace najdete v části [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md) .

## <a name="typescript-definition"></a>Definice TypeScript
Definiční soubor [TypeScript](https://www.typescriptlang.org/) pro Clipper najdete [tady](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Rozhraní API pro Azure Media Clipper
Tato část popisuje plochu rozhraní API poskytovanou Clipperem.

- `ready(handler)`: nabízí způsob, jak spustit JavaScript, jakmile bude Clipper úplně načtený a připravený k použití.
- `load(assets)`: načte seznam assetů do časové osy pomůcky (neměl by se používat společně s assetsPanelLoaderCallback). Podrobnosti o tom, jak načíst assety do Clipperu, najdete v tomto [článku](media-services-azure-media-clipper-load-assets.md) .
- `setLogLevel(level)`: nastaví úroveň protokolování, která se má zobrazit v konzole prohlížeče. Možné hodnoty jsou: `info`, `warn`, `error`.
- `setHeight(height)`: nastaví celkovou výšku widgetu v pixelech (minimální výška je 600 pixelů bez panelu aktiva a 850 px s podoknem assety).
- `version`: Získá verzi widgetu.

## <a name="next-steps"></a>Další kroky
Podívejte se na další kroky pro konfiguraci Azure Media Clipperu:
- [Načítají se prostředky do Azure Media Clipperu.](media-services-azure-media-clipper-load-assets.md)
- [Konfigurace vlastních klávesových zkratek](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Odesílají se ořezové úlohy z Clipperu.](media-services-azure-media-clipper-submit-job.md)
- [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md)
