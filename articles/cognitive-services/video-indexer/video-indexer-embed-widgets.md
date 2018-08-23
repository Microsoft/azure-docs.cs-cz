---
title: Vložení widgetů Azure Video Indexer do svých aplikací | Dokumentace Microsoftu
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: ba81030c3d6384ca6b66d6a3b14e614d1626e3e0
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "41987499"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Vložení widgetů Video Indexer do vašich aplikací

Video Indexer podporuje dva typy vkládání widgetů do aplikace: **kognitivní přehledy** a **Player**. 

* A **kognitivní přehledy** pomůcka zahrnuje všechny vizuální přehledy, které se extrahují z videa indexování procesu. 
    Ve widgetu insights podporuje následující volitelné parametry adresy URL:

    |Název|Definice|Popis|
    |---|---|---|
    |pomůcky|Řetězců oddělené čárkami|Umožňuje řídit insights, který chcete vykreslit. <br/>Příklad: **widgety lidí, značky =** budou vykreslovat pouze lidé a značky insights uživatelského rozhraní<br/>Dostupné možnosti: uživatelé, klíčová slova, poznámky, značky, zabarvení, přepis, hledání | 
* A **Player** widgetu umožňuje Streamovat videa pomocí adaptivní přenosové rychlosti.

    Ve widgetu player podporuje následující volitelné parametry adresy URL:

    |Název|Definice|Popis|
    |---|---|---|
    |t|Počet sekund od začátku|Díky player začít přehrávat v daném časovém bodu.<br/>Příklad: t = 60|
    |titulky|Kód jazyka|Během načítání k dispozici v nabídce titulky widgetu načte titulek v daném jazyce.<br/>Příklad: popisky = en-Us|
    |showCaptions|Hodnotu typu boolean|Díky hráč načíst data pomocí popisků již povoleno.<br/>Příklad: showCaptions = true|
    |type||Aktivuje zvukový přehrávač vzhledu (video část odebrána).<br/>Příklad: Zadejte = zvuku|
    |Automatické přehrávání|Hodnotu typu boolean|Rozhodněte, zda hráč začínat přehrávání videa při načtení (výchozí hodnota je true).<br/>Příklad: přehrávání = false|
    |Jazyk|Kód jazyka|Ovládací prvek hráč řídí lokalizace (výchozí hodnota je en US)<br/>Příklad: jazyk = de-DE|

## <a name="embedding-public-content"></a>Vkládání veřejného obsahu

