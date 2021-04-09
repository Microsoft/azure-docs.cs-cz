---
title: Schéma vstupních metadat Azure Media Services V3
description: Tento článek poskytuje přehled o schématu vstupních metadat Azure Media Services V3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634681"
---
# <a name="input-metadata"></a>Vstupní metadata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Úloha kódování je přidružená ke vstupnímu assetu (nebo assetům), na kterém chcete provést některé úlohy kódování.  Po dokončení úlohy se vytvoří výstupní Asset. Výstupní Asset obsahuje video, zvuk, miniatury, manifesty a další soubory. 

Výstupní Asset obsahuje také soubor s metadaty o vstupním prostředku. Název souboru JSON metadat má náhodné ID, nepoužívejte ho k identifikaci vstupního assetu, ke kterému výstupní Asset patří. Chcete-li identifikovat vstupní prostředek, ke kterému patří, použijte `Uri` pole (Další informace naleznete v [části ostatní podřízené prvky](#other-child-elements)).  

Media Services neprovádí prověřování vstupních assetů za účelem generování metadat. Vstupní metadata se generují jako artefakt, když se v úloze zpracuje vstupní Asset. Proto je tento artefakt zapsán do výstupního prostředku. Pro generování metadat pro vstupní prostředky a výstupní prostředky se používají různé nástroje. Vstupní metadata proto mají trochu jiné schéma než výstupní metadata.

Tento článek popisuje prvky a typy schématu JSON, na kterých je vstupní metada ( &lt; asset_id &gt;_metadata.json) založený. Informace o souboru, který obsahuje metadata o výstupním prostředku, najdete v tématu [výstupní metadata](output-metadata-schema.md).  

Příklad schématu JSON najdete na konci tohoto článku.  

## <a name="assetfile"></a>AssetFile  

Obsahuje kolekci elementů AssetFile pro úlohu kódování.  

> [!NOTE]
> Následující čtyři podřízené elementy se musí objevit v sekvenci.

| Název  | Description |
| --- | --- | 
| **VideoTracks**|Každý soubor fyzického majetku může obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. Další informace najdete v tématu [VideoTracks](#videotracks). |
| **AudioTracks**|Každý fyzický soubor prostředků může obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Další informace najdete v tématu [AudioTracks](#audiotracks) . |
| **Metadata**  |Metadata souboru prostředků reprezentovaná jako key\value řetězce. <br />Příklad: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Ostatní podřízené elementy

| Název | Popis |
| --- | --- |
| **Název**<br />Vyžadováno |Název souboru assetu. <br /><br />Příklad: `"Name": "Ignite-short.mp4"` |
| **Identifikátor URI**<br />Vyžadováno |Adresa URL, kde se nachází vstupní Asset. Pokud chcete identifikovat vstupní Asset, do kterého patří výstupní Asset, použijte `Uri` místo ID pole.|
| **Velikost**<br />Vyžadováno |Velikost souboru prostředků v bajtech  <br /><br />Příklad: `"Size": 75739259`|
| **Doba trvání**<br />Vyžadováno |Doba přehrávání obsahu <br /><br />Příklad: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Vyžadováno |Počet datových proudů v souboru prostředků.  <br /><br />Příklad: `"NumberOfStreams": 2`|
| **FormatNames**<br />Vyžadováno |Názvy formátů.  <br /><br />Příklad: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Vyžadováno |Formátování podrobných názvů. <br /><br />Příklad: `"FormatVerboseName": "QuickTime / MOV"` |
| **Spuštění** |Čas zahájení obsahu  <br /><br />Příklad: `"StartTime": "PT0S"` |
| **OverallBitRate** |Průměrná přenosová rychlost souboru prostředků v bitech za sekundu  <br /><br />Příklad: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Název | Description |
| --- | --- |
| **FourCC**<br />Vyžadováno |Videokodek FourCC kód, který je hlášen pomocí FFmpeg.<br /><br />Příklad: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Profil** |Profil stopy videa. <br /><br />Příklad: `"Profile": "Main"`|
| **Obsah** |Úroveň stopy videa <br /><br />Příklad: `"Level": "3.2"`|
| **PixelFormat** |Formát pixelu stopy videa <br /><br />Příklad: `"PixelFormat": "yuv420p"`|
| **Width (Šířka)**<br />Vyžadováno |Zakódovaná Šířka videa v pixelech <br /><br />Příklad: `"Width": "1280"`|
| **Height (Výška)**<br />Vyžadováno |Výška kódovaného videa v pixelech<br /><br />Příklad: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Vyžadováno |Čítač zobrazení poměru stran videa<br /><br />Příklad: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Vyžadováno |Jmenovatel poměru stran zobrazení videa <br /><br />Příklad: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Ukázka poměru stran videa – čitatel. <br /><br />Příklad: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Příklad: `"SampleAspectRatioDenominator": 1.0`|
| **Snímků**<br />Vyžadováno |Měřená snímková frekvence videa ve formátu. 3F. <br /><br />Příklad: `"FrameRate": 29.970`|
| **Rychlostí** |Průměrná přenosová rychlost videa v bitech za sekundu, jak je vypočítána ze souboru assetu. Počítá se pouze část základního datového proudu a režijní náklady na balení nejsou zahrnuty. <br /><br />Příklad: `"Bitrate": 8421583`|
| **HasBFrames** |Video sleduje počet snímků B. <br /><br />Příklad: `"HasBFrames": 2`|
| **Metadata** |Obecné řetězce klíč/hodnota, které lze použít k uložení nejrůznějších informací. <br />Viz kompletní příklad na konci článku. |
| **Účet**<br />Vyžadováno |Index s nulovým základem tohoto zvukového zvuku nebo stopy videa<br /><br /> Toto **ID** nemusí nutně být TrackID, jak se používá v souboru MP4. <br /><br />Příklad: `"Id": 2`|
| **Kodek** |Řetězec kodeku pro sledování videa <br /><br />Příklad: `"Codec": "h264 | hev1"`|
| **CodecLongName** |Dlouhý název kodeku zvuk nebo video stop <br /><br />Příklad: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Kodek** |Řetězec kodeku pro sledování videa <br /><br />Příklad: `"Codec": "h264 | hev1"`|
| **Porno**<br />Vyžadováno |Časová základna<br /><br />Příklad: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Počet snímků (přítomných pro stopy videa). <br /><br />Příklad: `"NumberOfFrames": 2107`|
| **Spuštění** |Čas zahájení sledování<br /><br />Příklad: `"StartTime": "PT0.033S"` |
| **Doba trvání** |Doba trvání sledování <br /><br />Příklad: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Název  | Description |
| --- | --- | 
| **SampleFormat** |Vzorový formát <br /><br />Příklad: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Rozložení kanálu. <br /><br />Příklad: `"ChannelLayout": "stereo"`|
| **Kanály**<br />Vyžadováno |Číslo (0 nebo více) zvukových kanálů. <br /><br />Příklad: `"Channels": 2`|
| **SamplingRate**<br />Vyžadováno |Vzorkovací frekvence zvuku v ukázkách/s nebo Hz. <br /><br />Příklad: `"SamplingRate": 48000`|
| **Rychlostí** |Průměrná rychlost zvukového bitu v bitech za sekundu, jak je vypočítána ze souboru assetu. Počítá se pouze část základního datového proudu a režie balení není v tomto počtu zahrnutá. <br /><br />Příklad: `"Bitrate": 192080`|
| **Metadata** |Obecné řetězce klíč/hodnota, které lze použít k uložení nejrůznějších informací.  <br />Viz kompletní příklad na konci článku. |
| **Účet**<br />Vyžadováno |Index s nulovým základem tohoto zvukového zvuku nebo stopy videa<br /><br /> To neznamená, že TrackID, jak se používá v souboru MP4. <br /><br />Příklad: `"Id": 1`|
| **Kodek** |Řetězec kodeku pro sledování videa <br /><br />Příklad: `"Codec": "aac"`|
| **CodecLongName** |Dlouhý název kodeku zvuk nebo video stop <br /><br />Příklad: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Porno**<br />Vyžadováno |Časová základna<br /><br />Příklad: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Počet snímků (přítomných pro stopy videa). <br /><br />Příklad: `"NumberOfFrames": 3294`|
| **Spuštění** |Čas zahájení sledování Další informace najdete v tématu [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Příklad: `"StartTime": "PT0S"` |
| **Doba trvání** |Doba trvání sledování <br /><br />Příklad: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadata

| Název | Description |
| --- | --- |
| **zkrat**<br />Vyžadováno |Klíč v páru klíč/hodnota. |
| **value**<br /> Vyžadováno |Hodnota v páru klíč/hodnota. |

## <a name="schema-example"></a>Příklad schématu

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

[Výstupní metadata](output-metadata-schema.md)
