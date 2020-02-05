---
title: Vložení Video Indexer widgetů do vašich aplikací
titleSuffix: Azure Media Services
description: Tento článek ukazuje, jak vložit Azure Media Services Video Indexer widgety do aplikace.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: ed3e2cf9830e3776886e662fd27f43f76728d6b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988715"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Vložení Video Indexer widgetů do vašich aplikací

V tomto článku se dozvíte, jak můžete ve svých aplikacích vkládat Video Indexer widgety. Video Indexer podporuje vkládání tří typů widgetů do vaší aplikace: *vnímání přehledů*, *přehrávačů*a *editorů*. 

Počínaje verzí 2 se základní adresa URL widgetu skládá z oblasti zadaného účtu. Například účet v Západní USA oblasti generuje: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typy widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Pomůcka pro rozpoznávání přehledů zahrnuje všechny Visual Insights, které byly extrahovány z procesu indexování videí. Pomůcka pro rozpoznávání poznatků podporuje následující volitelné parametry adresy URL.

|Name (Název)|Definice|Popis|
|---|---|---|
|`widgets`|Řetězce oddělené čárkami|Umožňuje řídit přehledy, které chcete vykreslit. <br/> Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` vykreslí jenom osoby a bude mít přehled o uživatelském rozhraní.<br/>Dostupné možnosti: people (lidé), keywords (klíčová slova), annotations (poznámky), brands (značky), sentiments (citová zabarvení), transcript (přepis), search (vyhledávání).<br/>Všimněte si, že parametr URL `widgets` není ve verzi 2 podporován.<br/>|
|`locale`|Krátký kód jazyka|Řídí jazyk Insights. Výchozí hodnota je `en`. <br/> Příklad: `locale=de`.|
|`tab`|Výchozí vybraná karta|Řídí kartu **přehledy** , která je ve výchozím nastavení vykreslená. <br/> Příklad: `tab=timeline` vykreslí přehledy s vybranou kartou **Časová osa** .|

### <a name="player-widget"></a>Widget Player

Pomůcku přehrávače můžete použít ke streamování videa pomocí adaptivní přenosové rychlosti. Widget přehrávače podporuje následující volitelné parametry adresy URL.

|Name (Název)|Definice|Popis|
|---|---|---|
|`t`|Sekund od začátku|Nastaví, aby se hráč od zadaného časového bodu začal přehrávat.<br/> Příklad: `t=60`.|
|`captions`|Kód jazyka|Načte titulek v zadaném jazyce během načítání pomůcky, aby byl dostupný v nabídce **titulků** .<br/> Příklad: `captions=en-US`.|
|`showCaptions`|Logická hodnota|Přehrávač se načte s už povolenými titulky.<br/> Příklad: `showCaptions=true`.|
|`type`||Aktivuje vzhled zvukového přehrávače (součást videa se odebrala).<br/> Příklad: `type=audio`.|
|`autoplay`|Logická hodnota|Určuje, zda by měl přehrávač při načtení videa začít přehrávat video. Výchozí hodnota je `true`.<br/> Příklad: `autoplay=false`.|
|`language`|Kód jazyka|Řídí jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`.|

### <a name="editor-widget"></a>Widget pro Editor

Pomůcku editoru můžete použít k vytvoření nových projektů a správě přehledů videa. Pomůcka editoru podporuje následující volitelné parametry adresy URL.

