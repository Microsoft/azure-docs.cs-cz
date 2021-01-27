---
title: Kódování vlastní transformace pomocí Media Services V3 REST – Azure
description: V tomto tématu se dozvíte, jak použít Azure Media Services V3 ke kódování vlastní transformace pomocí REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 7a2cfc0db2f95b21c872dd2ed0cf4fbf675a8d6f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897607"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Jak kódovat pomocí vlastní transformace – REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených integrovaných přednastavení na základě osvědčených postupů, jak je znázorněno v kurzu [streamování souborů](stream-files-tutorial-with-rest.md#create-a-transform) . Můžete také vytvořit vlastní předvolby, která bude cílit na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující požadavky:

* Všechny hodnoty pro výšku a šířku v obsahu AVC musí být násobkem 4.
* V Azure Media Services V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi rozhraními API v2, která jako jednotku používala kilobity za sekundu. Pokud je například přenosová rychlost v v2 zadaná jako 128 (kilobit/s), ve verzi V3 by se nastavila na 128000 (bity za sekundu).

## <a name="prerequisites"></a>Požadavky 

- [Vytvořte účet Media Services](./create-account-howto.md). <br/>Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services. 
- [Nakonfigurujte metodu post pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).<br/>Nezapomeňte postupovat podle posledního kroku v tématu [získání tokenu Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definování vlastní předvolby

Následující příklad definuje tělo žádosti o novou transformaci. Definujeme sadu výstupů, které chceme vygenerovat při použití této transformace. 

V tomto příkladu jsme nejdřív přidali vrstvu AacAudio pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřadíme popisky, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup zahrnoval také miniatury. V následujícím příkladu určíme obrázky ve formátu PNG vygenerované v 50% rozlišení vstupního videa a tři časová razítka – {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát pro výstupní soubory – jeden pro video a zvuk a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, která vytvoří jedinečné názvy na každou vrstvu. Můžeme buď použít `{Label}` `{Bitrate}` makro nebo, v příkladu se zobrazí předchozí.

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

## <a name="create-a-new-transform"></a>Vytvořit novou transformaci  

V tomto příkladu vytvoříme **transformaci** , která je založena na vlastní předvolbě, které jsme definovali dříve. Při vytváření transformace byste nejprve měli použít příkaz [Get](/rest/api/media/transforms/get) a ověřit, zda již existuje. Pokud transformace existuje, znovu ji použijte. 

V kolekci, kterou jste stáhli, vyberte Transformers **a úlohy** -> **vytvořit nebo aktualizovat transformaci**.

Metoda **Put** http je podobná:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Vyberte kartu **tělo** a nahraďte text kódem JSON, který jste [definovali dříve](#define-a-custom-preset). Pokud Media Services chcete použít transformaci na zadané video nebo zvuk, musíte úlohu v rámci této transformace odeslat.

Vyberte **Odeslat**. 

Pokud Media Services chcete použít transformaci na zadané video nebo zvuk, musíte úlohu v rámci této transformace odeslat. Kompletní příklad, který ukazuje, jak odeslat úlohu v rámci transformace, najdete v tématu [kurz: streamování souborů videa – REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Další kroky

Zobrazit [Další operace REST](/rest/api/media/)
