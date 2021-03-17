---
title: Generovat miniatury pomocí Media Encoder Standard REST
description: Tento článek ukazuje, jak použít REST ke kódování assetu a k vygenerování miniatur ve stejnou dobu pomocí Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f4456a65f422c23da73f36dd74680fbb598db186
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955830"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Postup generování miniatur pomocí kodéru Standard s REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pomocí Media Encoder Standard můžete vygenerovat jednu nebo více miniatur ze vstupního videa ve formátech [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)nebo [bmp](https://en.wikipedia.org/wiki/BMP_file_format) .

## <a name="recommended-reading-and-practice"></a>Doporučené čtení a postupy

Doporučujeme, abyste se seznámili s vlastními transformacemi. Přečtěte si, [jak se zakódovat s vlastními nastaveními pro transformaci](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Parametry miniatury

Měli byste nastavit následující parametry:

- **počátek** – pozice ve vstupním videu, ze které se má začít generovat miniatury. Hodnota může být ve formátu ISO 8601 (například PT05S, aby začínala v intervalu 5 sekund) nebo počet snímků (například 10 pro začátek na desátém snímku) nebo relativní hodnota pro dobu trvání streamu (například 10% pro začátek na 10% doby trvání proudu). Také podporuje makro {Best}, které dává kodéru pokyn, aby vybral nejlepší miniaturu z prvních několika sekund videa a vytvoří pouze jednu miniaturu bez ohledu na to, jaká další nastavení jsou pro krok a rozsah. Výchozí hodnota je makro {Best}.
- **Krok** – intervaly, ve kterých jsou vygenerovány miniatury. Hodnota může být ve formátu ISO 8601 (například PT05S pro jednu bitovou kopii každých 5 sekund) nebo počet snímků (například 30 pro jednu bitovou kopii každých 30 snímků) nebo relativní hodnota pro dobu trvání proudu (například 10% pro jednu bitovou kopii každých 10% doby trvání streamu). Hodnota kroku bude mít vliv na první vygenerovanou miniaturu, která nemusí být přesně ta zadaná v počátečním čase přednastavení transformace. Důvodem je kodér, který se pokusí vybrat nejlepší miniaturu mezi časem spuštění a pozicí kroku od počátečního času jako první výstup. Vzhledem k tomu, že výchozí hodnota je 10%, znamená to, že pokud má datový proud dlouhou dobu trvání, první vygenerovaná Miniatura může být daleko od té, která je zadána v počátečním čase. Zkuste vybrat rozumnou hodnotu pro krok, pokud se očekává, že se první Miniatura blíží počátečnímu času, nebo nastavte hodnotu rozsahu na 1, pokud je v počátečním čase potřeba jenom jedna miniatura.
- **Rozsah** – pozice relativní vzhledem k transformaci přednastavené transformace ve vstupním videu, při kterém se zastaví generování miniatur. Hodnota může být ve formátu ISO 8601 (například PT5M30S, aby se zastavila 5 minut a 30 sekund od počátečního času), nebo počet snímků (například 300 k zastavení na 300th snímku v době spuštění. Pokud je tato hodnota 1, znamená to, že v době spuštění je jenom jedna miniatura), nebo relativní hodnota pro dobu trvání datového proudu (například 50%, která se zastaví na polovinu doby trvání streamu od počátečního času). Výchozí hodnota je 100%, což znamená, že se na konci streamu zastaví.
- **vrstvy** – kolekce výstupních vrstev obrazu, které má kodér vyprodukovat.

## <a name="example-of-a-single-png-file-preset"></a>Příklad předvolby "jeden soubor PNG"

Následující předvolbu JSON lze použít k vytvoření jediného výstupního souboru PNG z prvních několika sekund vstupního videa, kde kodér vytváří nejvyšší úsilí při hledání "zajímavého" rámce. Všimněte si, že rozměry výstupních imagí byly nastaveny na 100%, což znamená, že odpovídají rozměrům vstupního videa. Všimněte si také, že nastavení "formát" v části "výstupy" se musí shodovat s použitím "PngLayers" v části "kodeky".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Příklad přednastavení "série obrázků JPEG"

Následující předvolbu JSON lze použít k vytvoření sady 10 imagí v časových razítkech s 5%, 15%,..., 95% vstupní časové osy, kde je velikost obrázku zadaná jako jedna čtvrtina vstupního videa.

### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Příklad "jednoho obrázku v konkrétním časovém razítku"

Následující přednastavení JSON lze použít k vytvoření jednoho obrázku JPEG na 30 sekundovém označení vstupního videa. Tato předvolba očekává, že bude vstupní video v době trvání delší než 30 sekund (jinak úloha neproběhne úspěšně).

### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Příklad předvolby "miniatury v různých rozlišeních"

Následující předvolbu lze použít ke generování miniatur v různých rozlišeních v jednom úkolu. V příkladu na pozicích 5%, 15%,..., 95% vstupní časové osy, kodér vygeneruje dvě image – jednu na 100% vstupního rozlišení videa a druhý na 50%.

Poznámka: použití makra {Solution} v názvu souboru; označuje kodéru, aby používal šířku a výšku, které jste zadali v části Encoding (kódování) při generování názvu souboru výstupních imagí. To vám také pomůže snadno rozlišovat mezi různými obrázky.

### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Příklad generování miniatury při kódování

I když všechny výše uvedené příklady obsahují informace o tom, jak můžete odeslat úlohu kódování, která vytváří pouze image, můžete také zkombinovat kódování videa nebo zvuku s generováním miniatur. Následující přednastavení JSON oznamuje Standard kodéru, aby generoval miniaturu během kódování.

### <a name="json-preset"></a>Přednastavení JSON

Informace o schématu najdete v [tomto](../previous/media-services-mes-schema.md) článku.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky
[Generování miniatur pomocí .NET](media-services-generate-thumbnails-dotnet.md)