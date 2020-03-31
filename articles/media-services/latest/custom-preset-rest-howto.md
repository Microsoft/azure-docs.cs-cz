---
title: Zakódovat vlastní transformaci pomocí media services v3 REST - Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódovat vlastní transformaci pomocí REST.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761795"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Jak zakódovat pomocí vlastní transformace - REST

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených předvoleb založených na osvědčených postupech v oboru, jak je znázorněno v kurzu [Streamování souborů.](stream-files-tutorial-with-rest.md#create-a-transform) Můžete také vytvořit vlastní přednastavení pro cílí na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující aspekty:

* Všechny hodnoty pro výšku a šířku obsahu AVC musí být násobkem 4.
* Ve službě Azure Media Services v3 jsou všechny přetáčicí přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi v2 API, který používá kilobitů za sekundu jako jednotku. Například pokud byl určen přenosový tok v 2 jako 128 (kilobitů za sekundu), ve v3 by byla nastavena na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky 

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). <br/>Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Mediální služby. 
- [Konfigurace postman pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).<br/>Ujistěte se, že postupujte podle posledního kroku v tématu [Získat Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definování vlastního přednastavení

Následující příklad definuje tělo požadavku nové transformace. Definujeme sadu výstupů, které chceme generovat při použití této transformace. 

V tomto příkladu nejprve přidáme vrstvu AacAudio pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřazujeme popisky tak, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup obsahoval také miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, generované při 50 % rozlišení vstupního videa a na třech časových razítkách - {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát výstupních souborů - jeden pro video + audio a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, které produkují jedinečné názvy na vrstvu. Můžeme použít `{Label}` buď `{Bitrate}` nebo makro, příklad ukazuje bývalý.

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
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
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

## <a name="create-a-new-transform"></a>Vytvoření nové transformace  

V tomto příkladu vytvoříme **transformaci,** která je založena na vlastní přednastavení jsme definovali dříve. Při vytváření transformace, měli byste nejprve použít [Získat](https://docs.microsoft.com/rest/api/media/transforms/get) ke kontrole, pokud již existuje. Pokud transformace existuje, znovu jej použijte. 

V kolekci Pošťáka, kterou jste stáhli, vyberte **Transformovat a vytváření úloh**->**nebo aktualizovat transformaci**.

Metoda požadavku **PUT** HTTP je podobná:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Vyberte kartu **Tělo** a nahraďte tělo kódem json, který jste [definovali dříve](#define-a-custom-preset). Chcete-li použít transformaci na zadané video nebo zvuk, je třeba odeslat úlohu v rámci této transformace.

Vyberte **Poslat**. 

Chcete-li použít transformaci na zadané video nebo zvuk, je třeba odeslat úlohu v rámci této transformace. Úplný příklad, který ukazuje, jak odeslat úlohu v rámci transformace, najdete [v tématu Výuka: Streamovat video soubory - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Další kroky

Zobrazit [další operace REST](https://docs.microsoft.com/rest/api/media/)
