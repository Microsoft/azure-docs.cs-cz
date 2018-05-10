---
title: Pokročilé kódování přizpůsobením MES přednastavení | Microsoft Docs
description: Toto téma ukazuje, jak provádět pokročilé kódování přizpůsobením Media Encoder Standard přednastavení úloh.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: juliako
ms.openlocfilehash: 9480e6f3f651611e5281968d6d1651bd39dda44f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Pokročilé kódování přizpůsobením MES přednastavení 

## <a name="overview"></a>Přehled

Toto téma ukazuje, jak přizpůsobit Media Encoder Standard přednastavení. [Kódování s Media Encoder Standard pomocí vlastních předvoleb](media-services-custom-mes-presets-with-dotnet.md) téma ukazuje, jak vytvořit kódování úlohy a úlohy, která spustí tuto úlohu pomocí rozhraní .NET. Jakmile upravíte přednastavení, zadejte vlastní přednastavení kódování úlohy. 

>[!NOTE]
>Pokud používáte přednastavení XML, ujistěte se, chcete-li zachovat pořadí prvků, jak je znázorněno v následující ukázky XML (například KeyFrameInterval by měl předcházet SceneChangeDetection).
>

V tomto tématu je ukázán vlastních předvoleb, které provádět následující úlohy kódování.

## <a name="support-for-relative-sizes"></a>Podpora pro relativní velikosti

Při vytváření miniatur, není potřeba vždycky zadat výstupní šířky a výšky v pixelech. Je můžete zadat v procentech v rozsahu [1 %,..., 100 %].

### <a name="json-preset"></a>Přednastavení JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Přednastavení XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Vytváření miniatur

V této části ukazuje, jak přizpůsobit přednastavení, která generuje miniatur. Přednastavení definovaná níže obsahuje informace o tom, jak chcete zakódovat váš soubor, jakož i informace potřebné k vytváření miniatur. Můžete využít některé z přednastavení MES zdokumentovaný [to](media-services-mes-presets-overview.md) a přidejte kód, který generuje miniatur.  

> [!NOTE]
> **SceneChangeDetection** nastavení v následujících přednastavení lze nastavit pouze na hodnotu true, pokud jsou kódování s jednou přenosovou rychlostí videa. Pokud jsou kódování na video s více přenosovými rychlostmi a sadu **SceneChangeDetection** na hodnotu true, vrátí kodér k chybě.  
>
>

Informace o schématu najdete v tématu [to](media-services-mes-schema.md) tématu.

