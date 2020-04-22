---
title: Technologie přehrávání přehrávače médií Azure
description: Přečtěte si další informace o technologii přehrávání přehrávané k přehrávání videa nebo zvuku.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726484"
---
# <a name="playback-technology-tech"></a>Technologie přehrávání ("tech") #

Technologie přehrávání označuje konkrétní technologii prohlížeče nebo pluginu používanou k přehrávání videa nebo zvuku.

- **azureHtml5JS**: Využívá standardy MSE a EME ve spojení s prvkem videa pro přehrávání obsahu DASH bez pluginů s podporou šifrovaného obsahu aes-128 bitové obálky nebo běžného šifrovaného obsahu DRM (přes PlayReady a Widevine, když to prohlížeč podporuje) z Azure Media Services
- **flashSS**: využívá technologii flash player k přehrávání plynulého obsahu s podporou dešifrování obálky AES-128 bit ze služby Azure Media Services - vyžaduje verzi Flash 11.4 nebo vyšší
- **html5FairPlayHLS**: využívá Safari specifické v prohlížeči-založené přehrávání technologie přes HLS s video prvek. Tato technologie je vyžaduje přehrání chráněného obsahu FairPlay ze služby Azure Media Services a byla přidána do techOrder u 10/19/16
- **silverlightSS:** Využívá technologii silverlight k přehrávání plynulého obsahu s podporou obsahu chráněného službou PlayReady ze služby Azure Media Services.
- **html5**: využívá v prohlížeči-založené přehrávání technologie s video prvek.  Když se na apple iOS nebo Android zařízení, tato technologie umožňuje přehrávání HLS proudů s nějakou základní podporu pro AES-128 bit obálky šifrování nebo DRM obsahu (přes FairPlay, když prohlížeč podporuje).

## <a name="tech-order"></a>Technická objednávka ##

Aby bylo zajištěno, že váš datový zdroj je hratelný na široké škále zařízení, `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` doporučujeme následující technickou objednávku a je výchozí, pokud: a lze je nastavit přímo na `<video>` nebo programově v možnostech:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

– nebo –

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Matice kompatibility ##

Vzhledem k doporučené muka s streamování obsahu ze služby Azure Media Services se očekává následující matice přehrávání kompatibility

| Prohlížeč        | Operační systém                                                       | Očekávané technologie (vymazat)  | Očekávané technologie (AES)  | Očekávaná technologie (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeiE 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | blikáSS                | blikáSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8,1                                        | azureHtml5JS           | azureHtml5JS         | není podporováno                |
| Edge           | Xbox One<sup>1</sup> (aktualizace z listopadu 2015)                   | azureHtml5JS           | azureHtml5JS         | není podporováno                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | blikáSS                | blikáSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (bez tokenu)3    | není podporováno                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Puma<sup>1</sup>                           | blikáSS                | blikáSS              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (bez tokenu)<sup>3</sup>    | není podporováno                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | není podporováno                |
| IE 8           | Windows                                                  | není podporováno          | není podporováno        | není podporováno                |

<sup>1</sup> Konfigurace není podporována ani testována; uvedeny jako odkaz na dokončení.

<sup>2</sup> Úspěšné přehrávání na zařízeních se systémem Android vyžaduje kombinaci funkcí zařízení, podpory grafiky, vykreslování kodeků, podpory operačního systému a dalších. Vzhledem k tomu, android je open-source platforma, která umožňuje výrobcům telefonů změnit Vanilla Android OS poskytované společností Google, to způsobuje určitou fragmentaci v prostoru Android, a některá zařízení nemusí být podporovány z důvodu nedostatku funkcí. Některé zařízení Android také nemají podporu pro všechny kodeky.  

<sup>3</sup> V případech, kdy neexistuje žádná podpora tokenu, lze k přidání této funkce použít proxy server. Podívejte se na tento [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) se dozvíte více o tomto řešení.

> [!NOTE]
> Pokud očekávaná technologie vyžaduje instalaci pluginu, jako je Flash, a která není nainstalována na počítači uživatele, AMP bude i nadále kontrolovat schopnosti další technologie ve spojení s typy zdrojů a informacemi o ochraně v seznamu technologií. Pokud se například pokusíte zobrazit nechráněný datový proud na vyžádání v Safari 8 v OS X Yosemite a flash a silverlight nejsou nainstalovány, AMP vybere nativní technologii Html5 pro přehrávání.<br/><br/>Nové technologie prohlížeče se objevují denně, a jako takový by mohly mít vliv na tuto matici.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)