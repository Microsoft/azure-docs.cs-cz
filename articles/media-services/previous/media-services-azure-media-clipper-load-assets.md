---
title: Načtení prostředků do Azure Media Clipperu | Microsoft Docs
description: Postup načtení prostředků do Azure Media Clipperu
services: media-services
keywords: Clip; dílčí klip; Encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685041"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Načítají se prostředky do Azure Media Clipperu.  

Prostředky se dají načíst do Azure Media Clipperu dvěma způsoby:
1. Staticky procházející knihovnou prostředků
2. Dynamické generování seznamu prostředků prostřednictvím rozhraní API

## <a name="statically-load-videos-into-clipper"></a>Staticky načíst videa do Clipperu
Staticky načtěte videa do Clipperu, aby koncoví uživatelé mohli vytvářet klipy bez výběru videí z panelu Výběr assetu.

V tomto případě předáte do Clipperu statickou sadu prostředků. Každý Asset zahrnuje ID prostředku/filtru AMS, název, publikovanou adresu URL streamování. V případě potřeby může být předán token pro ověření obsahu ochrany obsahu nebo pole miniatur URL. Pokud je předáno v, miniatury se naplní do rozhraní. Ve scénářích, kde je knihovna prostředků statická a malá, můžete předat kontrakt assetu pro každý Asset v knihovně.

> [!NOTE]
> Při statickém načítání prostředků do Clipperu se prostředky přidávají **přímo na časovou osu** a **podokno assetů se nevykresluje**. První Asset se přidá na časovou osu a zbytek prostředků se nakryje na pravé straně časové osy.

Chcete-li načíst statickou knihovnu prostředků, použijte metodu **Load** k předání v reprezentaci JSON jednotlivých assetů. Následující ukázka kódu ilustruje reprezentaci JSON pro jeden Asset.

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
> Je vhodné zřetězit volání metody Load () s připraveným (obslužným) metodou, jak je znázorněno v předchozím příkladu. Předchozí příklad zaručuje, že pomůcka je připravena před načtením assetů.

> [!NOTE]
> Aby adresy URL miniatur fungovaly podle očekávání v časové ose Clipperu, musí být rovnoměrně rozloženy přes video (na základě doby trvání) a v chronologickém pořadí v rámci pole. Jako vzorový odkaz pro generování imagí s procesorem Media Encoder Standard můžete použít následující fragment kódu JSON:

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

## <a name="dynamically-load-videos-in-clipper"></a>Dynamické načítání videí v Clipperu
Umožňuje dynamicky načíst videa do Clipperu a umožnit tak koncovým uživatelům vybrat videa na panelu výběru assetu, aby bylo možné je vystřihnout.

Alternativně můžete načíst prostředky dynamicky prostřednictvím zpětného volání. Ve scénářích, kdy jsou prostředky dynamicky generovány nebo je knihovna rozsáhlá, je nutné načíst prostřednictvím zpětného volání. K dynamickému načtení prostředků je nutné implementovat funkci onLoadAssets zpětného volání. Tato funkce je předána do Clipperu při inicializaci. Vyřešené prostředky by měly odpovídat stejné smlouvě jako staticky načtené prostředky. Následující ukázka kódu ukazuje signaturu metody, očekávaný vstup a očekávaný výstup.

> [!NOTE]
> Při dynamickém načítání prostředků do Clipperu se assety vykreslují na **panelu výběru assetu**.

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
