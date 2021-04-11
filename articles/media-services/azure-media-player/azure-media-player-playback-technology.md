---
title: Technologie přehrávání Azure Media Player
description: Přečtěte si další informace o technologii přehrávání, která se používá k přehrání videa nebo zvuku.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 0fadb679260582a788eb6408ff3761f00c2ceb1a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448819"
---
# <a name="playback-technology-tech"></a>Technologie přehrávání ("tech") #

Technologie přehrávání odkazuje na konkrétní technologii prohlížeče nebo modulu plug-in, která se používá k přehrání videa nebo zvuku.

- **azureHtml5JS**: využívá standardy MSE a EME ve spojení s elementem video pro přehrávání přerušovaného obsahu založeného na modulu plug-in s podporou algoritmu AES-128 šifrovaný obsah nebo společný zašifrovaný šifrovaný obsah (přes PlayReady a Widevine, když ho prohlížeč podporuje) od Azure Media Services
- **Flash**: využívá technologii přehrávače Flash k přehrání hladkého obsahu s podporou šifrování obálek AES-128 od Azure Media Services – vyžaduje verzi Flash 11,4 nebo vyšší.
- **html5FairPlayHLS**: využívá aplikaci Safari specifickou v prohlížeči v technologii přehrávání prostřednictvím HLS s prvkem video. Tento techer je nutný k přehrání FairPlay chráněného obsahu z Azure Media Services a přidal se do techOrder od 10/19/16.
- **Silverlight**: využívá technologii Silverlight k přehrání hladkého obsahu s podporou obsahu chráněného technologií PlayReady z Azure Media Services.
- **HTML5**: využívá technologii přehrávání založenou na prohlížeči s prvkem video.  Když jste na zařízení se systémem iOS nebo Android, tento techer umožňuje přehrávání HLS datových proudů s určitou základní podporou pro šifrování obálek AES-128 nebo obsah DRM (přes FairPlay, když ho prohlížeč podporuje).

## <a name="tech-order"></a>Tech Order ##

Aby se zajistilo, že váš Asset bude moct využít širokou škálu zařízení, doporučuje se následující techá objednávka a je výchozí, pokud: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` a dá se nastavit přímo na `<video>` programově nebo v možnostech:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

nebo

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Tabulka kompatibility ##

Vzhledem k doporučenému technickému pořadí s obsahem streamování z Azure Media Services je očekávána následující matice přehrávání kompatibility.

| Prohlížeč        | Operační systém                                                       | Očekávalo se Tech (Clear)  | Očekával se technický (AES).  | Očekávaný technický (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Edge 11      | Windows 10, Windows 8.1 Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | blesk                | blesk              | Silverlight (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | nepodporováno                |
| Edge           | Xbox 1<sup>(aktualizace</sup> 2015)                   | azureHtml5JS           | azureHtml5JS         | nepodporováno                |
| Chrome 37 +     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | blesk                | blesk              | Silverlight (PlayReady)    |
| Safari         | iOS 6+                                                   | HTML5                  | HTML5 (bez tokenu) 3    | nepodporováno                |
| Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X horská Lion<sup>1</sup>                           | blesk                | blesk              | Silverlight (PlayReady)    |
| Chrome 37 +     | Android 4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | Android 4,02                                             | HTML5                  | HTML5 (bez tokenu)<sup>3</sup>    | nepodporováno                |
| Firefox 42 +    | Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | nepodporováno                |
| IE 8, IE 9, IE 10  | Windows                                                  | nepodporováno          | nepodporováno        | nepodporováno                |

<sup>1</sup> konfigurace není podporována nebo testována; uvádí se jako referenční dokumentace pro dokončení.

<sup>2</sup> úspěšné přehrávání na zařízeních s Androidem vyžaduje kombinaci možností zařízení, podporu grafiky, vykreslování kodeků, podporu operačních systémů a další. Vzhledem k tomu, že Android je open source platforma, která telefonním výrobcům umožňuje změnit Vanilla Android, který poskytuje Google, to způsobí určitou fragmentaci v prostoru Androidu a některá zařízení nemusí být podporovaná z důvodu nedostatku funkcí. Některá zařízení s Androidem navíc nepodporují všechny kodeky.  

<sup>3</sup> v případech, kdy není k dispozici podpora tokenu, lze použít proxy server k přidání této funkce. Další informace o tomto řešení najdete na tomto [blogu](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) .

> [!NOTE]
> Pokud se zvolený technický typ vyžaduje, aby byl nainstalovaný modul plug-in, jako je třeba Flash a který není v počítači uživatele nainstalovaný, bude AMP dál kontrolovat možnosti další tech. ve spojení s typy zdrojů a informacemi o ochraně v seznamu tech. Pokud se například pokusíte zobrazit nechráněný Stream na vyžádání v Safari 8 v OS X Yosemite a aplikace Flash i Silverlight nejsou nainstalované, AMP vybere nativní technologii Html5 tech for přehrávání.<br/><br/>Nové technologie prohlížeče se objevují každý den a stejně tak by to mohlo mít vliv na tuto matici.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)