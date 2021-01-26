---
title: Vložení Video Indexer widgetů do aplikací
titleSuffix: Azure Media Services
description: Naučte se vkládat Video Indexer widgety do svých aplikací.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797774"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Vložení Video Indexer widgetů do aplikací

V tomto článku se dozvíte, jak můžete ve svých aplikacích vkládat Video Indexer widgety. Video Indexer podporuje vkládání tří typů widgetů do vašich aplikací: *Cognitive Insights*, *Player* a *Editor*.

Počínaje verzí 2 se základní adresa URL widgetu skládá z oblasti zadaného účtu. Například účet v oblasti Západní USA generuje: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Typy widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Widget Cognitive Insights zahrnuje všechny vizuální přehledy, které se extrahovaly z indexování videa. Pomůcka pro rozpoznávání poznatků podporuje následující volitelné parametry adresy URL:

|Name|Definice|Popis|
|---|---|---|
|`widgets` | Řetězce oddělené čárkami | Umožňuje řídit přehledy, které chcete vykreslit.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` vykreslí jenom lidé a klíčová slova UI Insights.<br/>Dostupné možnosti: lidé, animatedCharacters, klíčová slova, popisky, zabarvení, emoce, témata, klíčové snímky, přepisy, optické rozpoznávání znaků, reproduktory, scény a namedEntities.|
|`controls`|Řetězce oddělené čárkami|Umožňuje řídit ovládací prvky, které chcete vykreslit.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` vykreslí jenom možnost hledání a tlačítko Stáhnout.<br/>Dostupné možnosti: Hledat, stáhnout, předvolby, jazyk.|
|`language`|Krátký kód jazyka (název jazyka)|Určuje jazyk Insights.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ani `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Krátký kód jazyka | Řídí jazyk uživatelského rozhraní. Výchozí hodnota je `en`. <br/>Příklad: `locale=de`.|
|`tab` | Výchozí vybraná karta | Řídí kartu **přehledy** , která je ve výchozím nastavení vykreslená. <br/>Příklad: `tab=timeline` vykreslí přehledy pomocí vybrané karty **Časová osa** .|
|`location` ||`location`Parametr musí být součástí vložených odkazů, viz [Jak získat název vaší oblasti](regions.md). Pokud je váš účet ve verzi Preview, `trial` měl by se použít pro hodnotu umístění. `trial` je výchozí hodnota pro `location` parametr.| 

### <a name="player-widget"></a>Widget Player

Pomůcku přehrávače můžete použít ke streamování videa pomocí adaptivní přenosové rychlosti. Widget přehrávače podporuje následující volitelné parametry adresy URL.

|Name|Definice|Popis|
|---|---|---|
|`t` | Sekund od začátku | Nastaví, aby se hráč od zadaného časového bodu začal přehrávat.<br/> Příklad: `t=60`. |
|`captions` | Kód jazyka | Načte titulek v zadaném jazyce během načítání pomůcky, aby byl dostupný v nabídce **titulků** .<br/> Příklad: `captions=en-US`. |
|`showCaptions` | Logická hodnota | Přehrávač se načte s už povolenými titulky.<br/> Příklad: `showCaptions=true`. |
|`type`| | Aktivuje vzhled zvukového přehrávače (součást videa se odebrala).<br/> Příklad: `type=audio`. |
|`autoplay` | Logická hodnota | Určuje, zda by měl přehrávač při načtení videa začít přehrávat video. Výchozí hodnota je `true`.<br/> Příklad: `autoplay=false`. |
|`language`/`locale` | Kód jazyka | Řídí jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`.|
|`location` ||`location`Parametr musí být součástí vložených odkazů, viz [Jak získat název vaší oblasti](regions.md). Pokud je váš účet ve verzi Preview, `trial` měl by se použít pro hodnotu umístění. `trial` je výchozí hodnota pro `location` parametr.| 

### <a name="editor-widget"></a>Widget pro Editor

Pomůcku editoru můžete použít k vytvoření nových projektů a správě přehledů videa. Pomůcka editoru podporuje následující volitelné parametry adresy URL.