|Name (Název)|Definice|Popis|
|---|---|---|
|`accessToken`<sup>*</sup>|Řetězec|Poskytuje přístup k videím, která jsou pouze v účtu, který se používá k vložení widgetu.<br> Pomůcka editoru vyžaduje parametr `accessToken`.|
|`language`|Kód jazyka|Řídí jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`.|
|`locale`|Krátký kód jazyka|Řídí jazyk Insights. Výchozí hodnota je `en`.<br/>Příklad: `language=de`.|

<sup>*</sup> Vlastník by měl poskytnout `accessToken` s opatrností.

## <a name="embedding-public-content"></a>Vkládání veřejného obsahu

1. Přihlaste se k webu [video indexer](https://www.videoindexer.ai/) .
2. Vyberte video, se kterým chcete pracovat.
3. Vyberte tlačítko **Vložit** , které se zobrazí pod videem.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po výběru tlačítka pro **vložení** můžete vybrat widget, který chcete vložit do aplikace. 
4. Vyberte požadovaný typ widgetu (**rozpoznávání přehledů**, **přehrávač**nebo **Editor**).
 
5. Zkopírujte kód pro vložení a pak ho přidejte do aplikace. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Pokud máte problémy sdílející adresy URL vašich videí, přidejte do odkazu parametr `location`. Parametr by měl být nastaven na [oblasti Azure, ve kterých video indexer existuje](regions.md). Například: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Vložení soukromého obsahu

Chcete-li vložit soukromé video, je nutné předat token přístupu v atributu **Src** prvku IFRAME:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
K získání obsahu pomůcky pro rozpoznávání přehledů použijte jednu z následujících možností:<br/>
- Rozhraní API [pomůcky Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- [Přístupový token získat přístup k videu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Přidejte ho jako parametr dotazu na adresu URL. Zadejte tuto adresu URL jako hodnotu **Src** prvku IFRAME, jak je uvedeno výše.

Pokud chcete zajistit možnosti úprav v rámci vložené pomůcky, musíte předat přístupový token, který zahrnuje oprávnění k úpravám. Použijte [pomůcku Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) nebo [Získejte přístup k videu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) pomocí `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interakce widgetů

Pomůcka pro rozpoznávání přehledů může pracovat s videem ve vaší aplikaci. V této části si ukážeme, jak této interakce dosáhnout.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Chcete-li dostávat pomůcky Video Indexer ke komunikaci s dalšími komponentami, Video Indexer službu:

- Používá metodu HTML5 pro komunikaci mezi zdroji **PostMessage**. 
- Ověří původ zprávy z webu VideoIndexer.ai 

Pokud implementujete vlastní kód přehrávače a integrujete se widgety pro rozpoznávání poznatků, je vaší zodpovědností ověřit původ zprávy, která pochází z VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Vložení widgetů do aplikace nebo blogu (doporučeno) 

V této části se dozvíte, jak dosáhnout interakce mezi dvěma Video Indexer widgety, aby když uživatel vybere v aplikaci ovládací prvek Insight, hráč přejde na příslušný okamžik.

1. Zkopírujte kód pro vložení pomůcky přehrávače.
2. Zkopírujte kód pro vložení kódu pro rozpoznávání poznatků.
3. Přidejte [soubor. prostředníka](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) , který bude zpracovávat komunikaci mezi dvěma widgety:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Když teď uživatel vybere v aplikaci ovládací prvek Insight, přehrávač přejde na příslušný okamžik.