1. Přihlaste se k vaší [Video Indexer](https://api-portal.videoindexer.ai/) účtu. 
2. Klikněte na tlačítko "Vložit", který se zobrazí pod videem.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknutí na tlačítko, vložení modální okno se zobrazí na obrazovce, ve kterém můžete zvolit, jaké widgetu chcete vložení ve vaší aplikaci.
    Výběr widget (**Player** nebo **kognitivní přehledy**), vygeneruje vložený kód pro vložení v aplikaci.
 
3. Zvolte typ ovládacího prvku chcete (**kognitivní přehledy** nebo **Player**).
4. Zkopírujte kód pro vložení a přidat do svojí aplikace. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Vložení soukromého obsahu

Můžete získat Vložit kódy pro vložení automaticky otevíraná okna (jak je znázorněno v předchozí části) pro **veřejné** pouze videa. 

Pokud chcete vložit **privátní** videa, musíte předat přístupového tokenu v **iframe**společnosti **src** atribut:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Použít [ **získat přehledy widgetu** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) rozhraní API k získání obsahu widgetu kognitivní přehledy, nebo použijte [ **Get Video přístup na Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) a přidejte ho jako dotazování odesílaných na adresu url, jak je znázorněno výše. Zadejte tuto adresu URL jako **iframe**společnosti **src** hodnotu.

Pokud chcete poskytnout přehledy funkce pro úpravy (jako máme v našich webové aplikace) v vloženého widgetu, budete muset předat přístupový token s oprávnění k úpravám. Použití [ **získat přehledy widgetu** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) nebo [ **Get Video přístup na Token** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) s **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Interakce widgetů

**Kognitivní přehledy** widgetu můžete pracovat s videem ve své aplikaci. Tato část ukazuje, jak dosáhnout tuto interakci.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Video Indexer pomůcky ke komunikaci s ostatními součástmi získáte služby Video Indexer provede následující akce:

- Používá komunikaci mezi zdroji HTML5 metoda **zpravy** a 
- Ověří zprávy napříč VideoIndexer.ai původu. 

Pokud se rozhodnete implementovat vlastní kód player a proveďte integraci s **kognitivní přehledy** widgetů, je vaší odpovědností, abyste ověření původu zprávu, která pochází z VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Vložení oba typy widgetů do aplikace / blogu (doporučeno) 

Tato část ukazuje, jak dosáhnout interakce mezi dvě pomůcky Video Indexer tak když uživatel klepne na ovládací prvek insight ve své aplikaci, hráč přejde na příslušné místo.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopírovat **Player** kód pro vložení widgetu.
2. Kopírovat **kognitivní přehledy** kód pro vložení.
3. Přidat [ **zprostředkovatel souboru** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) zpracování komunikace mezi dvě pomůcky:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Nyní když uživatel klikne na ovládací prvek insight ve své aplikaci, hráč přejde na příslušné místo.

Další informace najdete v tématu [tuto ukázku](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu kognitivní přehledy a Azure Media Player umožňuje přehrávání obsahu

Tato část ukazuje, jak dosáhnout interakce mezi **kognitivní přehledy** widgetů a instanci Azure Media Player pomocí [modulu plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Přidáte modul plug-in Video Indexer hráč AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Vytvoření instance Azure Media Player pomocí modulu plug-in Video Indexer.

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

3. Kopírovat **kognitivní přehledy** kód pro vložení.

Je třeba nyní schopen komunikovat s vaší Azure Media Playeru.

Další informace najdete v tématu [tuto ukázku](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Vložení widgetu kognitivní přehledy Video indexeru a použijte vlastní přehrávač (může být jakékoli player)

Pokud používáte vlastní přehrávač, budete muset starat o manipulaci s přehrávače sami, abyste dosáhli komunikace. 

1. Vložte přehrávače videa.

    Například standardní přehrávači HTML5.

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Vložení widgetu kognitivní přehledy.
3. Implementace komunikace přehrávače prostřednictvím naslouchání události "zpráva". Příklad:

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

## <a name="adding-subtitles"></a>Přidání titulků

Pokud vložíte přehledů funkce Video Indexer s přehrávače AMP, můžete použít **GetVttUrl** metodu k získání titulky (titulky). Můžete také volat metodu javascript z modulu Video Indexer AMP **getSubtitlesUrl** (jak je uvedeno výše). 

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení Vložitelný widgetů

### <a name="cognitive-insights-widget"></a>Widget kognitivní přehledy
Můžete zvolit typy přehledů, které chcete, aby tak, že zadáte jako hodnotu pro následující parametr URL přidat kód pro vložení získáte (z rozhraní API nebo z webové aplikace):

**& pomůcky =** \<seznam požadovaného widgety >

Možné hodnoty jsou: uživatelé, kteří, klíčová slova, zabarvení, přepis, hledání.

Například pokud chcete pro vložení widgetu, který obsahuje pouze lidé a vyhledávání informací adresu URL vložení prvku iframe bude vypadat například takto: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, hledání

Záhlaví okna iframe se taky dají upravit poskytnutím **& název =** <YourTitle> na adresu url elementu iframe. (Přizpůsobí html \<title > hodnota).
Například pokud chcete poskytnout název "MyInsights" okno iframe, adresa url bude vypadat například takto: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Všimněte si, že je tato možnost je relevantní pouze v případech, když budete muset otevřít přehledy v novém okně.

### <a name="player-widget"></a>Přehrávač widgetu
Je-li vložit Video Indexer player můžete velikost hráč tak, že určíte velikost elementu iframe.

Příklad:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Ve výchozí přehrávač Video Indexer bude mít automaticky generované skryté titulky založené na přepisu videa, která se extrahují z videa s Zdrojový jazyk, který byl vybrán při videa na server nebyl nahrán.

Pokud chcete vložit v jiném jazyce můžete přidat **& popisky = < jazyka | "vše" | "false" >** player vloženou adresu URL nebo put "all" jako hodnotu, pokud budete chtít mít všechny dostupné jazyky titulky.
Pokud chcete, aby popisky, který se má zobrazit ve výchozím nastavení můžete předat **& showCaptions = true**

Adresa URL vložení pak bude vypadat například takto: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Pokud chcete zakázat popisky můžete předat jako hodnota parametru titulky "false".

Auto play – ve výchozím nastavení, které hráč se spustí přehrávání videa. Můžete také nikoli k předání & automatické přehrávání = false výše na adresu URL vložení.

## <a name="next-steps"></a>Další postup

Informace o tom, jak zobrazení a úprava přehledů funkce Video Indexer najdete v tématu [to](video-indexer-view-edit.md) článku.

Podívejte se také, [Video indexer codepen](https://codepen.io/videoindexer/pen/eGxebZ).
