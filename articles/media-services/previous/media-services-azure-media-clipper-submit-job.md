---
title: Odeslat ořezové úlohy Azure Media Clipperu | Microsoft Docs
description: Postup odeslání ořezových úloh z Azure Media Clipperu
services: media-services
keywords: Clip; dílčí klip; Encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684978"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Odeslání ořezových úloh z Azure Media Clipperu 

Azure Media Clipper vyžaduje, aby byla pro zpracování odesílání úlohy oříznutí implementována metoda **submitSubclipCallback** . Tato funkce je určena pro implementaci HTTP POST výstupu z Clipperu do webové služby. Tato webová služba je místo, kde můžete odeslat úlohu kódování. Výstupem Clipperu je Media Encoder Standard předvolby kódování pro vykreslené úlohy nebo datová část REST API pro volání filtru dynamického manifestu. Tento model předávacího modelu je nezbytný, protože pověření účtu Media Services nejsou v prohlížeči klienta zabezpečená.

Následující sekvenční diagram znázorňuje pracovní postup mezi klientem prohlížeče, webovou službou a Azure Media Services: ![sekvenčním diagramem Azure Media Clipperu](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

V předchozím diagramu jsou čtyři entity: prohlížeč koncového uživatele, Webová služba, koncový bod CDN hostující prostředky Clipperu a Azure Media Services. Když koncový uživatel přejde na vaši webovou stránku, stránka získá prostředky JavaScript a CSS pro Clipper z hostitelského koncového bodu CDN. Koncový uživatel nakonfiguruje z prohlížeče úlohu oříznutí nebo volání vytváření filtru dynamického manifestu. Když koncový uživatel odešle požadavek na vytvoření úlohy nebo filtru, uloží datovou část úlohy do webové služby, kterou musíte nasadit. Tato webová služba nakonec odešle úlohu oříznutí nebo volání vytvoření filtru k Azure Media Services pomocí přihlašovacích údajů k účtu Media Services.

Následující ukázka kódu ukazuje ukázkovou metodu **submitSubclipCallback** . V této metodě implementujete příspěvek HTTP Media Encoder Standard předvolby kódování. Pokud byl příspěvek úspěšný (**výsledek**), **příslib** se vyřeší, jinak se zamítl s podrobnostmi o chybě.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
Výstupem odeslání úlohy je buď přednastavení kódování Media Encoder Standard pro vykreslenou úlohu nebo datová část REST API pro dynamické filtry manifestu.

## <a name="submitting-encoding-job-to-create-video"></a>Odesílá se úloha kódování pro vytvoření videa.
Chcete-li vytvořit videoklip s přesným snímkem, můžete odeslat úlohu kódování Media Encoder Standard. Úloha kódování vytváří vykreslená videa, což je nový fragmentovaný soubor MP4.

Výstupní kontrakt úlohy pro vykreslený výstřižek je objekt JSON s následujícími vlastnostmi:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Chcete-li provést úlohu kódování, odešlete úlohu kódování Media Encoder Standard s přidruženou předvolbu. Podrobnosti o odesílání úloh kódování pomocí [sady .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) nebo [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset)najdete v tomto článku.

## <a name="quickly-creating-video-clips-without-encoding"></a>Rychlé vytváření videoklipů bez kódování
Alternativa k vytvoření úlohy kódování můžete použít Azure Media Clipper k vytváření dynamických filtrů manifestu. Filtry nevyžadují kódování a dají se rychle vytvořit, protože nový Asset se nevytvoří. Výstupní kontrakt pro výstřižek filtru je objekt JSON s následujícími vlastnostmi:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Chcete-li odeslat volání REST k vytvoření dynamického filtru manifestu, odešlete přidruženou datovou část filtru pomocí [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
