---
title: Vložení adaptivního streamovacího videa MPEG-DASH do aplikace HTML5 pomocí souboru DASH.js | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak vložit adaptivní streamovací video MPEG-DASH do aplikace HTML5 s souborem DASH.js.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564852"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 využívající DASH.js  

## <a name="overview"></a>Přehled
MPEG-DASH je standard ISO pro adaptivní streamování videoobsahu, který nabízí významné výhody pro vývojáře, kteří chtějí poskytovat vysoce kvalitní, adaptivní výstup pro streamování videa. S MPEG-DASH se datový proud videa automaticky přizpůsobí nižšímu rozlišení, když se síť zahltí. Tím se snižuje pravděpodobnost, že se divákovi uvidí "pozastavené" video, zatímco přehrávač stáhne několik dalších sekund, aby se přehrál (to znamená, že snižuje pravděpodobnost ukládání do vyrovnávací paměti). Jak se snižuje zahlcení sítě, přehrávač videa se zase vrátí k kvalitnějšímu proudu. Tato schopnost přizpůsobit požadovanou šířku pásma také vede k rychlejšímu spuštění videa. To znamená, že prvních několik sekund lze přehrát v segmentu s nižší kvalitou rychlého stažení a poté zvýšit vyšší kvalitu, jakmile bude dostatečný obsah uložen do vyrovnávací paměti.

Dash.js je open-source MPEG-DASH video přehrávač napsaný v JavaScriptu. Jeho cílem je poskytnout robustní, multiplatformní přehrávač, který lze volně znovu použít v aplikacích, které vyžadují přehrávání videa. Poskytuje přehrávání MPEG-DASH v libovolném prohlížeči, který podporuje W3C Media Source Extensions (MSE), dnes to je Chrome, Microsoft Edge a IE11 (ostatní prohlížeče uvedly svůj záměr podporovat MSE). Další informace o dash.js, js naleznete v úložišti GitHub dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Vytvoření přehrávače streamovaného videa založeného na prohlížeči
Chcete-li vytvořit jednoduchou webovou stránku, která zobrazuje přehrávač videa s očekávanými ovládacími prvky, jako je přehrávání, pozastavení, převíjení vzad atd., musíte:

1. Vytvoření stránky HTML
2. Přidání značky videa
3. Přidání přehrávače dash.js
4. Inicializovat přehrávač
5. Přidání nějakého stylu CSS
6. Zobrazit výsledky v prohlížeči, který implementuje MSE

Inicializace přehrávače může být dokončena v několika řádcích kódu JavaScript. Pomocí dash.js, je to opravdu tak jednoduché vložit MPEG-DASH video ve vašem prohlížeči-založené aplikace.

## <a name="creating-the-html-page"></a>Vytvoření stránky HTML
Prvním krokem je vytvoření standardní stránky HTML obsahující prvek **videa,** uložte tento soubor jako basicPlayer.html, jak ukazuje následující příklad:

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
Chcete-li do aplikace přidat implementaci odkazu dash.js, je třeba získat soubor dash.all.js z nejnovější verze projektu dash.js. To by mělo být uloženo ve složce JavaScript vaší aplikace. Tento soubor je komfortní soubor, který spojuje všechny potřebné dash.js kód do jednoho souboru. Pokud se podíváte kolem dash.js úložiště, najdete jednotlivé soubory, testovací kód a mnoho dalšího, ale pokud vše, co chcete udělat, je použít dash.js, pak dash.all.js soubor je to, co potřebujete.

Chcete-li přidat dash.js přehrávač do svých aplikací, přidejte značku skriptu do hlavní části basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Dále vytvořte funkci pro inicializaci přehrávače při načtení stránky. Za řádek, ve kterém načtete soubor dash.all.js, přidejte následující skript:

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

Tato funkce nejprve vytvoří DashContext. Používá se ke konfiguraci aplikace pro konkrétní prostředí runtime. Z technického hlediska definuje třídy, které by měl y framework vkládání závislostí použít při vytváření aplikace. Ve většině případů používáte Dash.di.DashContext.

Dále vytvořte instanci primární třídy dash.js framework, MediaPlayer. Tato třída obsahuje základní metody potřebné, jako je přehrávání a pozastavení, spravuje vztah s elementem videa a také spravuje interpretaci souboru MpD (Media Presentation Description), který popisuje přehráno video.

Funkce startup() třídy MediaPlayer je volána, aby bylo zajištěno, že přehrávač je připraven k přehrávání videa. Funkce mimo jiné zajišťuje, že byly načteny všechny nezbytné třídy (definované kontextem). Jakmile je přehrávač připraven, můžete k němu připojit prvek videa pomocí funkce attachView(). Funkce spuštění umožňuje mediaplayeru vstříknout datový proud videa do prvku a podle potřeby také řídit přehrávání.

Předaj adresu URL souboru MPD do přehrávače MediaPlayer, aby věděl o videu, které má přehrát. SetupVideo() funkce právě vytvořena bude muset být provedena, jakmile se stránka plně načte. Proveďte to pomocí události onload prvku těla. Změňte `<body>` prvek na:

```html
    <body onload="setupVideo()">
```

Nakonec nastavte velikost prvku videa pomocí CSS. V adaptivním prostředí streamování je to obzvláště důležité, protože velikost přehrávaného videa se může měnit, protože přehrávání se přizpůsobuje měnícím se podmínkám sítě. V této jednoduché demo jednoduše vynutit video prvek, který má být 80% z dostupných okna prohlížeče přidáním následující CSS do hlavní části stránky:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Přehrávání videa
Chcete-li přehrát video, namiřte prohlížeč na soubor basicPlayback.html a klikněte na tlačítko Přehrát na zobrazeném přehrávači videa.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také

[Úložiště Dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js) 

