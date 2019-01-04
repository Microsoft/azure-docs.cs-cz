---
title: Vložení widgetů Video Indexer do vašich aplikací
titlesuffix: Azure Media Services
description: Zjistěte jak vložit widgety Video Indexeru do vaší aplikace.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/25/2018
ms.author: juliako
ms.openlocfilehash: 2c07cfcba473e2e27f14ff0118e6ca8a8f484df1
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791810"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Vložení widgetů Video Indexer do vašich aplikací

Tento článek ukazuje, jak vložit widgety Video Indexeru do vašich aplikací. Video Indexer podporuje dva typy vkládání widgetů do aplikace: **Kognitivní přehledy** a **Player**. 

> [!NOTE]
> Počínaje 1. února 2018 se verze 1 **kognitivní přehledy** widgetu se přestanou používat. Vložit adresu URL verze bude ve výchozím nastavení `version=2`.

## <a name="widget-types"></a>Typy widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Widget **Cognitive Insights** zahrnuje všechny vizuální přehledy, které se extrahovaly z indexování videa. Widget přehledů podporuje tyto volitelné parametry adresy URL:

|Název|Definice|Popis|
|---|---|---|
|widgets|Řetězce oddělené čárkami|Umožňuje řídit, které přehledy chcete vykreslit. <br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` vykreslí jenom přehledy lidí a značek<br/>Dostupné možnosti: people (lidé), keywords (klíčová slova), annotations (poznámky), brands (značky), sentiments (citová zabarvení), transcript (přepis), search (vyhledávání).<br/>Není podporováno prostřednictvím adresy URL ve version=2<br/><br/>**Poznámka:** **Widgety** param adresa URL není podporováno, pokud **verze = 2** se používá. |
|version|Verze widgetu **Cognitive Insights**|Abyste získali přehledy toho nejnovější aktualizace widgetů, přidejte `?version=2` dotazů odesílaných na adresu URL vložení. Například `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2`. <br/> Pokud chcete získat starší verzi, stačí `version=2` z adresy URL odebrat.

### <a name="player-widget"></a>Widget Player

Widget **Player** umožňuje streamovat video pomocí adaptivní přenosové rychlosti. Widget přehrávače podporuje tyto volitelné parametry adresy URL:

|Název|Definice|Popis|
|---|---|---|
|t|Počet sekund od začátku|Přehrávač začne přehrávat od daného časového bodu.<br/>Příklad: t=60|
|captions|Kód jazyka|Při načítání widgetu načte titulky v daném jazyce, aby byly k dispozici v nabídce titulků.<br/>Příklad: captions=en-US|
|showCaptions|Logická hodnota|Přehrávač se načte s už povolenými titulky.<br/>Příklad: showCaptions=true|
|type||Aktivuje vzhled přehrávače zvuku (video část se odebere).<br/>Příklad: type=audio|"
|autoplay|Logická hodnota|Udává, jestli má přehrávač začít přehrávat video při načtení (výchozí hodnota je true).<br/>Příklad: autoplay=false|
|language|Kód jazyka|Určuje jazyk přehrávače (výchozí hodnota je en-US)<br/>Příklad: language=de-DE|

## <a name="embedding-public-content"></a>Vkládání veřejného obsahu

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Klikněte na video, se kterým chcete pracovat.
3. Klikněte na tlačítko „vložení“, které se zobrazuje pod videem.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknutí na tlačítko se na obrazovce zobrazí modální okno vložení, kde můžete zvolit, který widget chcete do aplikace vložit.
    Výběrem widgetu (**Player** nebo **Cognitive Insights**) se vygeneruje kód pro vložení do aplikace.
 
4. Zvolte typ požadovaného widgetu (**Cognitive Insights** nebo **Player**).
5. Zkopírujte kód pro vložení a přidejte ho do své aplikace. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Vložení soukromého obsahu

Kód pro vložení můžete z modálního okna vložení (znázorněného v předchozí části) získat jenom pro **veřejná** videa. 

Pokud chcete vložit **soukromé** video, musíte do atributu **src** prvku **iframe** předat přístupový token:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>
    
Pomocí rozhraní API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) (Získat widget přehledů) získejte obsah widgetu Cognitive Insights nebo použijte API [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) (Získat přístupový token videa) a přidejte ho jako parametr dotazu do adresy URL, jak je vidět výše. Tuto adresu URL zadejte jako hodnotu **src** prvku **iframe**.

Pokud chcete ve vloženém widgetu umožnit přehledy s úpravami (jako máme v naší webové aplikaci), budete muset předat přístupový token s oprávněním k úpravám. Použijte [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) nebo [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) s parametrem **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interakce widgetů

Widget **Cognitive Insights** podporuje interakci s videem ve vaší aplikaci. V této části si ukážeme, jak této interakce dosáhnout.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Aby widgety Video Indexeru komunikovaly s ostatními součástmi, provede Video Indexer tohle:

- Použije metodu HTML5 komunikace mezi zdroji **postMessage** a 
- Ověří původ zprávy z webu VideoIndexer.ai 

Pokud se rozhodnete implementovat vlastní kód přehrávače a provést integraci s widgety **Cognitive Insights**, je vaší odpovědností, abyste ověřili původ zprávy, která pochází z webu VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Vložení obou typů widgetů do aplikace/blogu (doporučeno) 

Tato část ukazuje, jak dosáhnout interakce mezi dvěma widgety Video Indexeru tak, že když uživatel klikne na ovládací prvek přehledu ve vaší aplikaci, přehrávač přejde na příslušnou pozici videa.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Zkopírujte kód pro vložení widgetu **Player**.
2. Zkopírujte kód pro vložení widgetu **Cognitive Insights**.
3. Přidejte [**zprostředkující soubor**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ke zpracování komunikace mezi oběma widgety:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Když teď uživatel klikne na ovládací prvek přehledu ve vaší aplikaci, přehrávač přejde na příslušnou pozici videa.

Další informace najdete v [této ukázce](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu Cognitive Insights a přehrání obsahu pomocí Azure Media Playeru

Tato část ukazuje, jak dosáhnout interakce mezi widgetem **Cognitive Insights** a instancí Azure Media Playeru pomocí [plug-inu pro AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Přidejte plug-in Video Indexeru pro přehrávač AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


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

Pokud vložíte přehledy Video Indexeru s vlastním přehrávačem AMP, můžete použít metodu **GetVttUrl** k získání titulků. Můžete také volat metodu javascriptu z plug-inu Video Indexeru pro AMP **getSubtitlesUrl** (viz výše). 

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení vložitelných widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights
Typy přehledů, které chcete, můžete zvolit tak, že je zadáte jako hodnotu do následujícího parametru URL přidaného do získaného kódu pro vložení (z rozhraní API nebo z webové aplikace):

**&widgets=** \<seznam požadovaných widgetů>

Možné hodnoty jsou: people (lidé), keywords (klíčová slova), sentiments (citová zabarvení), transcript (přepis), search (vyhledávání).

Pokud třeba chcete vložit widget obsahující jenom přehledy lidí a vyhledávání, bude adresa URL vložení prvku iframe vypadat takto: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search

Název okna iframe se taky dá přizpůsobit zadáním **&title=**<YourTitle> do URL prvku iframe. (Přizpůsobí se tím hodnota html \<title>).
Pokud třeba chcete oknu iframe dát název „MojePrehledy“, adresa URL bude vypadat takto: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MojePrehledy. Všimněte si, že je tato možnost relevantní jenom v případech, když přehledy potřebujete otevřít v novém okně.

### <a name="player-widget"></a>Widget Player
Pokud vložíte přehrávač Video Indexeru, můžete zvolit velikost přehrávače zadáním velikosti prvku iframe.

Příklad:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />

Ve výchozím nastavení bude mít přehrávač Video Indexeru automaticky generované titulky založené na přepisu videa, který se z videa extrahoval pomocí zdrojového jazyka zvoleného při nahrávání videa.

Pokud chcete vložení provést s jiným jazykem, můžete do URL vložení přehrávače přidat **&captions=< Language | ”all” | “false” >** nebo jako hodnotu zadat „all“, pokud chcete mít titulky ve všech dostupných jazycích.
Pokud chcete, aby se titulky ve výchozím nastavení zobrazovaly, můžete předat parametr **&showCaptions=true**

Adresa URL vložení pak bude vypadat třeba takto: https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian. Pokud chcete titulky vypnout, můžete jako hodnotu parametru titulků předat „false“.

Auto play – ve výchozím nastavení začne přehrávač přehrávat video. Můžete to změnit, když do adresy URL vložení předáte &autoplay=false.

## <a name="next-steps"></a>Další postup

Informace o tom, jak zobrazit a upravit přehledy Video Indexeru, najdete v [tomto](video-indexer-view-edit.md) článku.

Podívejte se také na [CodePen Video Indexeru](https://codepen.io/videoindexer/pen/eGxebZ).
