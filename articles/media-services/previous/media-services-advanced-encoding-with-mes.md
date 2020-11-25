---
title: Rozšířené kódování pomocí přizpůsobení předvoleb v inverze | Microsoft Docs
description: V tomto tématu se dozvíte, jak provést rozšířené kódování přizpůsobením Media Encoder Standard přednastavení úloh.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 4288e2e955f8205f3b6551c83a5c883eecf02501
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021142"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Rozšířené kódování pomocí přizpůsobení předvoleb inverze

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

V tomto tématu se dozvíte, jak přizpůsobit Media Encoder Standard předvolby. V tématu [kódování s Media Encoder Standard pomocí vlastního přednastavení](media-services-custom-mes-presets-with-dotnet.md) se dozvíte, jak pomocí .NET vytvořit úlohu kódování a úlohu, která tuto úlohu spustí. Po přizpůsobení předvolby zadejte vlastní přednastavení pro úlohu kódování. 

Pokud používáte předvolbu XML, nezapomeňte zachovat pořadí prvků, jak je znázorněno v ukázkách XML níže (například KeyFrameInterval by měl předcházet SceneChangeDetection).

> [!NOTE] 
> Mnohé z pokročilých funkcí Media Services V2 Media Encoder Standard v současnosti nejsou v systému V3 k dispozici. Další informace najdete v tématu [mezery k funkcím](../latest/media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Podpora relativních velikostí

Při generování miniatur není nutné vždy zadávat šířku a výšku výstupu v pixelech. Můžete je zadat v procentech v rozsahu [1%,..., 100%].

### <a name="json-preset"></a>Přednastavení JSON

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Generovat miniatury

V této části se dozvíte, jak přizpůsobit předvolbu, která generuje miniatury. Předem definovaná přednastavení obsahují informace o tom, jak chcete soubor zakódovat, i informace potřebné k vygenerování miniatur. Můžete využít kterékoli z přednastavených součástí informovaná v [této](media-services-mes-presets-overview.md) části a přidat kód, který generuje miniatury.  

> [!NOTE]
> Nastavení **SceneChangeDetection** v následující předvolbě lze nastavit pouze na hodnotu true, pokud používáte kódování na video s jednou přenosovou rychlostí. Pokud kódujete na video s více přenosovými rychlostmi a nastavíte **SceneChangeDetection** na hodnotu true, kodér vrátí chybu.  
>
>

Informace o schématu naleznete v [tomto](media-services-mes-schema.md) tématu.

Nezapomeňte si prohlédnout část s [informacemi](#considerations) o tom, co je potřeba.

### <a name="json-preset"></a>Přednastavení JSON

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>4500</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>4500</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
    <JpgImage Start="{Best}">
      <JpgLayers>
        <JpgLayer>
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Požadavky

Platí následující důležité informace:

* Použití explicitních časových razítek pro počátek/krok/rozsah předpokládá, že vstupní zdroj je alespoň 1 minuta dlouhý.
* Elementy jpg/png/BmpImage obsahují atributy řetězce začátek, krok a rozsah – tyto prvky lze interpretovat jako:

  * Číslo rámce, pokud jsou nezáporná celá čísla, například Start: "120",
  * Relativní ke zdrojové době trvání, pokud je vyjádřena jako%, například "Start": "15%" nebo
  * Časové razítko, pokud se vyjádří jako HH: MM: SS... formát, například "Start": "00:01:00"

    Při psaní můžete kombinovat a párovat notace.

    Kromě toho také spuštění podporuje speciální makro {Best}, které se pokusí určit první "zajímavý" rámec poznámky k obsahu: (krok a rozsah se ignoruje, když je počátek nastavený na {Best}).
  * Výchozí hodnoty: začátek: {nejlepší}
* Výstupní formát se musí explicitně zadat pro každý formát obrázku: jpg/png/BmpFormat. V případě, že je přítomný, rozchází na JpgVideo na JpgFormat a tak dále. OutputFormat zavádí nové makro konkrétního kodeku pro Image: {index}, které musí být přítomné (jednou a jenom jednou) pro formáty výstupu obrázku.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Střih videa (oříznutí)
V této části se dozvíte, jak upravit předvolby kodéru pro vystřižení nebo střihování vstupního videa, kde je vstup nazvaný Mezzanine soubor nebo soubor na vyžádání. Kodér je také možné použít k vystřihování nebo ořezávání assetu, který je zachycen nebo archivován z živého datového proudu – podrobnosti pro tuto položku jsou k dispozici v [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Chcete-li oříznout videa, můžete využít některé z přednastavení v programu informované v [této](media-services-mes-presets-overview.md) části a upravit **zdrojový** element (jak je vidět níže). Hodnota StartTime musí odpovídat absolutním časovým razítkem vstupního videa. Například pokud má první snímek vstupního videa časové razítko 12:00:10.000, pak StartTime by měl být alespoň 12:00:10.000 a vyšší. V následujícím příkladu předpokládáme, že vstupní video má počáteční časové razítko nula. **Zdroje** by se měly umístit na začátek přednastavení.

### <a name="json-preset"></a><a id="json"></a>Přednastavení JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1000,
          "MaxBitrate": 1000,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 650,
          "MaxBitrate": 650,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 400,
          "MaxBitrate": 400,
          "BufferWindow": "00:00:05",
          "Width": 320,
          "Height": 180,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
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
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>Přednastavení XML
Chcete-li oříznout vaše videa, můžete si proniknout jakékoli z [popsaných](media-services-mes-presets-overview.md) PŘEDNASTAVENÍ programu status a upravit **zdrojový** prvek (jak je vidět níže).

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Vytvořit překryv

Media Encoder Standard umožňuje překrýt obrázek do existujícího videa. V současné době jsou podporovány následující formáty: PNG, jpg, GIF a BMP. Předdefinovaná definice níže je základní příklad překrytí videa.

Kromě definování přednastaveného souboru musíte také nechat Media Services zjistit, který soubor v assetu je překrytý obrázkem a který soubor je zdrojové video, na které chcete obrázek překrýt. Videosoubor musí být **primárním** souborem.

Pokud používáte rozhraní .NET, přidejte následující dvě funkce do příkladu .NET definovaného v [tomto](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tématu. Funkce **UploadMediaFilesFromFolder** nahrává soubory ze složky (například BigBuckBunny.mp4 a Image001.png) a nastaví soubor MP4 jako primární soubor v prostředku. Funkce **EncodeWithOverlay** používá vlastní přednastavený soubor, který byl předán (například přednastavení, které následuje) k vytvoření úlohy kódování.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Aktuální omezení:
>
> Nastavení neprůhlednosti překrytí není podporováno.
>
> Zdrojový videosoubor a soubor překryvných obrázků musí být ve stejném prostředku a videosoubor musí být nastaven jako primární soubor v tomto prostředku.
>
>

### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Vložit tichou zvukovou stopu, když vstup nemá zvuk
Ve výchozím nastavení platí, že pokud odešlete vstup do kodéru, který obsahuje pouze video, a žádný zvuk, pak výstupní prostředek obsahuje soubory, které obsahují pouze data videa. Někteří hráči nemusí být schopni tyto výstupní proudy zpracovat. Toto nastavení můžete použít k vynucení, aby kodér mohl do výstupu v tomto scénáři přidat tichou zvukovou stopu.

Chcete-li vynutit, aby kodér vytvořil Asset, který obsahuje tichou zvukovou stopu, když vstup nemá žádný zvuk, zadejte hodnotu "InsertSilenceIfNoAudio".

V [této](media-services-mes-presets-overview.md) části můžete provést kterékoli z předdefinovaných součástí informování a provést následující úpravy:

### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Zakázat automatické zrušení prokládání
Zákazníci nemusí dělat nic, když chtějí, aby se automaticky prokládaný obsah pro prokládání. Při automatickém zrušení prokládání je zapnuto (výchozí), automatické rozpoznávání prokládaných rámců a jenom rozkládaných snímků označených jako prokládaných.

Automatické zrušení prokládání můžete vypnout. Tato možnost se nedoporučuje.

### <a name="json-preset"></a>Přednastavení JSON

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Jenom předvolby zvukového zařízení
V této části jsou popsány dva zvuky v případě zvuku: zvuk AAC a zvuk ve kvalitním formátu AAC.

### <a name="aac-audio"></a>Zvuk AAC

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>Zvuk ve dobré kvalitě AAC

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Zřetězení dvou nebo více videosouborů

Následující příklad ukazuje, jak můžete vygenerovat přednastavení pro zřetězení dvou nebo více videosouborů. Nejběžnějším scénářem je, když chcete do hlavního videa přidat záhlaví nebo přívěs. Zamýšlené použití je v případě, že soubory videa společně sdílí vlastnosti (rozlišení videa, kmitočet snímků, počet zvukových stop atd.). Měli byste dbát na to, abyste nevzali videa o různých kmitočtech snímků nebo s různými počty zvukových stop.

>[!NOTE]
>Aktuální návrh funkce zřetězení očekává, že vstupní videoklipy jsou konzistentní z hlediska rozlišení, frekvence snímků atd. 

### <a name="requirements-and-considerations"></a>Požadavky a předpoklady

* Vstupní videa by měla mít jenom jednu zvukovou stopu.
* Všechna vstupní videa by měla mít stejnou kmitočet snímků.
* Videa je třeba nahrát do samostatných assetů a nastavovat videa jako primární soubor v jednotlivých prostředcích.
* Potřebujete znát dobu trvání videí.
* V níže uvedených příkladech se předpokládá, že všechna vstupní videa začínají časovým razítkem nula. Pokud mají videa jiné počáteční časové razítko, je třeba upravit hodnoty StartTime, protože se jedná o obvykle v případě živých archivů.
* Přednastavení JSON umožňuje explicitní odkazy na hodnoty AssetID vstupních assetů.
* Vzorový kód předpokládá, že přednastavení JSON bylo uloženo do místního souboru, například "C:\supportFiles\preset.json". Také předpokládá, že se vytvořily dva prostředky nahráním dvou videosouborů a že znáte výsledné hodnoty AssetID.
* Fragment kódu a přednastavení JSON ukazují příklad zřetězení dvou videosouborů. Můžete ji zvětšit na více než dvě videa:

  1. Volání úlohy. InputAssets. Add () opakovaně pro přidání dalších videí v daném pořadí.
  2. Provádění odpovídajících úprav elementu sources ve formátu JSON přidáním dalších záznamů ve stejném pořadí.

### <a name="net-code"></a>Kód .NET

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>Přednastavení JSON

Aktualizujte vlastní předvolby s ID prostředků, které chcete zřetězit, a s odpovídajícím časovým segmentem pro každé video.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Oříznutí videa pomocí kodéru Media Encoder Standard
Podívejte se na téma věnované [oříznutí videí pomocí Media Encoder Standard](media-services-crop-video.md) .

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Vložení stop videa, když vstup nemá žádné video

Ve výchozím nastavení platí, že pokud odešlete vstup do kodéru, který obsahuje pouze zvuk, a žádné video, pak výstupní Asset obsahuje soubory, které obsahují pouze zvukové údaje. Někteří hráči, včetně Azure Media Player (viz [Tento](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)), nemusí být schopni tyto datové proudy zpracovat. Toto nastavení můžete použít k vynucení, aby kodér Přidal do výstupu monochromatickou stopu videa v tomto scénáři.

> [!NOTE]
> Vynucení kodéru vložení výstupního videa zvětší velikost výstupního prostředku, a tím i náklady vzniklé pro úlohu kódování. Měli byste spustit testy, abyste ověřili, že toto výsledné zvýšení má pouze mírný dopad na měsíční poplatky.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Vložení videa pouze do nejnižší přenosové rychlosti

Předpokládejme, že používáte více přednastavení kódování přenosů, jako je například ["H264 více přenosů 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) ke kódování celého vstupního katalogu pro streamování, který obsahuje kombinaci videosouborů a souborů pouze zvuku. Pokud v tomto scénáři nemá vstup žádné video, možná budete chtít, aby kodér vynutil vložení monochromatického videa pouze do nejnižší přenosové rychlosti, a to na rozdíl od vložení videa při každé výstupní přenosové rychlosti. K tomu je potřeba použít příznak **InsertBlackIfNoVideoBottomLayerOnly** .

V [této](media-services-mes-presets-overview.md) části můžete provést kterékoli z předdefinovaných součástí informování a provést následující úpravy:

#### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>Přednastavení XML

Při použití XML použijte Condition = "InsertBlackIfNoVideoBottomLayerOnly" jako atribut pro element **H264Video** a Condition = "InsertSilenceIfNoAudio" jako atribut pro **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Vkládání videa do všech výstupních přenosů
Předpokládejme, že používáte více přednastavení kódování přenosů, jako je například ["H264 více přenosů 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) pro kódování celého vstupního katalogu pro streamování", který obsahuje kombinaci videosouborů a souborů pouze zvuku. Pokud v tomto scénáři nemá vstup žádné video, možná budete chtít, aby kodér vynutil vložení monochromatického videa do všech výstupních přenosů. Tím se zajistí, že jsou všechny vaše výstupní prostředky homogenní vzhledem k počtu stop videa a zvukovým stopám. Chcete-li to dosáhnout, je nutné zadat příznak "InsertBlackIfNoVideo".

V [této](media-services-mes-presets-overview.md) části můžete provést kterékoli z předdefinovaných součástí informování a provést následující úpravy:

#### <a name="json-preset"></a>Přednastavení JSON

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>Přednastavení XML

Při použití XML použijte Condition = "InsertBlackIfNoVideo" jako atribut pro element **H264Video** a Condition = "InsertSilenceIfNoAudio" jako atribut pro **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Otočení videa
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje rotaci pomocí úhlů 0/90/180/270. Výchozí chování je "auto", kde se pokusí detekovat metadata otočení v souboru příchozího videa a kompenzovat je. Zahrňte následující **zdrojový** element do jednoho z přednastavení definovaných v [této](media-services-mes-presets-overview.md) části:

### <a name="json-preset"></a>Přednastavení JSON

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>Přednastavení XML

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Podívejte se také na [Toto](media-services-mes-schema.md#PreserveResolutionAfterRotation) téma, kde najdete další informace o tom, jak kodér interpretuje nastavení šířky a výšky v předvolbě, když se aktivuje kompenzace rotace.

Hodnotu "0" můžete použít k označení kodéru tak, aby ignoroval metadata rotace (Pokud je k dispozici) ve vstupním videu.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled kódování Media Services](media-services-encode-asset.md)
