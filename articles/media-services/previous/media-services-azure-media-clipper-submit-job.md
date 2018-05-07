---
title: "Odesílání úloh výstřižek Azure Media výřez | Microsoft Docs"
description: "Postup odeslání úlohy výstřižek z média výřez Azure"
services: media-services
keywords: "klip; subclip; kódování; média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Odesílání úloh výstřižek z média výřez Azure
Vyžaduje Azure výřez média **submitSubclipCallback** metody k implementaci pro zpracování výstřižek úlohy odeslání. Tato funkce je pro implementaci HTTP POST výstupu výřez k webové službě. Tato webová služba je, kde můžete odeslat úlohy kódování. Výstup výřez je buď Media Encoder Standard kódování přednastavených vykreslené úloh nebo REST API datové části pro dynamické manifestu filtrovat volání. Tento model průchozí je nutné, protože přihlašovací údaje účtu media services nejsou zabezpečené v prohlížeči klienta.

Následující diagram pořadí znázorňuje pracovní postup mezi klienta prohlížeče, webové služby a služby Azure Media Services: ![Azure Media výřez pořadí Diagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Na předchozím obrázku jsou čtyři entity: koncového uživatele prohlížeče, webové služby, koncový bod CDN hostování výřez prostředků a Azure Media Services. Když koncový uživatel přejde na webové stránce, získá stránce výřez JavaScript a CSS prostředky z hostitelských koncového bodu CDN. Koncový uživatel nakonfiguruje úloha výstřižek nebo volání vytvoření dynamické manifestu filtru ze svého prohlížeče. Když koncový uživatel odešle volání vytvoření úlohy nebo filtr, uloží prohlížeče datové části úlohy k webové službě, která je nutné nasadit. Tato webová služba nakonec předá úlohu výstřižek nebo volání Vytvoření filtru k Azure Media Services pomocí médiu služeb přihlašovací údaje účtu.

Následující příklad kódu ukazuje ukázku **submitSubclipCallback** metoda. V této metodě implementovat HTTP POST Media Encoder Standard kódování předvolby. Pokud byl úspěšný v příspěvku (**výsledek**), **Promise** vyřešen, jinak se odmítne kvůli podrobnosti o chybě.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
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
Výstup úlohy odeslání je buď Media Encoder Standard přednastavení kódování pro vykreslené úlohu nebo REST API datové části pro dynamické filtry manifestu.

## <a name="submitting-encoding-job-to-create-video"></a>Odeslání úlohy kódování, chcete-li vytvořit video
Můžete odeslat Media Encoder Standard úlohy kódování vytvořit přesný snímek video klip. Kódování úlohy produktu vykresluje videa, nový fragmentované souboru MP4.

Kontrakt výstup úlohy vykreslené výstřižek je objekt JSON s následujícími vlastnostmi:

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
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
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

K provedení úlohy kódování, odeslání Media Encoder Standard úlohy kódování s přidruženého přednastavení. Najdete v tomto článku podrobnosti o odesílání kódování úlohy pomocí [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) nebo [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Rychlé vytváření videosoubory bez kódování
Alternativa k vytváření úlohu kódování, můžete použít výřez Azure Media k vytvoření manifestu dynamické filtry. Filtry nevyžadují kódování a můžete se rychle vytvořit, protože není vytvořen nový prostředek. Výstup kontrakt výstřižek filtru je objekt JSON s následujícími vlastnostmi:

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

Pokud chcete odeslat volání REST za účelem vytvoření dynamického manifestu filtru, odeslání pomocí datové části přidružené filtru [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).