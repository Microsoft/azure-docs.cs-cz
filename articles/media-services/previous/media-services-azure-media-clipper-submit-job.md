---
title: Odesílání úloh oříznutí Azure Media Clipperem | Dokumentace Microsoftu
description: Postup pro odesílání úloh oříznutí z Azure Media Clipperem
services: media-services
keywords: Galerie, dílčí klip, kódování, médií
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0894c3677b87fe48c130d648253dadd0d43429f4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821443"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Odesílání úloh oříznutí z Azure Media Clipperem
Azure Media Clipperem vyžaduje **submitSubclipCallback** metody k implementaci pro zpracování odesílání úloh oříznutí. Tato funkce je pro implementaci metody POST protokolu HTTP Clipperem výstupu na webovou službu. Tato webová služba je, kde můžete odeslat kódovací úlohu. Výstup Clipperem je buď kodéru Media Encoder Standard kódování předvolbu pro vykreslený úlohy nebo datové části rozhraní REST API pro volání filtr dynamických manifestů. Tento model průchozí je nezbytné, protože přihlašovací údaje k účtu media services nejsou zabezpečené do prohlížeče klienta.

Následující sekvence diagramu znázorňuje pracovní postupy mezi klientského prohlížeče, webové služby a služby Azure Media Services: ![Azure Media Clipperem sekvenční Diagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Na předchozím obrázku, jsou čtyři entity: koncový uživatel prohlížeče, webové služby, koncový bod CDN hostování Clipperem prostředky a Azure Media Services. Když koncový uživatel přejde na webovou stránku, na stránce získá Clipperem JavaScript a CSS prostředky z hostitelských koncového bodu CDN. Koncový uživatel konfiguruje výstřižek úlohy nebo volání vytvoření filtr dynamických manifestů ze svého prohlížeče. Když koncový uživatel odešle volání vytvoření úlohy nebo filtr, vloží prohlížeči datové úlohy pro webovou službu, je nutné nasadit. Tato webová služba nakonec odešle úlohu výstřižek nebo volání Vytvoření filtru k Azure Media Services pomocí médií služby přihlašovací údaje k účtu.

Následující příklad kódu znázorňuje ukázku **submitSubclipCallback** metody. V této metodě implementaci požadavku HTTP POST kodéru Media Encoder Standard předvolbu kódování. Pokud příspěvek byl úspěšný (**výsledek**), **Promise** vyřeší, v opačném případě se odmítne kvůli podrobnosti o chybě.

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
Výstup úlohy odeslání je buď kodéru Media Encoder Standard předvolba kódování pro vykreslený úlohu nebo datové části rozhraní REST API pro filtry dynamických manifestů.

## <a name="submitting-encoding-job-to-create-video"></a>Odesílání úlohy kódování k vytvoření videa
Můžete odeslat úlohu kodéru Media Encoder Standard kódování k vytvoření snímku přesné videoklip. Kódování vytvoří úlohu vykreslení videa, nový fragmentovaný soubor MP4.

Kontrakt výstup úlohy pro vykreslený výstřižek je objekt JSON s následujícími vlastnostmi:

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

K provedení úlohy kódování, odeslání Media Encoder Standard úlohy kódování s přidruženým předvolby. Zobrazit tento článek o tom, jak poslat kódování úlohy s použitím [sady .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) nebo [rozhraní REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Rychlé vytváření videoklipy bez kódování
Alternativa k vytvoření úlohy kódování, chcete-li vytvořit filtry dynamických manifestů můžete použít Azure Media Clipperem. Filtry nevyžadují žádné kódování a lze jej rychle vytvořit, protože není vytvořen nový prostředek. Výstup smlouvu pro oříznutí filtru je objekt JSON s následujícími vlastnostmi:

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

Svoje volání REST vytvořit filtr dynamických manifestů můžete odeslat pomocí datové části přidružený filtr [rozhraní REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).