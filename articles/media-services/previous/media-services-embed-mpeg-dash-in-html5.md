---
title: Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js | Dokumentace Microsoftu
description: Toto téma ukazuje postup vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 2c8e71a058b2c770741c38f07c6c440fea90f2b2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998429"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs-legacy"></a>Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js (starší verze)

## <a name="overview"></a>Přehled
MPEG-DASH je standard ISO pro adaptivní streamování obsahu videa, která nabízí významné výhody pro vývojáře, kteří chtějí dodávat vysoce kvalitní, adaptivní streamování výstup videa. S MPEG-DASH datový proud videa při automaticky upraví na nižší definici stane zahlcení sítě. Tím se snižuje pravděpodobnost, že prohlížeč zobrazuje "pozastaveno" videa, když hráč stáhne další několik sekund přehrávání (označuje se také jako ukládání do vyrovnávací paměti). Jak snižuje zahlcení sítě, přehrávače videa zase vrátit datový proud vyšší kvality. Tato schopnost přizpůsobit požadovanou šířku pásma výsledkem také rychlejší spouštění pro video. To znamená, že prvních pár sekund můžete přehrát v segmentu rychlé stažení nižší kvality a potom krok až vyšší Jakmile dostatečná kvalitního obsahu má byla uložená do vyrovnávací paměti.

Souborem Dash.js je open source MPEG-DASH videopřehrávače napsané v jazyce JavaScript. Jeho cílem je poskytovat robustní, napříč platformami přehrávač, který lze libovolně opětovně použít v aplikacích, které vyžadují přehrávání videa. Poskytuje přehrávání MPEG-DASH v jakémkoli prohlížeči, které dnes podporuje W3C média zdrojového rozšíření (MSE), Chrome, Microsoft Edge a 11 (ostatní prohlížeče označili jejich cílem je podporovat MSE). Další informace o souborem DASH.js js, najdete v článku souborem dash.js úložiště GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Vytváření založené na prohlížeči streamování přehrávač videa
K vytvoření jednoduché webové stránky, která zobrazuje přehrávač videa s očekávané řídí takové play, pozastavení, rewind atd., budete muset:

1. Vytvoření stránky HTML
2. Přidat značku pro video
3. Přidat přehrávači souborem dash.js
4. Inicializovat přehrávači
5. Přidat některé šablony stylů CSS
6. Zobrazení výsledků v prohlížeči, který implementuje MSE

Inicializace přehrávač je možné dokončit v pouze několika řádků kódu jazyka JavaScript. Pomocí souborem dash.js, je to v podstatě to jednoduše vložit video MPEG-DASH do aplikace založené na prohlížeči.

## <a name="creating-the-html-page"></a>Vytvoření stránky HTML
Prvním krokem je vytvoření standardní stránku HTML obsahující **videa** element uložit tento soubor jako basicPlayer.html jako následující příklad ukazuje:

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

## <a name="adding-the-dashjs-player"></a>Přidání přehrávači souborem DASH.js
Chcete-li přidat souborem dash.js referenční implementaci k aplikaci, budete muset vzít dash.all.js soubor z projektu souborem dash.js verzi 1.0. To by měl být uloženy ve složce JavaScript vaší aplikace. Tento soubor je soubor usnadnění práce, která stáhne všechny potřebné souborem dash.js kódu do jednoho souboru. Pokud máte najdete kolem souborem dash.js úložiště, můžete najít jednotlivé soubory, otestovat kód a spoustu dalších věcí, ale pokud vše, co chcete udělat je použít souborem dash.js, dash.all.js souboru je, co potřebujete.

K přehrávači souborem dash.js přidejte do svých aplikací, přidáte značky skriptu do hlavní části basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

V dalším kroku vytvořte funkci inicializace hráč při načtení stránky. Přidejte následující skript po řádek, ve kterém můžete načíst dash.all.js:

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

Tato funkce vytvoří nejprve DashContext. To slouží ke konfiguraci aplikací pro konkrétní běhového prostředí. Z technického hlediska definuje třídy, které rozhraní injektáž závislostí musí použít při vytváření aplikace. Ve většině případů použít Dash.di.DashContext.

V dalším kroku vytvoření instance třídy primární rozhraní souborem dash.js MediaPlayer. Tato třída obsahuje základní metody, jako třeba přehrát a pozastavit, spravuje relace s element videa a také řídí výklad soubor média prezentace popis (MPD), který popisuje video přehrát.

Voláním funkce startup() MediaPlayer třídy se ujistěte se, že hráč připravené k přehrání videa. Mimo jiné funkce zajišťuje, že všechny nezbytné třídy (podle definice v rámci) byly načteny. Když hráč je připravená, můžete připojit element videa pomocí funkce attachView(). Po spuštění funkce umožňuje MediaPlayer k vložení do prvku datový proud videa a také řídit přehrávání podle potřeby.

Předejte adresu URL souboru MPD MediaPlayer takže ví o videa, že se má přehrát. Funkci setupVideo() právě vytvořili, bude nutné provést, jakmile plně načtení stránky. To lze proveďte pomocí události při načtení elementu těla. Změna vašeho <body> element:

```html
    <body onload="setupVideo()">
```

Nastavte velikost elementu videa pomocí šablon stylů CSS. V prostředí adaptivní streamování totiž obzvláště důležité, velikost přehrávaného videa můžou změnit, protože přehrávání se přizpůsobí měnící se síťové podmínky. V této ukázce jednoduché jednoduše vynuťte elementu video o 80 % okna prohlížeče k dispozici přidáním následující šablony stylů CSS do hlavní části stránky:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Přehrávání videa
Přehrát video, přejděte v prohlížeči na basicPlayback.html souboru a klikněte na tlačítko Přehrát v videopřehrávač zobrazí.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vývoj aplikací videopřehrávače](media-services-develop-video-players.md)

[Úložiště GitHub souborem dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

