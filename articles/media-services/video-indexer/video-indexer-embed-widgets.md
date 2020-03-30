---
title: Vložení widgetů Video Indexer u svých aplikací
titleSuffix: Azure Media Services
description: Přečtěte si, jak do aplikací vložit widgety Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 4d92bd3709c5f56db0095ca1be2caa0e9c78b42f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336817"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Vložení widgetů Video Indexer u svých aplikací

Tento článek ukazuje, jak můžete do aplikací vložit widgety Video Indexer. Video Indexer podporuje vkládání tří typů widgetů do vašich aplikací: *Cognitive Insights*, *Player*a *Editor*.

Počínaje verzí 2 obsahuje základní adresa URL widgetu oblast zadaného účtu. Například účet v oblasti Západní USA `https://wus2.videoindexer.ai/embed/insights/...`generuje: .

## <a name="widget-types"></a>Typy widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Widget Cognitive Insights zahrnuje všechny vizuální přehledy, které se extrahovaly z indexování videa. Widget Cognitive Insights podporuje následující volitelné parametry adresy URL:

|Name (Název)|Definice|Popis|
|---|---|---|
|`widgets` | Řetězce oddělené čárkami | Umožňuje řídit přehledy, které chcete vykreslit.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` Vykreslí pouze přehledy ui pro lidi a klíčová slova.<br/>Dostupné možnosti: lidé, animovanéznaky, klíčová slova, popisky, pocity, emoce, témata, klíčové snímky, přepis, ocr, reproduktory, scény a pojmenované entity.|
|`controls`|Řetězce oddělené čárkami|Umožňuje řídit ovládací prvky, které chcete vykreslit.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` vykreslí pouze možnost vyhledávání a tlačítko stáhnout.<br/>Dostupné možnosti: vyhledávání, stahování, předvolby, jazyk.|
|`language`|Krátký kód jazyka (název jazyka)|Řídí jazyk přehledů.<br/>Příklad: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Nebo`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Krátký kód jazyka | Řídí jazyk ui. Výchozí hodnota je `en`. <br/>Příklad: `locale=de`.|
|`tab` | Výchozí vybraná karta | Řídí kartu **Přehledy,** která je ve výchozím nastavení vykreslena. <br/>Příklad: `tab=timeline` Vykreslí přehledy s vybranou kartou **Časová osa.**|

### <a name="player-widget"></a>Widget Player

Pomocí widgetu Přehrávač můžete streamovat video pomocí adaptivní přenosové rychlosti. Widget Přehrávač podporuje následující volitelné parametry adresy URL.

|Name (Název)|Definice|Popis|
|---|---|---|
|`t` | Sekundy od začátku | Způsobí, že hráč začne hrát od zadaného časového bodu.<br/> Příklad: `t=60`. |
|`captions` | Kód jazyka | Načte titulek v zadaném jazyce během načítání widgetu, aby byl k dispozici v nabídce **Titulky.**<br/> Příklad: `captions=en-US`. |
|`showCaptions` | Logická hodnota | Přehrávač se načte s už povolenými titulky.<br/> Příklad: `showCaptions=true`. |
|`type`| | Aktivuje vzhled zvukového přehrávače (část videa je odstraněna).<br/> Příklad: `type=audio`. |
|`autoplay` | Logická hodnota | Označuje, zda by měl přehrávač při načtení začít přehrávat video. Výchozí hodnota je `true`.<br/> Příklad: `autoplay=false`. |
|`language` | Kód jazyka | Ovládá jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`.|

### <a name="editor-widget"></a>Editor widget

Pomocí widgetu Editor můžete vytvářet nové projekty a spravovat přehledy videa. Widget Editor podporuje následující volitelné parametry adresy URL.

|Name (Název)|Definice|Popis|
|---|---|---|
|`accessToken`<sup>*</sup> | Řetězec | Poskytuje přístup k videím, která jsou pouze v účtu, který se používá k vložení widgetu.<br> Widget Editor vyžaduje `accessToken` parametr. |
|`language` | Kód jazyka | Ovládá jazyk přehrávače. Výchozí hodnota je `en-US`.<br/>Příklad: `language=de-DE`. |
|`locale` | Krátký kód jazyka | Řídí jazyk přehledů. Výchozí hodnota je `en`.<br/>Příklad: `language=de`. |

