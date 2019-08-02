---
title: Vložení Video Indexer widgetů do aplikací
titlesuffix: Azure Media Services
description: Zjistěte jak vložit widgety Video Indexeru do vaší aplikace.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ec3c7379c8c7f28765fbc4396d3e9804a6c127f6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663746"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Vložení Video Indexer widgetů do aplikací

Tento článek ukazuje, jak vložit widgety Video Indexeru do vašich aplikací. Video Indexer podporuje vkládání tří typů widgetů do vaší aplikace: Přehledy, **přehrávač**a **Editor**pro **rozpoznávání**. 

Počínaje verzí 2 se základní adresa URL widgetu skládá z oblasti zadaného účtu. Například účet v oblasti Západní USA generuje: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typy widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Widget **Cognitive Insights** zahrnuje všechny vizuální přehledy, které se extrahovaly z indexování videa. Widget přehledů podporuje tyto volitelné parametry adresy URL:

|Name|Definice|Popis|
|---|---|---|
|`widgets`|Řetězce oddělené čárkami|Umožňuje řídit, které přehledy chcete vykreslit. <br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` vykreslí jenom přehledy lidí a značek<br/>Dostupné možnosti: people (lidé), keywords (klíčová slova), annotations (poznámky), brands (značky), sentiments (citová zabarvení), transcript (přepis), search (vyhledávání).<br/>Není podporováno prostřednictvím adresy URL ve version=2<br/><br/>**Poznámka:** Parametr adresy URL widgets není ve verzi 2 podporován. |
|`locale`|Krátký kód jazyka|Řídí jazyk Insights. Výchozí hodnota je `en`. Například: `language=de`.|
|`tab`|Výchozí vybraná karta|Řídí kartu Přehledy, která je ve výchozím nastavení vykreslená. `tab=timeline`vykreslí přehledy pomocí vybrané karty Časová osa.|

### <a name="player-widget"></a>Widget Player

Widget **Player** umožňuje streamovat video pomocí adaptivní přenosové rychlosti. Widget přehrávače podporuje tyto volitelné parametry adresy URL:

|Name|Definice|Popis|
|---|---|---|
|`t`|Sekund od začátku|Přehrávač začne přehrávat od daného časového bodu.<br/>Příklad: `t=60`.|
|`captions`|Kód jazyka|Při načítání widgetu načte titulky v daném jazyce, aby byly k dispozici v nabídce titulků.<br/>Příklad: `captions=en-US`.|
|`showCaptions`|Logická hodnota|Přehrávač se načte s už povolenými titulky.<br/>Příklad: `showCaptions=true`.|
|`type`||Aktivuje vzhled přehrávače zvuku (video část se odebere).<br/>Příklad: `type=audio`.|
|`autoplay`|Logická hodnota|Udává, jestli má přehrávač začít přehrávat video při načtení (výchozí hodnota je true).<br/>Příklad: `autoplay=false`.|
|`language`|Kód jazyka|Určuje jazyk přehrávače (výchozí hodnota je en-US)<br/>Příklad: `language=de-DE`.|

### <a name="editor-widget"></a>Widget pro Editor 

Pomůcka **editoru** umožňuje vytvářet nové projekty a spravovat přehledy videí.

|Name|Definice|Popis|
|---|---|---|
|`accessToken`<sup>*</sup>|Řetězec|`accessToken` Parametr je vyžadován při použití widgetu editoru.<br/>Přístupový token poskytuje přístup k videím, která jsou pouze v rámci účtu, který se používá k vložení widgetu. |
|`language`|Kód jazyka|Určuje jazyk přehrávače (výchozí hodnota je en-US)<br/>Příklad: `language=de-DE`.|
|`locale`|Krátký kód jazyka|Řídí jazyk Insights. Výchozí hodnota je `en`. Například: `language=de`.|

<sup>*</sup>Vlastník by měl poskytovat `accessToken` upozornění. 

## <a name="embedding-public-content"></a>Vkládání veřejného obsahu

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Klikněte na video, se kterým chcete pracovat.
3. Klikněte na tlačítko „vložení“, které se zobrazuje pod videem.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknutí na tlačítko se na obrazovce zobrazí modální okno vložení, kde můžete zvolit, který widget chcete do aplikace vložit.
    Výběr widgetu (**rozpoznávání přehledů**, **přehrávač**nebo **Editor**) vygeneruje vložený kód, který chcete vložit do aplikace.
 
4. Vyberte požadovaný typ widgetu (**rozpoznávání přehledů**, **přehrávač**nebo **Editor**).
5. Zkopírujte kód pro vložení a přidejte ho do své aplikace. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Pokud máte problémy se sdílením adres URL vašich videí, zkuste do odkazu přidat parametr Location (umístění). Parametr by měl být nastaven na [oblasti Azure, ve kterých video indexer existuje](regions.md). Například, `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Vložení soukromého obsahu

Kód pro vložení můžete z modálního okna vložení (znázorněného v předchozí části) získat jenom pro **veřejná** videa. 

Pokud chcete vložit **soukromé** video, musíte do atributu **src** prvku **iframe** předat přístupový token:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Pomocí rozhraní API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) (Získat widget přehledů) získejte obsah widgetu Cognitive Insights nebo použijte API [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) (Získat přístupový token videa) a přidejte ho jako parametr dotazu do adresy URL, jak je vidět výše. Tuto adresu URL zadejte jako hodnotu **src** prvku **iframe**.

