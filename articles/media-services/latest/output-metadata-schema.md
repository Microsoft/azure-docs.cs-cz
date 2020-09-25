---
title: Azure Media Services schéma metadat pro výstup | Microsoft Docs
description: Tento článek obsahuje přehled schématu výstupní metadata Azure Media Services V3.
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
ms.openlocfilehash: aa6d4edc4348fa850eeb7e8d91ce0791ee4c7170
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336407"
---
# <a name="output-metadata"></a>Výstupní metadata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Úloha kódování je přidružená ke vstupnímu assetu (nebo assetům), na kterém chcete provést některé úlohy kódování. Například kódování souboru MP4 do H. 264 datových sad MP4 s adaptivní přenosovou rychlostí; Vytvořte miniaturu. Vytvořte překryvy. Po dokončení úlohy se vytvoří výstupní Asset.  Výstupní Asset obsahuje video, zvuk, miniatury a další soubory. Výstupní Asset obsahuje také soubor s metadaty o výstupním prostředku. Název souboru JSON metadat má následující formát: `<source_file_name>_manifest.json` (například `BigBuckBunny_manifest.json` ). Měli byste vyhledat jakékoli * _metadata.jsna a zadat dotaz na řetězec FilePath v rámci vyhledání zdrojového názvu souboru (bez zkrácení).

Media Services neprovádí prověřování vstupních assetů za účelem generování metadat. Vstupní metadata se generují jako artefakt, když se v úloze zpracuje vstupní Asset. Proto je tento artefakt zapsán do výstupního prostředku. Pro generování metadat pro vstupní prostředky a výstupní prostředky se používají různé nástroje. Vstupní metadata proto mají trochu jiné schéma než výstupní metadata.

Tento článek popisuje prvky a typy schémat JSON, na kterých je založena výstupní metadata ( &lt; source_file_name &gt;_manifest.json). <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Úplný příklad kódu schématu a formátu JSON najdete na konci tohoto článku.  

## <a name="assetfile"></a>AssetFile

Kolekce záznamů AssetFile pro úlohu kódování.  

| Název | Popis |
| --- | --- |
| **Prostředky** |Kolekce vstupních/zdrojových mediálních souborů, které byly zpracovány za účelem vytvoření tohoto AssetFile.<br />Příklad: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Každý fyzický AssetFile může v něm obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. <br />Viz [VideoTracks](#videotracks). |
| **AudioTracks**|Každý fyzický AssetFile může v něm obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Toto je kolekce všech těchto zvukových stop.<br /> Další informace najdete v tématu [AudioTracks](#audiotracks). |
| **Název**<br />Vyžadováno |Název souboru mediálního prostředku <br /><br />Příklad: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Velikost**<br />Vyžadováno |Velikost souboru prostředků v bajtech <br /><br />Příklad: `"Size": 32414631`|
| **Doba trvání**<br />Vyžadováno |Doba přehrávání obsahu Další informace najdete v tématu formát [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) . <br /><br />Příklad: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Každý fyzický AssetFile může v něm obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. Element **VideoTracks** reprezentuje kolekci všech stop videa.  

| Název | Popis |
| --- | --- |
| **Účet**<br /> Vyžadováno |Index tohoto videa na základě nuly **Poznámka:**  Toto **ID** nemusí nutně být TrackID, jak se používá v souboru MP4. <br /><br />Příklad: `"Id": 1`|
| **FourCC**<br />Vyžadováno | Videokodek FourCC kód, který je hlášen pomocí FFmpeg.  <br /><br />Příklad: `"FourCC": "avc1"`|
| **Profil** |Profil H264 (platí jenom pro kodek H264).  <br /><br />Příklad: `"Profile": "High"` |
| **Obsah** |Úroveň H264 (platí pouze pro kodek H264).  <br /><br />Příklad: `"Level": "3.2"`|
| **Délk**<br />Vyžadováno |Zakódovaná Šířka videa v pixelech  <br /><br />Příklad: `"Width": "1280"`|
| **Výška**<br />Vyžadováno |Výška kódovaného videa v pixelech  <br /><br />Příklad: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Vyžadováno|Čítač zobrazení poměru stran videa  <br /><br />Příklad: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Vyžadováno |Jmenovatel poměru stran zobrazení videa  <br /><br />Příklad: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />Vyžadováno |Měřená snímková frekvence videa ve formátu. 3F.  <br /><br />Příklad: `"Framerate": 29.970`|
| **Rychlostí**<br />Vyžadováno |Průměrná přenosová rychlost videa v bitech za sekundu, jak se počítá z AssetFile. Počítá jenom základní datovou část datového proudu a nezahrnuje režijní náklady na balení.  <br /><br />Příklad: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Vyžadováno |Průměrná přenosová rychlost pro tuto stopu videa, jak je požadováno prostřednictvím přednastaveného kódování, v bitech za sekundu. <br /><br />Příklad: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Každý fyzický AssetFile může v něm obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Element **AudioTracks** reprezentuje kolekci všech těchto zvukových stop.  

| Název  | Popis |
| --- | --- |
| **Účet**<br />Vyžadováno  |Index této zvukové stopy založený na nule. **Poznámka:**  To není nutně TrackID, jak se používá v souboru MP4.  <br /><br />Příklad: `"Id": 2`|
| **Kodek**  |Řetězec kodeku zvukového záznamu  <br /><br />Příklad: `"Codec": "aac"`|
| **Jazyk**|Příklad: `"Language": "eng"`|
| **Kanály**<br />Vyžadováno|Počet zvukových kanálů.  <br /><br />Příklad: `"Channels": 2`|
| **SamplingRate**<br />Vyžadováno |Vzorkovací frekvence zvuku v ukázkách/s nebo Hz.  <br /><br />Příklad: `"SamplingRate": 48000`|
| **Rychlostí**<br />Vyžadováno |Průměrná rychlost zvukového bitu v bitech za sekundu, jak se počítá z AssetFile. Počítá jenom základní datovou část datového proudu a nezahrnuje režijní náklady na balení.  <br /><br />Příklad: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Příklad schématu JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

[Vytvoření vstupu úlohy z adresy URL HTTPS](job-input-from-http-how-to.md)
