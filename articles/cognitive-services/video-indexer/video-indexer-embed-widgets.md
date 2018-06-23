---
title: Vložení do aplikace Azure Video Indexer pomůcky | Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/04/2018
ms.author: juliako
ms.openlocfilehash: 8da06253c58a2de474e879f18013fa1e57f5668d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343783"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Vložit Video Indexer pomůcky do svých aplikací

Video Indexer podporuje vnoření dva typy pomůcky do své aplikace: **kognitivní Insights** a **Player**. 

* A **kognitivní Insights** pomůcka zahrnuje všechny visual statistiky, které se extrahují z videa indexování procesu. 
    Pomůcka insights podporuje následující volitelné parametry adresy URL:

    |Název|Definice|Popis|
    |---|---|---|
    |pomůcky|Řetězce oddělené čárkou|Umožňuje řídit statistiky, který chcete vykreslit. <br/>Příklad: **pomůcky = osoby, značky** vykreslí jenom lidé a značky Statistika uživatelského rozhraní<br/>Dostupné možnosti: osoby, klíčová slova, poznámky, značky, chráněny, přepis, vyhledávání | 
* A **Player** pomůcky umožňuje Streamovat videa pomocí adaptivní přenosové rychlosti.

    Pomůcka player podporuje následující volitelné parametry adresy URL:

    |Název|Definice|Popis|
    |---|---|---|
    |T|Sekund od začátku|Díky player začít přehrávat od daného okamžiku bodu.<br/>Příklad: t = 60|
    |titulky|Kód jazyka|Během načítání být k dispozici v nabídce titulky pomůcku načte titulek v daném jazyce.<br/>Příklad: titulky = en-Us|
    |showCaptions|Logická hodnota|Díky player zatížení s titulky již povoleno.<br/>Příklad: showCaptions = true|
    |type||Aktivuje vzhled zvuk přehrávače (video část odebrána).<br/>Příklad: Zadejte = zvuk|
    |Automatické přehrávání disků.|Logická hodnota|Rozhodněte, pokud přehrávač začněte přehrávání videa při načtení (výchozí hodnota je true).<br/>Příklad: automatické přehrávání disků = false|
    |jazyk|Kód jazyka|Ovládací prvek přehrávač ovládací prvky lokalizace (výchozí hodnota je en US)<br/>Příklad: jazyk = de-DE|

## <a name="embedding-public-content"></a>Vložení veřejnému obsahu