Projděte si [aspekty](#considerations) části.

### <a id="json"></a>Přednastavení JSON
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


### <a id="xml"></a>Přednastavení XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

### <a name="considerations"></a>Požadavky

Platí následující aspekty:

* Použití explicitní časová razítka pro spuštění nebo krok nebo rozsah předpokládá, že se vstupní zdroj alespoň 1 minuta.
* JPG nebo Png nebo BmpImage prvky má spustit, krok a rozsah řetězec atributy – to jde interpretovat jako:

  * Rámce číslo, pokud jsou nezáporná celá čísla, například "Start": "120",
  * Vzhledem ke zdrojové doba trvání, pokud vyjádřený jako % konci, například "Start": "15 %", nebo
  * Časové razítko, pokud vyjádřený jako hh: mm:... formátování, například "Start": "00: 01:00"

    Můžete kombinovat a párovat zápisy, jako je prosím.

    Kromě toho spustit také podporuje speciální makra: {osvědčené}, která se pokusí určit první "zajímavé" snímek obsahu Poznámka: (krok a rozsah ignorují při spuštění je nastaven na {nejvhodnější})
  * Výchozí nastavení: Spuštění: {nejlepší}
* Výstupní formát je třeba explicitně zadat pro každý formát obrázku: Jpg nebo Png nebo BmpFormat. Pokud jsou k dispozici, odpovídá MES JpgVideo k JpgFormat a tak dále. OutputFormat zavádí nové makro konkrétní kodek obrázků: {Index}, které musí být k dispozici (jednou a jen jednou) pro výstupní formáty bitové kopie.

## <a id="trim_video"></a>Trim video (výstřižek.)
Tato část pojednává o Úprava přednastavení kodér oříznutí nebo trim vstupní video, kde je vstupní soubor mezzanine takzvané soubor nebo soubor na vyžádání. Kodér lze také v případě potřeby upraví nebo trim prostředek, která je zachytit nebo archivovat z živý datový proud – podrobnosti o to jsou k dispozici v [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Oříznout videa, mohou mít libovolný přednastavení MES zdokumentovaný [to](media-services-mes-presets-overview.md) část a upravte **zdroje** – element (jak je znázorněno níže). Hodnota čas spuštění musí odpovídat absolutní časová razítka vstupní videa. Například pokud první snímek vstupní video má časovým razítkem 12:00:10.000, pak čas spuštění by měl alespoň 12:00:10.000 a větší. V následujícím příkladu předpokládáme, že vstupní video má výchozí časové razítko nula. **Zdroje** musí být umístěny na začátku přednastavení.

### <a id="json"></a>Přednastavení JSON
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

### <a name="xml-preset"></a>Přednastavení XML
Oříznout videa, mohou mít libovolný přednastavení MES zdokumentovaný [sem](media-services-mes-presets-overview.md) a upravovat **zdroje** – element (jak je znázorněno níže).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a id="overlay"></a>Vytvoření překrytí

Media Encoder Standard umožňuje překrytí bitovou kopii do existujícího videa. V současné době jsou podporovány následující formáty: png, jpg, gif, bmp a. Předvolba definovaná níže je základní příklad překryvné video.

Kromě definování soubor přednastavení, máte také umožní Media Services vědět, který soubor v prostředku je bitovou kopii překrytí a soubor, který je zdrojem videa na kterém chcete překrýt bitovou kopii. Video souboru musí být **primární** souboru.

Pokud používáte rozhraní .NET, přidejte následující dvě funkce v rozhraní .NET příkladu definované v [to](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tématu. **UploadMediaFilesFromFolder** funkce odešle soubory ze složky (například BigBuckBunny.mp4 a Image001.png) a nastaví soubor mp4 jako primární soubor v prostředku. **EncodeWithOverlay** funkce používá vlastní přednastavené soubor, který byl předán do ní (například přednastavení, která odpovídá) k vytvoření úlohy kódování.


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


> [!NOTE]
> Aktuální omezení:
>
> Nastavení krytí překrytí není podporováno.
>
> Video váš zdrojový soubor a soubor bitové kopie překrytí musí být ve stejné asset a video soubor musí být nastavena jako primární soubor v tento prostředek.
>
>

### <a name="json-preset"></a>Přednastavení JSON
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
                  "OverlayLoopCount": 1,
                  "InputLoop": true
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


### <a name="xml-preset"></a>Přednastavení XML
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
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


## <a id="silent_audio"></a>Vložit tichou zvuk sledovat, pokud vstup neobsahuje žádný zvuk
Ve výchozím nastavení Pokud odesíláte vstup kodéru, který obsahuje pouze video a žádné zvuk, pak výstupní asset obsahuje soubory, které obsahují pouze video data. Některé přehrávače nemusí být schopná zpracovat takové výstupní datové proudy. Toto nastavení slouží k vynucení kodér přidat tichou zvuk sledovat ve výstupu v tomto scénáři.

Chcete-li vynutit kodér k vytvoření asset, který obsahuje tichou zvuk sledovat, kdy se vstup neobsahuje žádný zvuk, zadejte hodnotu "InsertSilenceIfNoAudio".

Můžete využít některé z přednastavení MES zdokumentována [to](media-services-mes-presets-overview.md) části a proveďte následující změny:

### <a name="json-preset"></a>Přednastavení JSON
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>Přednastavení XML
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Zakázat automatické zrušte prokládání.
Zákazníci, nemusíte nic dělat, když se jako obsah prokládání být automaticky zrušte prokládaných. Prokládání zrušte automaticky je na (výchozí) MES nepodporuje automatické zjišťování prokládaných snímků a pouze zrušte interlaces označena jako prokládaných rámce.

Můžete vypnout prokládání zrušte automaticky. Tato možnost se nedoporučuje.

### <a name="json-preset"></a>Přednastavení JSON
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>Přednastavení XML
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Pouze přednastavení
V této části ukážeme dvou pouze MES přednastavení: AAC zvuk a AAC dobrý kvalitu zvuku.

### <a name="aac-audio"></a>AAC zvuk
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

### <a name="aac-good-quality-audio"></a>AAC kvalitních zvuk
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

## <a id="concatenate"></a>Řetězení dvou nebo více souborů video

Následující příklad ilustruje, jak můžete vygenerovat přednastavení ke zřetězení dvou nebo více souborů videa. Nejběžnější scénáře je, pokud chcete přidat hlavičku nebo vozidel hlavní video. Slouží při video soubory upravovaný společně sdílet vlastnosti (rozlišení videa, obnovovací frekvence, zvuk sledovat počet atd.). By měl třeba dbát na to kombinovat videa různý kmitočet, nebo mají jiný počet zvukových stop.

>[!NOTE]
>Současný návrh zřetězení funkce očekává, že jsou vstupní videosoubory konzistentní z hlediska řešení, obnovovací frekvence atd. 

### <a name="requirements-and-considerations"></a>Požadavky a důležité informace

* Vstupní videa by měla mít pouze jeden zvuk sledovat.
* Vstup videa, by měly mít stejnou snímků za sekundu.
* Musíte nahrát videa do samostatné prostředky a videím nastavit jako primární soubor v každé asset.
* Je třeba vědět trvání videa.
* Přednastavené následující příklady předpokládá, že všechny vstupní videa spustit s časovým razítkem nula. Budete muset upravit hodnoty StartTime, pokud videím mít různé výchozí časové razítko, což je obvykle případ s archivy za provozu.
* Přednastavení JSON umožňuje explicitní odkazy na hodnoty ID vstupní prostředků.
* Ukázkový kód předpokládá, že přednastavených JSON byla uložena do místního souboru, jako je například "C:\supportFiles\preset.json". Předpokládá také, zda byly vytvořeny dva prostředky tím, že nahrajete dva video soubory a že znáte výsledné hodnoty ID.
* Fragment kódu a JSON přednastavených ukazuje příklad zřetězení dva video soubory. Můžete ji rozšířit do více než dva videa pomocí:

  1. Úloha volání. InputAssets.Add() opakovaně Chcete-li přidat další videa, v pořadí.
  2. Provedení odpovídající upraví "Zdroje" elementu v kódu JSON, můžete přidat další položky ve stejném pořadí.

### <a name="net-code"></a>Kód .NET

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

### <a name="json-preset"></a>Přednastavení JSON

Aktualizujte vaše vlastní předvolby s ID prostředků, které chcete zřetězit a s příslušnou dobu segmentu pro každý video.

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

## <a id="crop"></a>Oříznutí videa pomocí procesoru Media Encoder Standard
Najdete v článku [oříznout videa pomocí procesoru Media Encoder Standard](media-services-crop-video.md) tématu.

## <a id="no_video"></a>Vložit video sledovat při vstupu je žádné video

Ve výchozím nastavení Pokud odesíláte vstup kodéru, který obsahuje pouze zvuk a obraz, pak výstupní asset obsahuje soubory, které obsahují pouze zvuková data. Některé přehrávače, včetně Azure Media Player (viz [to](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nemusí být schopná zpracovat takové datové proudy. Toto nastavení slouží k vynucení kodér přidat černobílý video sledovat ve výstupu v tomto scénáři.

> [!NOTE]
> Vynucení kodér vložit výstupu videa sledovat zvětšuje velikost výstupní Asset, a tím vám účtovány náklady pro úlohy kódování. Byste měli spustit testy ověření, že toto výsledné zvýšení má pouze mírné vliv na vaše měsíční poplatky.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Vložení videa na pouze nejnižší přenosovou rychlostí

Předpokládejme, že jsou pomocí více kódování přenosovou rychlostí, jako přednastavení ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) ke kódování celý vstupní katalogu pro streamování, obsahující směs video soubory a soubory jen zvukovém souboru. V tomto scénáři při ve vstupu je žádné video, můžete vynutit kodér vložte černobílý video sledovat na nejnižší přenosovou oproti vložení videa na každou výstupní přenosovou rychlostí. Jak toho docílit, budete muset použít **InsertBlackIfNoVideoBottomLayerOnly** příznak.

Můžete využít některé z přednastavení MES zdokumentována [to](media-services-mes-presets-overview.md) části a proveďte následující změny:

#### <a name="json-preset"></a>Přednastavení JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Přednastavení XML

Při použití XML, použít podmínku = "InsertBlackIfNoVideoBottomLayerOnly" jako atribut k **H264Video** elementu a podmínky = "InsertSilenceIfNoAudio" jako atribut k **AACAudio**.

```
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

### <a name="inserting-video-at-all-output-bitrates"></a>Vkládání video vůbec výstup přenosových rychlostí
Předpokládejme, že jsou pomocí více kódování přenosovou rychlostí, jako přednastavení ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) ke kódování celý vstupní katalogu pro streamování, obsahující směs video soubory a soubory jen zvukovém souboru. V tomto scénáři při ve vstupu je žádné video, můžete vynutit kodér vložit černobílý video sledovat vůbec přenosových rychlostí výstup. To zajistí, že výstupu prostředky jsou všechny informace, které bylo zajištěno homogenní, s ohledem na počet sleduje videa a audia sleduje. Jak toho docílit, je třeba zadat příznak "InsertBlackIfNoVideo".

Můžete využít některé z přednastavení MES zdokumentována [to](media-services-mes-presets-overview.md) části a proveďte následující změny:

#### <a name="json-preset"></a>Přednastavení JSON
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Přednastavení XML

Při použití XML, použít podmínku = "InsertBlackIfNoVideo" jako atribut k **H264Video** elementu a podmínky = "InsertSilenceIfNoAudio" jako atribut k **AACAudio**.

```
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

## <a id="rotate_video"></a>Otočit video
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje oběh podle úhly 0/90 nebo 180 nebo 270. Výchozí chování je "Auto", kde se pokusí zjistit metadata otočení příchozí videosoubor a kompenzovat ho. Patří **zdroje** element pro jedno z přednastavení definované v [to](media-services-mes-presets-overview.md) části:

### <a name="json-preset"></a>Přednastavení JSON
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
### <a name="xml-preset"></a>Přednastavení XML
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Další informace naleznete v [to](media-services-mes-schema.md#PreserveResolutionAfterRotation) téma pro další informace o interpretace kodéru: nastavení šířky a výšky v přednastavení, při aktivaci otočení honoráře.

Hodnota "0" můžete použít k označení kodéru ignorovat otočení metadata, pokud je k dispozici v vstup videa.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Kódování Přehled služby Media Services](media-services-encode-asset.md)
