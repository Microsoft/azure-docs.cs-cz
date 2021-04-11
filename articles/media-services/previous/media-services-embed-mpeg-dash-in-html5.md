---
title: Vložení videa adaptivního streamování s použitím MPEG-SPOJOVNÍKu do aplikace HTML5 s DASH.js | Microsoft Docs
description: Toto téma ukazuje, jak vložit video s adaptivním streamování MPEG-SPOJOVNÍKem do aplikace HTML5 s DASH.js.
author: IngridAtMicrosoft
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 820c10a2f5bb43ff931027954b7ecf10c5b4c59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013563"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 využívající DASH.js

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Přehled
MPEG-POMLČKa je standard ISO pro adaptivní streamování obsahu videa, který nabízí významné výhody pro vývojáře, kteří chtějí poskytovat vysoce kvalitní a adaptivní streamování videí. Pomocí MPEG-POMLČKy se Stream videa automaticky upraví na nižší definici, když dojde k zahlcení sítě. Tím se snižuje pravděpodobnost, že prohlížeč zobrazuje "pozastavené" video, zatímco hráč stahuje několik dalších sekund, než se hraje (to znamená, že snižuje pravděpodobnost ukládání do vyrovnávací paměti). I když se zahlcení sítě omezuje, přehrávač videa se zase vrátí do datového proudu s vyšší kvalitou. Tato možnost přizpůsobení požadované šířky pásma také vede k rychlejšímu počátečnímu času pro video. To znamená, že prvních pár sekund je možné přehrát v rychlém stahování a v případě, že je obsah uložen do vyrovnávací paměti.

Dash.js je open source přehrávač videa ve formátu MPEG-SPOJOVNÍK napsaný v JavaScriptu. Jeho cílem je zajistit robustní přehrávač pro více platforem, který se dá volně znovu použít v aplikacích, které vyžadují přehrávání videa. Poskytuje přehrávání ve formátu MPEG-SPOJOVNÍKy v jakémkoli prohlížeči, který podporuje rozhraní MSE (W3C Media source Extensions), dnes, který je Chrome, Microsoft Edge a IE11 (ostatní prohlížeče uvedli svůj záměr podporovat MSE). Další informace o DASH.js, js najdete v úložišti dash.js GitHubu.

## <a name="creating-a-browser-based-streaming-video-player"></a>Vytvoření přehrávače videa streamování založeného na prohlížeči
Chcete-li vytvořit jednoduchou webovou stránku, která zobrazuje přehrávač videa s očekávanými ovládacími prvky, jako je přehrávání, pozastavení, převíjení atd., je třeba:

1. Vytvoření stránky HTML
2. Přidat značku videa
3. Přidání přehrávače dash.js
4. Inicializace přehrávače
5. Přidat některý styl CSS
6. Zobrazení výsledků v prohlížeči, který implementuje MSE

Inicializace přehrávače se dá provést jenom v několik řádků kódu JavaScriptu. Pomocí dash.js je v aplikacích založených na prohlížeči ve skutečnosti jednoduché vkládat videa se systémem MPEG-SPOJOVNÍK.

## <a name="creating-the-html-page"></a>Vytvoření stránky HTML
Prvním krokem je vytvořit standardní stránku HTML obsahující prvek **video** , Uložit tento soubor jako basicPlayer.html, jak ukazuje následující příklad:

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

## <a name="adding-the-dashjs-player"></a>Přidání přehrávače DASH.js
Chcete-li přidat dash.js referenční implementaci do aplikace, je nutné předash.all.js soubor z nejnovější verze dash.js projektu. Tato složka by měla být uložena ve složce JavaScriptu vaší aplikace. Tento soubor je pohodlný soubor, který do jednoho souboru vyžádá všechny nezbytné dash.js kód. Pokud se dash.js úložiště nacházíte, najdete jednotlivé soubory, testovací kód a mnoho dalšího, ale pokud chcete použít dash.js, pak je dash.all.js soubor co potřebujete.

Chcete-li přidat dash.js přehrávač do aplikací, přidejte značku skriptu do části Head v basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Dále vytvořte funkci pro inicializaci přehrávače při načtení stránky. Přidejte následující skript za řádek, ve kterém načítáte dash.all.js:

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

Tato funkce nejprve vytvoří DashContext. Slouží ke konfiguraci aplikace pro konkrétní běhové prostředí. Z technického hlediska definuje třídy, které má rozhraní injektáže Dependency Framework použít při vytváření aplikace. Ve většině případů se používá pomlčka. di. DashContext.

Dále vytvořte instanci primární třídy rozhraní dash.js Framework MediaPlayer. Tato třída obsahuje základní metody, které jsou potřeba, jako je třeba přehrát a pozastavit, spravuje relaci s prvkem videa a také spravuje výklad souboru s popisem multimediální prezentace (MPD), který popisuje video, které se má přehrát.

Je volána funkce Startup () třídy MediaPlayer, která zajistí, že je přehrávač připraven k přehrání videa. Mimo jiné funkce zajišťuje, aby byly načteny všechny potřebné třídy (definované v kontextu). Jakmile je přehrávač připravený, můžete k němu připojit prvek video pomocí funkce attachView (). Funkce Startup umožňuje MediaPlayer vložit datový proud videa do prvku a také řídit přehrávání podle potřeby.

Předejte adresu URL souboru MPD do MediaPlayer, aby ví o videu, na které se očekává přehrávání. Právě vytvořená funkce setupVideo () se bude muset provést až po úplném načtení stránky. Použijte k tomu událost Load elementu tělo. Změňte `<body>` element na:

```html
    <body onload="setupVideo()">
```

Nakonec nastavte velikost prvku video pomocí šablon stylů CSS. V prostředí adaptivního streamování je to obzvláště důležité, protože velikost přehrávaného videa se může změnit, protože přehrávání se mění podle stavu sítě. V této jednoduché ukázce stačí vynutit, aby byl prvek videa 80% dostupného okna prohlížeče přidáním následujících šablon stylů CSS do části Head stránky:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Přehrávání videa
Video nahrajete tak, že přejdete do svého prohlížeče basicPlayback.html a kliknete na Přehrát na přehrávači videa.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také

[Úložiště GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