|Name|Definice|Popis|
|---|---|---|
|`accessToken`<sup>*</sup> | Řetězec | Poskytuje přístup k videím, která jsou pouze v účtu, který se používá k vložení widgetu.<br> Widget pro Editor vyžaduje `accessToken` parametr. |
|`language` | Kód jazyka | Řídí jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`. |
|`locale` | Krátký kód jazyka | Řídí jazyk Insights. Výchozí hodnota je `en`.<br/>Příklad: `language=de`. |
|`location` ||`location`Parametr musí být součástí vložených odkazů, viz [Jak získat název vaší oblasti](regions.md). Pokud je váš účet ve verzi Preview, `trial` měl by se použít pro hodnotu umístění. `trial` je výchozí hodnota pro `location` parametr.| 

<sup>*</sup>Vlastník by měl poskytovat `accessToken` upozornění.

## <a name="embedding-videos"></a>Vkládání videí

Tato část popisuje vložení veřejného a soukromého obsahu do aplikací.

`location`Parametr musí být součástí vložených odkazů, viz [Jak získat název vaší oblasti](regions.md). Pokud je váš účet ve verzi Preview, `trial` měl by se použít pro hodnotu umístění. `trial` je výchozí hodnota pro `location` parametr. Příklad: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> Sdílení odkazu pro widget **Playeru** nebo **Insights** bude mít přístupový token a udělí vám oprávnění jen pro čtení k vašemu účtu.

### <a name="public-content"></a>Veřejný obsah

1. Přihlaste se k webu [video indexer](https://www.videoindexer.ai/) .
1. Vyberte video, se kterým chcete pracovat, a stiskněte tlačítko **Přehrát**.
1. Vyberte požadovaný typ widgetu (**rozpoznávání přehledů**, **přehrávač** nebo **Editor**).
1. Klikněte na **&lt; / &gt; Vložit**.
5. Zkopírujte kód pro vložení (zobrazí se ve **zkopírování vloženého kódu** do dialogového okna **& pro vložení sdílené složky** ).
6. Přidejte kód do aplikace.

### <a name="private-content"></a>Soukromý obsah

Chcete-li vložit soukromé video, je nutné předat token přístupu v `src` atributu prvku IFRAME:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Pokud chcete získat obsah widgetu pro rozpoznávání přehledů, použijte jednu z následujících metod:

- Rozhraní API [pomůcky Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- [Přístupový token získat přístup k videu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Přidejte ho jako parametr dotazu na adresu URL. Zadejte tuto adresu URL jako `src` hodnotu prvku IFRAME, jak je uvedeno výše.

Pokud chcete zajistit možnosti úprav v rámci vložené pomůcky, musíte předat přístupový token, který zahrnuje oprávnění k úpravám. Použijte k [widgetu Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) nebo [k získání přístupového tokenu videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interakce widgetů

Pomůcka pro rozpoznávání přehledů může pracovat s videem ve vaší aplikaci. V této části si ukážeme, jak této interakce dosáhnout.

![Video Indexer pomůcky pro rozpoznávání přehledů](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Přehled toku

Při úpravách přepisu dojde k následujícímu toku:

1. Přepis upravíte na časové ose.
1. Video Indexer získá tyto aktualizace a uloží je v [úpravách přepisu](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) v jazykovém modelu.
1. Popisky jsou aktualizované:

    * Pokud používáte widget přehrávače Video Indexer – automaticky se aktualizuje.
    * Pokud používáte externí přehrávač – zobrazí se nový soubor s titulky. uživatel **získá volání titulků videa** .

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Chcete-li dostávat pomůcky Video Indexer ke komunikaci s dalšími komponentami, Video Indexer službu:

- Používá metodu HTML5 komunikace mezi zdroji `postMessage` .
- Ověří původ zprávy z webu VideoIndexer.ai

Pokud implementujete vlastní kód přehrávače a integrujete se widgety pro rozpoznávání poznatků, je vaší zodpovědností ověřit původ zprávy, která pochází z VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Vložení widgetů do aplikace nebo blogu (doporučeno)

V této části se dozvíte, jak dosáhnout interakce mezi dvěma Video Indexer widgety, aby když uživatel vybere v aplikaci ovládací prvek Insight, hráč přejde na příslušný okamžik.

1. Zkopírujte kód pro vložení widgetu Player.
2. Zkopírujte kód pro vložení widgetu Cognitive Insights.
3. Přidejte [zprostředkující soubor](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ke zpracování komunikace mezi oběma widgety:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Když teď uživatel vybere v aplikaci ovládací prvek Insight, přehrávač přejde na příslušný okamžik.

Další informace naleznete v tématu [video indexer – vložení ukázky widgetů](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu Cognitive Insights a přehrání obsahu pomocí Azure Media Playeru

V této části se dozvíte, jak dosáhnout interakce mezi pomůckou pro rozpoznávání a Azure Media Player instancí pomocí [modulu plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Přidejte modul plug-in Video Indexer pro AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Vytvořte instanci Azure Media Player s modulem plug-in Video Indexer.

    ```javascript
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
    ```

3. Zkopírujte kód pro vložení widgetu Cognitive Insights.

Nyní můžete komunikovat s Azure Media Player.

Další informace najdete v [ukázce Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Vložení pomůcky Video Indexer pro rozpoznávání poznatků a použití jiného přehrávače videa

Pokud používáte přehrávač videa, který není Azure Media Player, je nutné ručně manipulovat s přehrávačem videa, aby bylo možné komunikovat.

1. Vložte přehrávač videa.

    Například standardní přehrávač HTML5:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Vložte widget Cognitive Insights.
3. Implementujte komunikaci pro přehrávač prostřednictvím naslouchání události „zpráva“. Například:

    ```javascript
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
    ```

Další informace najdete v [ukázce Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Přidání titulků

Pokud vkládáte Video Indexer Insights s vlastní [Azure Media Player](https://aka.ms/azuremediaplayer), můžete k získání titulků `GetVttUrl` (titulky) použít metodu. Můžete také volat metodu jazyka JavaScript z modulu plug-in Video Indexer AMP `getSubtitlesUrl` (jak je uvedeno výše).

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení vložitelných widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Můžete si vybrat typy přehledů, které chcete. Chcete-li to provést, zadejte je jako hodnotu pro následující parametr adresy URL, který je přidán do kódu pro vložení, který získáte (z rozhraní API nebo z webové aplikace): `&widgets=<list of wanted widgets>` .

Možné hodnoty jsou: `people` , `animatedCharacters` , `keywords` , `labels` , `sentiments` , `emotions` , `topics` , `keyframes` , `transcript` , `ocr` , `speakers` , `scenes` a `namedEntities` .

Například pokud chcete vložit pomůcku, která obsahuje jenom informace o lidech a klíčových slovech, bude adresa URL pro vložení IFRAME vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Název okna iFrame lze také přizpůsobit poskytnutím `&title=<YourTitle>` adresy URL prvku IFRAME. (Upravuje hodnotu jazyka HTML `<title>` ).
   
Například pokud chcete vašemu oknu IFRAME dát název "MyInsights", adresa URL bude vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Všimněte si, že je tato možnost relevantní jenom v případech, když přehledy potřebujete otevřít v novém okně.

### <a name="player-widget"></a>Widget Player

Pokud vložíte přehrávač Video Indexeru, můžete zvolit velikost přehrávače zadáním velikosti prvku iframe.

Například:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Ve výchozím nastavení používá Video Indexer Player automaticky generované skryté titulky založené na přepisu videa. Přepis se extrahuje z videa se zdrojovým jazykem, který jste vybrali při nahrávání videa.

Pokud chcete vložit jiný jazyk, můžete přidat `&captions=<Language Code>` na adresu URL přehrávače pro vložení. Pokud chcete, aby se popisky zobrazovaly ve výchozím nastavení, můžete předat &showCaptions = true.

Adresa URL pro vložení bude vypadat takto:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>AutoPlay

Ve výchozím nastavení se přehrávač začne přehrávat ve videu. Můžete se rozhodnout, že nechcete předat `&autoplay=false` předchozí adrese URL pro vložení.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na úložiště [ukázek kódu](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) , které obsahuje ukázky pro video indexer rozhraní API a widgety:

| Soubor nebo složka                       | Popis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Načíst video indexerer ve vlastním Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Vložte VI přehledů s vlastní Azure Media Player.                             |
| `control-vi-embedded-player`      | Vložte VI přehrávač a ovládat ho z vnějšku.                                    |
| `custom-index-location`           | Vložte VI Insights z vlastního externího umístění (může být zákazníkem objekt BLOB).     |
| `embed-both-insights`             | Základní využití pro VI přehledy pro systém Windows Player i Insights                            |
| `embed-insights-with-AMP`         | Vložte pomůcku VI Insights s vlastní Azure Media Player.                      |
| `customize-the-widgets`           | Vložte VI widgety s přizpůsobenými možnostmi.                                     |
| `embed-both-widgets`              | Vložte VI Player a přehledy a mezi nimi komunikujte.                      |
| `url-generator`                   | Vygeneruje vlastní adresu URL pro vložení widgetů na základě možností zadaných uživatelem.             |
| `html5-player`                    | Vložte VI přehledů s výchozím přehrávačem videí HTML5.                           |

## <a name="supported-browsers"></a>Podporované prohlížeče

Další informace najdete v tématu [podporované prohlížeče](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zobrazit a upravit Video Indexer Insights, najdete v tématu [zobrazení a úprava video Indexerch přehledů](video-indexer-view-edit.md).

Podívejte se také na [video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
