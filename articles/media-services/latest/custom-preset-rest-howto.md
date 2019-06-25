---
title: Kódování vlastní transformace pomocí rozhraní REST služby Media Services v3 – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódování vlastní transformace pomocí rozhraní REST.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761795"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Kódování s vlastní transformace – REST

Při kódování pomocí Azure Media Services, můžete začít rychle s jedním z doporučených předdefinované předvolby, podle doporučené postupy, jak je ukázáno v [streamování souborů](stream-files-tutorial-with-rest.md#create-a-transform) kurzu. Můžete také vytvořit vlastní přednastavení cílit na konkrétní požadavky scénáře nebo zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastní předvolby, platí následující aspekty:

* Všechny hodnoty pro výšku a šířku na AVC obsahu musí být násobkem 4.
* V Azure Media Services v3 kódování přenosových rychlostí jsou všechny bity za sekundu. Tím se liší od přednastavení pomocí rozhraní API v2, který používá kilobitů za sekundu za jednotku. Například pokud přenosovými rychlostmi ve verzi v2 byl zadán jako 128 (kilobitů za sekundu), ve verzi 3 to se nastavuje na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky 

- [Vytvoření účtu Media Services](create-account-cli-how-to.md). <br/>Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).<br/>Ujistěte se, že chcete postupovat podle poslední krok v tomto tématu [získat Azure AD na Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definovat vlastní předvolba

Následující příklad definuje nové transformace textu požadavku. Definujeme sadu výstupy, které chcete generovat, když se používá tato transformace. 

V tomto příkladu přidáme první vrstvu AacAudio pro zvuk kódování a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa jsme přiřadit popisky, takže je možné v názvu výstupního souboru. V dalším kroku chceme výstup tak, aby zahrnoval miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, vygeneruje ve výši 50 % rozlišení vstupního videa a na tři časová razítka – {25 %, 50 %, 75} délky vstupního videa. A konečně, My určete formát pro výstupní soubory – jeden pro videa a zvuku a druhou pro miniaturu. Vzhledem k tomu, že máme několik H264Layers, musíme použít makra, která vytvoří jedinečné názvy jednu vrstvu. Můžeme použít `{Label}` nebo `{Bitrate}` – makro, příklad ukazuje bývalé.

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

## <a name="create-a-new-transform"></a>Vytvořit nové transformace  

V tomto příkladu vytvoříme **transformace** , který je založen na vlastní předvolba jsme definovali dříve. Po vytvoření transformace, nejprve použijte [získat](https://docs.microsoft.com/rest/api/media/transforms/get) ke kontrole, pokud už existuje. Pokud existuje transformací, která ji znovu použít. 

Postman collection, který jste stáhli, vyberte **transformuje a úlohy**->**vytvoření nebo aktualizace transformace**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Vyberte **tělo** kartu a nahradit text pomocí tohoto kódu json kód [definovali dříve](#define-a-custom-preset). Pro Media Services použít transformace na zadané video ani zvuk budete muset odeslat úlohu v rámci této transformace.

Vyberte **Poslat**. 

Pro Media Services použít transformace na zadané video ani zvuk budete muset odeslat úlohu v rámci této transformace. Kompletní příklad, který ukazuje, jak se odeslat úlohu v rámci transformace, najdete v části [kurzu: Stream video soubory – REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Další postup

Zobrazit [jiné operace REST](https://docs.microsoft.com/rest/api/media/)