Pokud chcete ve vloženém widgetu umožnit přehledy s úpravami (jako máme v naší webové aplikaci), budete muset předat přístupový token s oprávněním k úpravám. Použijte [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) nebo [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) s parametrem **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interakce widgetů

Widget **Cognitive Insights** podporuje interakci s videem ve vaší aplikaci. V této části si ukážeme, jak této interakce dosáhnout.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Aby widgety Video Indexeru komunikovaly s ostatními součástmi, provede Video Indexer tohle:

- Použije metodu HTML5 komunikace mezi zdroji **postMessage** a 
- Ověří původ zprávy z webu VideoIndexer.ai 

Pokud se rozhodnete implementovat vlastní kód přehrávače a provést integraci s widgety **Cognitive Insights**, je vaší odpovědností, abyste ověřili původ zprávy, která pochází z webu VideoIndexer.ai.

### <a name="embed-widgets-in-your-application--blog-recommended"></a>Vložení widgetů do aplikace nebo blogu (doporučeno) 

Tato část ukazuje, jak dosáhnout interakce mezi dvěma widgety Video Indexeru tak, že když uživatel klikne na ovládací prvek přehledu ve vaší aplikaci, přehrávač přejde na příslušnou pozici videa.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Zkopírujte kód pro vložení widgetu **Player**.
2. Zkopírujte kód pro vložení widgetu **Cognitive Insights**.
3. Přidejte [**zprostředkující soubor**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ke zpracování komunikace mezi oběma widgety:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Když teď uživatel klikne na ovládací prvek přehledu ve vaší aplikaci, přehrávač přejde na příslušnou pozici videa.

Další informace najdete v [této ukázce](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu Cognitive Insights a přehrání obsahu pomocí Azure Media Playeru

Tato část ukazuje, jak dosáhnout interakce mezi widgetem **Cognitive Insights** a instancí Azure Media Playeru pomocí [plug-inu pro AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Přidejte plug-in Video Indexeru pro přehrávač AMP.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Vytvořte instanci Azure Media Playeru pomocí plug-inu Video Indexeru.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Zkopírujte kód pro vložení widgetu **Cognitive Insights**.

Teď by mělo být možné komunikovat s vaším Azure Media Playerem.

Další informace najdete v [této ukázce](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Vložení widgetu Video Indexeru Cognitive Insights a použití vlastního přehrávače (jakéhokoli)

Pokud používáte vlastní přehrávač, musíte se sami postarat o manipulaci s přehrávačem, abyste dosáhli komunikace. 

1. Vložte přehrávač videa.

    Například standardní přehrávač HTML5

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Vložte widget Cognitive Insights.
3. Implementujte komunikaci pro přehrávač prostřednictvím naslouchání události „zpráva“. Příklad:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Další informace najdete v [této ukázce](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Přidání titulků

Pokud vkládáte Video Indexer Insights s vlastní [Azure Media Player](https://aka.ms/azuremediaplayer), můžete k získání titulků (titulky) použít metodu **GetVttUrl** . Můžete také volat metodu javascriptu z plug-inu Video Indexeru pro AMP **getSubtitlesUrl** (viz výše). 

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení vložitelných widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Typy přehledů, které požadujete, můžete zvolit jako hodnotu pro následující parametr URL přidaný do kódu pro vložení, který získáte (z rozhraní API nebo z webové aplikace): `&widgets=<list of wanted widgets>`.

Možné hodnoty jsou: people (lidé), keywords (klíčová slova), sentiments (citová zabarvení), transcript (přepis), search (vyhledávání).

Například pokud chcete vložit pomůcku obsahující pouze lidi a vyhledat přehledy, adresa URL pro vložení IFRAME bude vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Název okna iFrame lze také přizpůsobit poskytnutím `&title=<YourTitle>` adresy URL prvku IFRAME. (Přizpůsobí se tím hodnota html \<title>).
    
Například pokud chcete vašemu oknu IFRAME dát název "MyInsights", adresa URL bude vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Všimněte si, že je tato možnost relevantní jenom v případech, když přehledy potřebujete otevřít v novém okně.

### <a name="player-widget"></a>Widget Player

Pokud vložíte přehrávač Video Indexeru, můžete zvolit velikost přehrávače zadáním velikosti prvku iframe.

Příklad:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Ve výchozím nastavení bude mít přehrávač Video Indexeru automaticky generované titulky založené na přepisu videa, který se z videa extrahoval pomocí zdrojového jazyka zvoleného při nahrávání videa.

Pokud chcete vložit v jiném jazyce, můžete přidat do adresy URL přehrávače `&captions=< Language | ”all” | “false” >` pro vložení nebo zadat "vše" jako hodnotu, pokud chcete mít všechny dostupné titulky jazyků.
Pokud chcete, aby se popisky zobrazovaly ve výchozím nastavení, můžete předat `&showCaptions=true`.

Adresa URL pro vložení bude vypadat takto: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Pokud chcete titulky vypnout, můžete jako hodnotu parametru titulků předat „false“.

Auto play – ve výchozím nastavení začne přehrávač přehrávat video. Můžete to změnit, když do adresy URL vložení předáte &autoplay=false.

## <a name="next-steps"></a>Další postup

Informace o tom, jak zobrazit a upravit přehledy Video Indexeru, najdete v [tomto](video-indexer-view-edit.md) článku.

Podívejte se také na [video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