<sup>*</sup>Majitel by `accessToken` měl být opatrný.

## <a name="embedding-public-content"></a>Vkládání veřejného obsahu

1. Přihlaste se na web [Video Indexer.](https://www.videoindexer.ai/)
2. Vyberte video, se kterým chcete pracovat.
3. Vyberte tlačítko **Vložit,** které se zobrazí pod videem.

    ![Tlačítko Vložit do video indexeru](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po výběru tlačítka **Vložit** můžete vybrat widget, který chcete vložit do aplikace.
4. Vyberte požadovaný typ widgetu **(Cognitive Insights**, **Player**nebo **Editor).**
 
5. Zkopírujte kód pro vložení a přidejte ho do aplikace.

    ![Kód pro vložení aplikace – Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Pokud máte problémy se sdílením adres `location` URL videa, přidejte parametr k odkazu. Parametr by měl být nastaven na [oblasti Azure, ve kterých existuje Video Indexer](regions.md). Například: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Vložení soukromého obsahu

Chcete-li vložit soukromé video, musíte předat `src` přístupový token v atributu iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Chcete-li získat obsah widgetu Cognitive Insights, použijte jednu z následujících metod:

- [Rozhraní API widgetu Získat přehledy.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- [Získat token přístupu k videu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Přidejte jej jako parametr dotazu do adresy URL. Zadejte tuto `src` adresu URL jako hodnotu prvku iframe, jak je znázorněno výše.

Chcete-li ve vloženém widgetu poskytnout možnosti úprav přehledů, musíte předat přístupový token, který zahrnuje oprávnění k úpravám. Použijte [widget Získat přehledy](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) nebo získat token [přístupu k videu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) s . `&allowEdit=true`

## <a name="widgets-interaction"></a>Interakce widgetů

Widget Cognitive Insights může pracovat s videem ve vaší aplikaci. V této části si ukážeme, jak této interakce dosáhnout.

![Cognitive Insights widget Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikace mezi zdroji

Chcete-li získat widgety Video Indexer pro komunikaci s jinými součástmi, služba Video Indexer:

- Používá metodu `postMessage`html5 komunikace mezi počátky .
- Ověří původ zprávy z webu VideoIndexer.ai

Pokud implementujete vlastní kód přehrávače a integrujete se s widgety Cognitive Insights, je vaší odpovědností ověřit původ zprávy, která pochází z VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Vložení widgetů do aplikace nebo blogu (doporučeno)

Tato část ukazuje, jak dosáhnout interakce mezi dvěma widgety Video Indexer, takže když uživatel vybere ovládací prvek insight ve vaší aplikaci, přehrávač přeskočí na příslušný okamžik.

1. Zkopírujte kód pro vložení widgetu Player.
2. Zkopírujte kód pro vložení widgetu Cognitive Insights.
3. Přidejte [zprostředkující soubor](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ke zpracování komunikace mezi oběma widgety:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Nyní, když uživatel vybere ovládací prvek insight na vaší aplikaci, hráč přeskočí na příslušný okamžik.

Další informace naleznete v ukázce [Video Indexer – Vložení obou widgetů](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Vložení widgetu Cognitive Insights a přehrání obsahu pomocí Azure Media Playeru

Tato část ukazuje, jak dosáhnout interakce mezi widgetem Cognitive Insights a instancí Přehrávače médií Azure pomocí [modulu plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Přidejte modul plug-in Video Indexer pro přehrávač AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Vytvořte instanci programu Azure Media Player pomocí modulu plug-in Video Indexer.

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

3. Zkopírujte kód pro vložení widgetu Cognitive Insights.

Nyní můžete komunikovat s Azure Media Player.

Další informace najdete v [ukázce azure media player + vi insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Vložení widgetu Video Indexer Cognitive Insights a použití jiného přehrávače videa

Pokud používáte jiný přehrávač videa než Azure Media Player, musíte ručně manipulovat s přehrávačem videa, abyste dosáhli komunikace.

1. Vložte přehrávač videa.

    Například standardní přehrávač HTML5:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Vložte widget Cognitive Insights.
3. Implementujte komunikaci pro přehrávač prostřednictvím naslouchání události „zpráva“. Například:

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

Další informace najdete v [ukázce azure media player + vi insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Přidání titulků

Pokud vložíte přehledy video indexeru s vlastním [přehrávačem médií Azure](https://aka.ms/azuremediaplayer), můžete pomocí `GetVttUrl` této metody získat skryté titulky (titulky). Můžete také volat metodu JavaScript z modulu `getSubtitlesUrl` plug-in AMP Video Indexer (jak je znázorněno výše).

## <a name="customizing-embeddable-widgets"></a>Přizpůsobení vložitelných widgetů

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Můžete zvolit typy přehledů, které chcete. Chcete-li to provést, zadejte je jako hodnotu pro následující parametr URL, který je přidán do `&widgets=<list of wanted widgets>`kódu pro vložení, který získáte (z rozhraní API nebo z webové aplikace): .

Možné hodnoty jsou: **lidé**, **klíčová slova**, **sentiment ,** **přepis**a **vyhledávání**.

Pokud například chcete vložit widget, který obsahuje pouze lidi a vyhledat přehledy, bude adresa URL pro vložení prvku iframe vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Název okna iframe lze také přizpůsobit poskytnutím `&title=<YourTitle>` adresy URL prvku iframe. (Přizpůsobí název \<HTML> hodnotu).

Chcete-li například dát oknu iframe název "MyInsights", bude adresa URL vypadat takto:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Všimněte si, že je tato možnost relevantní jenom v případech, když přehledy potřebujete otevřít v novém okně.

### <a name="player-widget"></a>Widget Player

Pokud vložíte přehrávač Video Indexeru, můžete zvolit velikost přehrávače zadáním velikosti prvku iframe.

Například:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Ve výchozím nastavení přehrávač Video Indexer automaticky vygeneroval skryté titulky, které jsou založeny na přepisu videa. Přepis je extrahován z videa se zdrojovým jazykem, který byl vybrán při nahrávání videa.

Pokud chcete vložit s jiným jazykem, `&captions=< Language | "all" | "false" >` můžete přidat do adresy URL pro vložení přehrávače. Pokud chcete titulky ve všech dostupných `all`jazycích, použijte hodnotu . Pokud chcete, aby se titulky zobrazovaly ve `&showCaptions=true`výchozím nastavení, můžete je předat .

Adresa URL pro vložení pak bude vypadat takto:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Chcete-li zakázat titulky, můžete `captions` předat `false`hodnotu parametru jako .

#### <a name="autoplay"></a>Automatické přehrávání
Ve výchozím nastavení začne přehrávač video přehrávat. můžete se rozhodnout, `&autoplay=false` že tak nechcete, přejdete na předchozí adresu URL pro vložení.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na [ukázky kódu](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) úložiště, které obsahuje ukázky pro rozhraní API a widgety video indexeru:

| Soubor/složka                       | Popis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Načtěte video indexer videa do vlastního programu Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Vi Insights můžete vložit pomocí vlastního přehrávače médií Azure.                             |
| `control-vi-embedded-player`      | Vem přehrávač VI a ovládejte jej zvenčí.                                    |
| `custom-index-location`           | Vložení Přehledů VI z vlastního externího umístění (může být objekt blob zákazníkem).     |
| `embed-both-insights`             | Základní využití VI Insights jak hráče, tak přehledů.                            |
| `embed-insights-with-AMP`         | Vložte widget VI Insights pomocí vlastního přehrávače médií Azure.                      |
| `customize-the-widgets`           | Vložit widgety VI s přizpůsobenými možnostmi.                                     |
| `embed-both-widgets`              | Vložte přehrávač VI a přehledy a komunikujte mezi nimi.                      |
| `url-generator`                   | Generuje vlastní adresu URL pro vložení widgetů na základě možností určených uživatelem.             |
| `html5-player`                    | Vložte VI Insights do výchozího přehrávače videa HTML5.                           |

## <a name="next-steps"></a>Další kroky

Informace o zobrazení a úpravě přehledů videoindexeru naleznete v tématu [Zobrazení a úpravy přehledů indexeru videa](video-indexer-view-edit.md).

Také, podívejte se na [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