Další informace naleznete v tématu [video indexer – vložení ukázky widgetů](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu Cognitive Insights a přehrání obsahu pomocí Azure Media Playeru

V této části se dozvíte, jak dosáhnout interakce mezi pomůckou pro rozpoznávání a Azure Media Player instancí pomocí [modulu plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Přidejte modul plug-in Video Indexer pro AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Vytvořte instanci Azure Media Player s modulem plug-in Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
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

        // Init your AMP instance.
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
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Zkopírujte kód pro vložení kódu pro rozpoznávání poznatků.

Nyní byste měli být schopni komunikovat s Azure Media Player.

Další informace najdete v [ukázce Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Vložení pomůcky Video Indexer pro rozpoznávání poznatků a použití jiného přehrávače videa

Pokud používáte přehrávač videa, který není Azure Media Player, je nutné ručně manipulovat s přehrávačem videa, aby bylo možné komunikovat. 

1. Vložte přehrávač videa.

    Například standardní přehrávač HTML5:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Vložte widget Cognitive Insights.
3. Implementujte komunikaci pro přehrávač prostřednictvím naslouchání události „zpráva“. Příklad:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Další informace najdete v [ukázce Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Přidání titulků

Pokud vkládáte Video Indexer Insights s vlastní [Azure Media Player](https://aka.ms/azuremediaplayer), můžete k získání titulků (titulky) použít metodu **GetVttUrl** . Můžete také volat metodu jazyka JavaScript z Video Indexer modulu plug-in **GETSUBTITLESURL** amp (jak je uvedeno výše). 

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení vložitelných widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Můžete si vybrat typy přehledů, které chcete. Chcete-li to provést, zadejte je jako hodnotu pro následující parametr adresy URL, který je přidán do kódu pro vložení, který získáte (z rozhraní API nebo z webové aplikace): `&widgets=<list of wanted widgets>`.

Možné hodnoty jsou: **lidé**, **klíčová slova**, **zabarvení**, **přepis**a **Search**.

Například pokud chcete vložit pomůcku obsahující pouze lidi a vyhledávání informací, adresa URL pro vložení IFRAME bude vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Název okna iFrame lze také přizpůsobit poskytnutím `&title=<YourTitle>` URL prvku IFRAME. (Přizpůsobuje > název \<HTML).
    
Například pokud chcete vašemu oknu IFRAME dát název "MyInsights", adresa URL bude vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Všimněte si, že je tato možnost relevantní jenom v případech, když přehledy potřebujete otevřít v novém okně.

### <a name="player-widget"></a>Widget Player

Pokud vložíte přehrávač Video Indexeru, můžete zvolit velikost přehrávače zadáním velikosti prvku iframe.

Příklad:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Ve výchozím nastavení používá Video Indexer Player automaticky generované skryté titulky založené na přepisu videa. Přepis se extrahuje z videa se zdrojovým jazykem, který jste vybrali při nahrávání videa.

Pokud chcete vkládat v jiném jazyce, můžete přidat `&captions=< Language | "all" | "false" >` do adresy URL přehrávače pro vložení. Pokud chcete titulky ve všech dostupných jazycích titulky, použijte hodnotu `all`. Pokud chcete, aby se popisky zobrazovaly ve výchozím nastavení, můžete předat `&showCaptions=true`.

Adresa URL pro vložení bude vypadat takto: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Pokud chcete popisky zakázat, můžete hodnotu parametru `captions` předat jako `false`.

#### <a name="autoplay"></a>AutoPlay
Ve výchozím nastavení se přehrávač začne přehrávat ve videu. Můžete se rozhodnout, že nechcete předat `&autoplay=false` do předchozí adresy URL pro vložení.

## <a name="code-samples"></a>Ukázky kódu

Podívejte se na úložiště [ukázek kódu](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) , které obsahuje ukázky pro video indexer rozhraní API a widgety:

| Soubor nebo složka                       | Popis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Načíst video indexerer ve vlastním Azure Media Player                        |
| `azure-media-player-vi-insights`  | Vložení VI Insights s vlastní Azure Media Player                             |
| `control-vi-embedded-player`      | Vložit VI přehrávač a ovládat ho z vnějšku                                    |
| `custom-index-location`           | Vložení VI Insights z vlastního externího umístění (může být zákazníkem objekt BLOB)     |
| `embed-both-insights`             | Základní využití VI Insights na základě perspektivy i Insights                            |
| `embed-insights-with-AMP`         | Vložení widgetu VI Insights s vlastním Azure Media Player                      |
| `customize-the-widgets`           | Vložení VI widgetů s přizpůsobenými možnostmi                                     |
| `embed-both-widgets`              | Vkládání VI přehrávačů a přehledů a komunikace mezi nimi                      |
| `url-generator`                   | Vygeneruje vlastní adresu URL pro vložení widgetů na základě možností zadaných uživatelem.             |
| `html5-player`                    | Vložení VI Insights s výchozím přehrávačem videí HTML5                            |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zobrazit a upravit Video Indexer Insights, najdete v tématu [zobrazení a úprava video Indexerch přehledů](video-indexer-view-edit.md).

Podívejte se také na [video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
