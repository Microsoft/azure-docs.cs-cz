---
title: Vložení do aplikace HTML5 s DASH.js Video adaptivní streamování MPEG-DASH | Microsoft Docs
description: Toto téma ukazuje, jak pro vložení do aplikace HTML5 s DASH.js Video adaptivní streamování MPEG-DASH.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 2b0e6bf643f55e1809b29def7766c58b59f4bb50
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788631"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Vložení do aplikace HTML5 s DASH.js MPEG-DASH adaptivní streamování videa
## <a name="overview"></a>Přehled
MPEG-DASH je standard ISO pro adaptivní streamování videa obsah, který nabízí významné výhody pro vývojáře, kteří chtějí poskytování vysoce kvalitních, adaptivní video streamování výstup. S MPEG-DASH datový proud videa automaticky upraví nižší definice stane zahlcení sítě. Tím se snižuje pravděpodobnost, že v prohlížeči zobrazuje "pozastavený" video při přehrávač stáhne další několik sekund přehrávání (neboli ukládání do vyrovnávací paměti). Jako zahlcení sítě snižuje, přehrávání videa pak vrátí datový proud s vyšší kvality. Tato schopnost přizpůsobit se šířky pásma požadované výsledkem také rychlejší čas spuštění videa. To znamená, že první několik sekund můžete přehrávají v segment fast stažení nižší kvality a potom krok až vyšší jednou dostatečný obsah kvality má byla uložená do vyrovnávací paměti.

Dash.js je open-source MPEG-DASH přehrávání videa napsané v jazyce JavaScript. Jeho cílem je zajistit přehrávač robustní, a platformy, které můžete volně opakovaně použít v aplikacích, které vyžadují přehrávání videa. Poskytuje MPEG-DASH přehrávání v žádný prohlížeč, který podporuje dnes W3C média zdrojového rozšíření (MSE), je Chrome, Microsoft Edge a IE11 (dalších prohlížečích označili jejich záměr pro podporu MSE). Další informace o DASH.js js, najdete v části dash.js úložiště GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Vytváření založené na prohlížeči streamování videa přehrávač
K vytvoření jednoduché webové stránky, která zobrazuje přehrávání videa s očekávané řídí takové a play, pozastavení, rewind atd., budete muset:

1. Vytvoření stránky HTML
2. Přidat video značky
3. Přidat dash.js player
4. Inicializace player
5. Přidat některé stylu CSS
6. Zobrazit výsledky v prohlížeči, který implementuje MSE

Inicializace přehrávač může dokončit jenom pár řádků kódu JavaScript. Pomocí dash.js, ve skutečnosti je to jednoduché vložit video MPEG-DASH do aplikace založené na prohlížeči.

## <a name="creating-the-html-page"></a>Vytvoření stránky HTML
Prvním krokem je vytvoření standardní stránku HTML obsahující **video** elementu, uložte tento soubor jako basicPlayer.html jako následující příklad znázorňuje:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Přidání DASH.js Player
Chcete-li přidat odkaz na implementaci dash.js k aplikaci, získat soubor dash.all.js z verzi 1.0 dash.js projektu. To má být uložen ve složce JavaScript vaší aplikace. Tento soubor je soubor pohodlí, který vrátí všechny nezbytné dash.js kód do jediného souboru. Pokud máte podívejte kolem dash.js úložiště, můžete najít jednotlivých souborů, testování kódu a mnoho dalšího, ale když všechny ji chcete je použít dash.js, je soubor dash.all.js co potřebujete.

Pokud chcete přidat dash.js player do aplikací, přidejte do části head basicPlayer.html značky script:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Dál vytvořte funkci k chybě při inicializaci přehrávač při načtení stránky. Přidejte následující skript po řádek, ve kterém můžete načíst dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Tato funkce nejprve vytvoří DashContext. Slouží ke konfiguraci aplikace pro konkrétní běhového prostředí. Z technického hlediska definuje třídy, které rozhraní vkládání závislostí musí použít při vytváření aplikace. Ve většině případů použijete Dash.di.DashContext.

Dále vytvořte instanci primární třídu rozhraní dash.js Media Player. Tato třída obsahuje metody, jako například potřeby přehrání a pozastavit, spravuje relaci s video element a také výklad soubor média prezentace popis (MPD), který popisuje video má být přehráván základní.

K zajištění, že přehrávač je připraven k přehrávání videa je volána funkce startup() třídy Media Player. Kromě jiných věcí funkce zajišťuje, že všechny potřebné třídy (podle definice v kontextu) byly načteny. Jakmile přehrávač je připraven, můžete připojit video element se pomocí funkce attachView(). Spuštění funkce umožňuje Media Player vložit do elementu datový proud videa a také řídit přehrávání podle potřeby.

Předejte adresu URL souboru MPD Media Player tak, že o video ví, že se očekává, přehrávání. Funkce setupVideo() právě vytvořili, bude nutné provést po plně načtení stránky. To provést pomocí události při načtení textu elementu. Změna vaší <body> elementu, který chcete:

```html
    <body onload="setupVideo()">
```

Nakonec nastavte velikost element video pomocí šablon stylů CSS. V adaptivní streamování prostředí je to možnost obzvláště důležité, protože velikost přehrávání videa může změnit při přehrávání přizpůsobuje na měnící se síťové podmínky. V této ukázce jednoduché jednoduše vynuťte element video být 80 % okna prohlížeče dostupné přidáním následujících šablon stylů CSS do části head stránky:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Přehrávání videa
Pokud chcete přehrát video, přejděte v prohlížeči na soubor basicPlayback.html a kliknutím na tlačítko Přehrát na přehrávání videa zobrazí.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vývoj aplikací videopřehrávače](media-services-develop-video-players.md)

[Dash.js úložiště GitHub](https://github.com/Dash-Industry-Forum/dash.js) 

