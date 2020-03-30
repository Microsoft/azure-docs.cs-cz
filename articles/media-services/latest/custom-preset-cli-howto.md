---
title: Zakódovat vlastní transformaci pomocí media services v3 Azure CLI | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódovat vlastní transformaci pomocí Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382949"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Kódování pomocí vlastní transformace – Azure CLI

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených předvoleb založených na osvědčených postupech v oboru, jak je znázorněno na rychlém startu [datových proudů souborů.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Můžete také vytvořit vlastní přednastavení pro cílí na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující aspekty:

* Všechny hodnoty pro výšku a šířku obsahu AVC musí být násobkem 4.
* Ve službě Azure Media Services v3 jsou všechny přetáčicí přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi v2 API, který používá kilobitů za sekundu jako jednotku. Například pokud byl určen přenosový tok v 2 jako 128 (kilobitů za sekundu), ve v3 by byla nastavena na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet mediálních služeb](create-account-cli-how-to.md).

Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Mediální služby.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definování vlastního přednastavení

Následující příklad definuje tělo požadavku nové transformace. Definujeme sadu výstupů, které chceme generovat při použití této transformace.

V tomto příkladu nejprve přidáme vrstvu AacAudio pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřazujeme popisky tak, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup obsahoval také miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, generované při 50 % rozlišení vstupního videa a na třech časových razítkách - {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát výstupních souborů - jeden pro video + audio a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, které produkují jedinečné názvy na vrstvu. Můžeme použít `{Label}` buď `{Bitrate}` nebo makro, příklad ukazuje bývalý.

Uložíme tuto transformaci do souboru. V tomto příkladu pojmenujeme soubor `customPreset.json`.

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Vytvoření nové transformace  

V tomto příkladu vytvoříme **transformaci,** která je založena na vlastní přednastavení jsme definovali dříve. Při vytváření transformace, měli byste nejprve zkontrolovat, zda již existují. Pokud transformace existuje, znovu jej použijte. Následující `show` příkaz vrátí `customTransformName` transformaci, pokud existuje:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Následující příkaz Azure CLI vytvoří transformace na základě vlastní přednastavení (definované dříve).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Chcete-li použít transformaci na zadané video nebo zvuk, je třeba odeslat úlohu v rámci této transformace. Úplný příklad, který ukazuje, jak odeslat úlohu v rámci transformace, najdete v článku [Úvodní příručka: Streamovat video soubory – Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Viz také

[Azure CLI](/cli/azure/ams)
