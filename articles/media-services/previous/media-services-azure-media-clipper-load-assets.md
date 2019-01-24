---
title: Načtení prostředků do Azure Media Clipperu | Dokumentace Microsoftu
description: Kroky pro načtení prostředků do Azure Media Clipperu
services: media-services
keywords: Galerie, dílčí klip, kódování, médií
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 415f58d9c6880bc7ef9ba6b6ba3f575fae382f04
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806879"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Načítání prostředků do Azure Media Clipperu
Prostředky je možné načíst do Azure Media Clipperem pomocí dvou metod:
1. Staticky předávání v knihovně prostředků
2. Dynamické generování seznamu sad prostředky přes rozhraní API

## <a name="statically-load-videos-into-clipper"></a>Staticky načtení videa do Clipperem
Staticky načtení videa do Clipperem povolit koncovým uživatelům vytvářet klipy nevybírejte videa z panel výběru prostředku.

V takovém případě předáte statickou sadu prostředků Clipperem. Každý prostředek zahrnuje AMS prostředek/filtru ID, název, publikovanou adresu URL streamování. Pokud je k dispozici, tokenu ověřování ochrana obsahu nebo pole thumbnail adresy URL možné předat v. Pokud předaná, naplní se do rozhraní pro miniatury. Ve scénářích, ve kterém prostředek knihovny statických a malé můžete předat ve smlouvě prostředku pro každý prostředek v knihovně.

> [!NOTE]
> Při načítání staticky prostředky do Clipperem, se přidají prostředky **přímo na časové ose** a **asset podokně nevykreslí**. První prostředek se přidá na časovou osu a zbývající prostředky jsou uspořádány na pravé straně časovou osu).

Chcete-li načíst knihovnu statických prostředků, použijte **načíst** metody a zajistěte tak předání JSON s reprezentací provedených každého prostředku. Následující příklad kódu ukazuje reprezentaci JSON pro jeden prostředek.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Doporučujeme řetěz volání metody load() ready(handler) metodou, jak je znázorněno v předchozím příkladu. V předchozím příkladu zaručuje, že widgetu připravený před načtením prostředky.

> [!NOTE]
> Pro miniaturu adresy URL fungovat podle očekávání na časové ose Clipperem musí být rovnoměrně rozdělené mezi video (založené na době trvání) a v chronologickém pořadí v poli. Můžete použít následující přednastavených fragmentu kódu JSON jako ukázkový odkaz pro generování bitových kopií procesor "Media Encoder Standard":

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Dynamické načtení videa v Clipperem
Dynamicky načtěte videa do Clipperem povolit koncovým uživatelům výběr videa z panel výběru prostředku do Galerie proti.

Alternativně můžete načíst prostředky dynamicky prostřednictvím zpětné volání. Ve scénářích, kde jsou prostředky dynamicky generované nebo knihovny je velká by měla načíst prostřednictvím zpětného volání. Dynamicky načíst prostředek, je nutné implementovat onLoadAssets volitelné funkce zpětného volání. Tato funkce je předána do Clipperem při inicializaci. Vyřešené prostředky by měly dodržovat stejný kontrakt jako staticky načtená prostředky. Následující příklad kódu ukazuje podpis metody, očekávaný vstup a očekávaný výstup.

> [!NOTE]
> Při načítání dynamické prostředky do Clipperem, prostředky se zobrazují v **panel výběru asset**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```