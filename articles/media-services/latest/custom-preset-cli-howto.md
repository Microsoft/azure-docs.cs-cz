---
title: Kódování vlastní transformace pomocí příkazového řádku služby Media Services v3 – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódování vlastní transformace pomocí rozhraní příkazového řádku.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: b0329c8c0bfa56e5ae36c5f4223edca91e6510a0
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755601"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Kódování s vlastní transformace – rozhraní příkazového řádku

Při kódování pomocí Azure Media Services, můžete začít rychle s jedním z doporučených předdefinované předvolby, podle doporučené postupy, jak je ukázáno v [streamování souborů](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) rychlý start. Můžete také vytvořit vlastní přednastavení cílit na konkrétní požadavky scénáře nebo zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastní předvolby, platí následující aspekty:

* Všechny hodnoty pro výšku a šířku na AVC obsahu musí být násobkem 4.
* V Azure Media Services v3 kódování přenosových rychlostí jsou všechny bity za sekundu. Tím se liší od přednastavení pomocí rozhraní API v2, který používá kilobitů za sekundu za jednotku. Například pokud přenosovými rychlostmi ve verzi v2 byl zadán jako 128 (kilobitů za sekundu), ve verzi 3 to se nastavuje na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md). <br/>Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definovat vlastní předvolba

Následující příklad definuje nové transformace textu požadavku. Definujeme sadu výstupy, které chcete generovat, když se používá tato transformace. 

V tomto příkladu přidáme první vrstvu AacAudio pro zvuk kódování a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa jsme přiřadit popisky, takže je možné v názvu výstupního souboru. V dalším kroku chceme výstup tak, aby zahrnoval miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, vygeneruje ve výši 50 % rozlišení vstupního videa a na tři časová razítka – {25 %, 50 %, 75} délky vstupního videa. A konečně, My určete formát pro výstupní soubory – jeden pro videa a zvuku a druhou pro miniaturu. Vzhledem k tomu, že máme několik H264Layers, musíme použít makra, která vytvoří jedinečné názvy jednu vrstvu. Můžeme použít `{Label}` nebo `{Bitrate}` – makro, příklad ukazuje bývalé.

Budeme se tato transformace uložit do souboru. V tomto příkladu používáme název souboru `customPreset.json`. 

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Vytvořit nové transformace  

V tomto příkladu vytvoříme **transformace** , který je založen na vlastní předvolba jsme definovali dříve. Při vytváření transformace, měli by nejdřív zkontrolovat, pokud jedna již existuje. Pokud existuje transformací, která ji znovu použít. Následující `show` příkaz vrátí `customTransformName` transformace, pokud existuje:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Následující příkaz rozhraní příkazového řádku vytvoří transformace podle vlastní předvolbu (definovali dříve). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Pro Media Services použít transformace na zadané video ani zvuk budete muset odeslat úlohu v rámci této transformace. Kompletní příklad, který ukazuje, jak se odeslat úlohu v rámci transformace, najdete v části [rychlý start: Stream video soubory – rozhraní příkazového řádku](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