1. Přihlaste se k vaší [Video Indexer](https://api-portal.videoindexer.ai/) účtu. 
2. Klikněte na tlačítko "Vložit", který se zobrazí pod videa.

    ![Pomůcky](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknutí na tlačítko, vložení modální zobrazí na obrazovce, kde si můžete vybrat jaké pomůcky chcete vložení v aplikaci.
    Výběr widget (**Player** nebo **kognitivní Insights**), generuje vložený kód pro vložení v aplikaci.
 
3. Zvolte typ ovládacího prvku chcete (**kognitivní Insights** nebo **Player**).
4. Zkopírujte kód pro vložení a přidat do vaší aplikace. 

    ![Pomůcky](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Vložení soukromý obsah

Můžete získat Vložit kódy z vložení automaticky otevíraná okna (jak je uvedeno v předchozí části) pro **veřejné** pouze videa. 

Pokud chcete vložit **privátní** video, musíte předat přístupový token v **iframe**na **src** atribut:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Použít [ **získat přehledy pomůcky** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) rozhraní API k získání obsahu pomůcky kognitivní statistiky, nebo použijte [ **získat Video přístup na Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) a přidat jako dotaz param na adresu url, jak je uvedeno výše. Zadejte tuto adresu URL jako **iframe**na **src** hodnotu.

Pokud chcete zajistit úpravy možnosti přehledy (jako jsou máme v našich webové aplikace) ve vaší embedded pomůcky, je nutné předat přístupový token s oprávnění k úpravám. Použití [ **získat přehledy pomůcky** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) nebo [ **získat Video přístup na Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) s **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Interakce pomůcky

**Kognitivní Insights** pomůcky mohou komunikovat s video ve vaší aplikaci. V této části ukazuje, jak zajistit tato interakce.

![Pomůcky](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Pokud chcete získat Video Indexer pomůcky ke komunikaci s ostatními součástmi, službu Video Indexer provede následující akce:

- Používá komunikaci mezi zdroji HTML5 metoda **zpravy** a 
- Ověří zpráva napříč VideoIndexer.ai původu. 

Pokud budete implementovat vlastní kód player a provést integraci s **kognitivní Insights** pomůcky, je vaší povinností ověření původu zprávu, která pochází z VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Oba typy pomůcky pro vložení v aplikaci nebo blog (doporučeno) 

V této části ukazuje, jak dosáhnout interakce mezi dvě pomůcky Video Indexer, když uživatel klikne Přehled ovládacího prvku v aplikaci, přehrávač přejde do okamžiku relevantní.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopírování **Player** pomůcky kód pro vložení.
2. Kopírování **kognitivní Insights** kód pro vložení.
3. Přidat [ **zprostředkovatel souboru** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) pro zpracování komunikace mezi dvě pomůcky:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Nyní když uživatel klikne na ovládací prvek přehledy v aplikaci, přehrávač přejde do okamžiku relevantní.

Další informace najdete v tématu [tuto ukázku](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu kognitivní Insights a použijte přehrávač služby Azure Media k přehrávání obsahu

V této části ukazuje, jak dosáhnout interakce mezi **kognitivní Insights** pomůcky a instance přehrávač médií Azure pomocí [AMP modulu plug-in](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Přidáte modul plug-in Video indexeru pro AMP přehrávač.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Vytvoří instanci Azure Media Player s modul plug-in indexeru Video.

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

3. Kopírování **kognitivní Insights** kód pro vložení.

Nyní byste měli mít teď ke komunikaci s vaší Azure Media Player.

Další informace najdete v tématu [tuto ukázku](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Vložit Video Indexer kognitivní Statistika pomůcky a použijte vlastní přehrávač (můžou být všechny přehrávač)

Pokud používáte vlastní player, budete muset postará o manipulaci s vaší player sami dosáhnout komunikace. 

1. Vložte vaše přehrávání videa.

    Například standardní přehrávač HTML5

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Vložení widgetu kognitivní statistiky.
3. Implementace komunikace pro vaše player prostřednictvím naslouchání událost "zprávy". Příklad:

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


Další informace najdete v tématu [tuto ukázku](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Přidávání titulků

Pokud vložit Video Indexer Statistika s vlastními AMP player, můžete použít **GetVttUrl** metoda získat skrytých titulků (titulky). Můžete také zavolat metodu javascript z modulu plug-in Video Indexer AMP **getSubtitlesUrl** (jak je uvedeno výše). 

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení li pomůcky

### <a name="cognitive-insights-widget"></a>Pomůcka kognitivní statistiky
Můžete zvolit typy statistiky chcete zadáním jako hodnotu na následující parametr URL, které jsou přidány do kód pro vložení získáte (z rozhraní API nebo z webové aplikace):

**& pomůcky =** \<seznam požadovaného pomůcky >

Možné hodnoty jsou: uživatelé, klíčová slova, chráněny, přepis, vyhledávání.

Například pokud chcete vložit widget obsahující jenom lidé a hledání Statistika iframe vložení adresa URL bude vypadat takto: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, vyhledávání

Nadpis okna iframe lze také přizpůsobit tím, že poskytuje **& title =** <YourTitle> na adresu url elementu iframe. (Přizpůsobovat html \<title > hodnotu).
Například pokud chcete poskytnout vaše iframe okno nadpis "MyInsights", adresa url bude vypadat takto: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Všimněte si, tato možnost je relevantní pouze v případě, když potřebujete k přehledům otevřít v novém okně.

### <a name="player-widget"></a>Pomůcka Player
Pokud vložit Video Indexer player můžete zadáním velikost iframe velikost přehrávač.

Například:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Ve výchozí Video Indexer přehrávač bude mít automaticky generovány skrytých titulků podle přepis video, které se extrahují z video s jazykem zdroj, který byl vybrán při přehrávání videa na server nebyl nahrán.

Pokud chcete vložit s jiným jazykem můžete přidat **& titulky = < jazyk | "vše" | "false" >** adresu URL player vložení nebo put "All (vše) jako hodnotu, pokud budete chtít mít všechny dostupné jazyky titulky.
Pokud chcete, aby titulků, který se má zobrazit ve výchozím nastavení můžete předat **& showCaptions = true**

Adresa URL vložení pak bude vypadat například takto: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Pokud chcete zakázat titulky můžete předat jako hodnota parametru titulky "false".

Automatické play – ve výchozím nastavení bude přehrávač spustit přehrávání videa. můžete zvolit nikoli k pomocí předání & automatické přehrávání = false na adresu URL vložení výše.

## <a name="next-steps"></a>Další postup

Informace o tom, jak zobrazit a upravit Video Indexer statistiky najdete v tématu [to](video-indexer-view-edit.md) článku.

## <a name="see-also"></a>Další informace najdete v tématech

[Video s přehledem indexeru](video-indexer-overview.md)
