---
title: Proveďte pokročilé kódování přizpůsobením přednastavení MES | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak provádět pokročilé kódování přizpůsobením přednastavení úloh kodéru médií.
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
ms.openlocfilehash: 5f7611fd9df207df51fa0e51218d8a234583b1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529779"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Proveďte pokročilé kódování přizpůsobením přednastavení MES 

## <a name="overview"></a>Přehled

Toto téma ukazuje, jak přizpůsobit přednastavení standardu kodéru médií. Kódování [pomocí standardu kodéru médií pomocí vlastního přednastavení](media-services-custom-mes-presets-with-dotnet.md) ukazuje, jak pomocí rozhraní .NET vytvořit úlohu kódování a úlohu, která tuto úlohu provede. Jakmile si upravíte přednastavení, zadejte vlastní přednastavení úlohy kódování. 

Pokud používáte přednastavení XML, ujistěte se, že je zachováno pořadí prvků, jak je znázorněno ve vzorcích XML níže (například KeyFrameInterval by měl předcházet SceneChangeDetection).

> [!NOTE] 
> Mnoho pokročilých funkcí media services v2 standardu Media Encoder Standard není v současné době k dispozici ve verze 3. Další informace naleznete v tématu [mezery funkcí](https://docs.microsoft.com/azure/media-services/latest/media-services-v2-vs-v3#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Podpora relativních velikostí

Při generování miniatur není nutné vždy určovat šířku a výšku výstupu v obrazových bodech. Můžete je zadat v procentech v rozsahu [1%, ..., 100%.).

### <a name="json-preset"></a>Přednastavení JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Přednastavení XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="generate-thumbnails"></a><a id="thumbnails"></a>Generovat miniatury

V této části je uvedeno, jak přizpůsobit přednastavení, které generuje miniatury. Níže definované přednastavení obsahuje informace o tom, jak chcete soubor zakódovat, a také informace potřebné ke generování miniatur. Můžete vzít libovolné přednastavení MES zdokumentované [v této](media-services-mes-presets-overview.md) části a přidat kód, který generuje miniatury.  

> [!NOTE]
> Nastavení **SceneChangeDetection** v následujícím přednastavení lze nastavit na hodnotu true pouze v případě, že kódujete jedno bitové video. Pokud kódujete video s více přenosovými rychlostmi a **nastavíte aplikaci SceneChangeDetection** na hodnotu true, kodér vrátí chybu.  
>
>

Informace o schématu naleznete v [tomto](media-services-mes-schema.md) tématu.

Přečtěte si část [Důležité informace.](#considerations)

### <a name="json-preset"></a><a id="json"></a>Přednastavení JSON
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


### <a name="xml-preset"></a><a id="xml"></a>Přednastavení XML
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

### <a name="considerations"></a>Požadavky

Platí následující důležité informace:

* Použití explicitní časová razítka pro Start/Krok/Rozsah předpokládá, že vstupní zdroj je alespoň 1 minutu dlouhý.
* Jpg/Png/BmpImage prvky mají Start, Step, a Range řetězce atributy - tyto mohou být interpretovány jako:

  * Číslo snímku, pokud jsou nezáporná celá čísla, například "Start": "120",
  * Vzhledem k době trvání zdroje, pokud je vyjádřena jako %-suffixed, například "Start": "15%", NEBO
  * Časové razítko, pokud je vyjádřeno jako HH:MM:SS... formát, například "Start" : "00:01:00"

    Můžete kombinovat notace, jak se vám zlíbí.

    Kromě toho Start také podporuje speciální makro:{Best}, který se pokouší určit první "zajímavý" snímek obsahu POZNÁMKA: (Krok a rozsah jsou ignorovány, když je start nastaven na {Best})
  * Výchozí hodnoty: Start:{Best}
* Výstupní formát musí být explicitně k dispozici pro každý formát obrázku: Jpg/Png/BmpFormat. Pokud je přítomen, MES zápasy JpgVideo na JpgFormat a tak dále. OutputFormat zavádí nové makro specifické pro kodek obrázku: {Index}, které musí být přítomno (jednou a pouze jednou) pro výstupní formáty obrazu.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Oříznutí videa (oříznutí)
V této části se hovoří o úpravě přednastavení kodéru pro oříznutí nebo oříznutí vstupního videa, kde je vstupem takzvaný mezaninový soubor nebo soubor na vyžádání. Kodér lze také použít k oříznutí nebo oříznutí datového zdroje, který je zachycen nebo archivován z živého přenosu – podrobnosti jsou k dispozici v [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Chcete-li videa oříznout, můžete vzít libovolné předvolby MES zdokumentované [v této](media-services-mes-presets-overview.md) části a upravit prvek **Zdroje** (jak je znázorněno níže). Hodnota StartTime musí odpovídat absolutní časová razítka vstupní video. Pokud má například první snímek vstupního videa časové razítko 12:00:10.000, měl by být StartTime alespoň 12:00:10.000 a vyšší. V níže uvedeném příkladu předpokládáme, že vstupní video má počáteční časové razítko nula. Zdroje by **měly** být umístěny na začátku přednastavení.

### <a name="json-preset"></a><a id="json"></a>Přednastavení JSON
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
Chcete-li svá videa oříznout, můžete si vzít libovolné předvolby MES [zde](media-services-mes-presets-overview.md) zdokumentované a upravit prvek **Zdroje** (jak je znázorněno níže).

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

## <a name="create-an-overlay"></a><a id="overlay"></a>Vytvoření překrytí

Standard kodéru médií umožňuje překrýt obraz na existující video. V současné době jsou podporovány následující formáty: png, jpg, gif a bmp. Přednastavené níže je základním příkladem překrytí videa.

Kromě definování přednastaveného souboru musíte také dát službě Media Services vědět, který soubor v datovém zdroji je překryvný obraz a který soubor je zdrojové video, na které chcete obrázek překrýt. Video soubor musí být **primární** soubor.

Pokud používáte rozhraní .NET, přidejte následující dvě funkce do příkladu .NET [definovaného](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) v tomto tématu. **Funkce UploadMediaFilesFromFolder** nahrává soubory ze složky (například BigBuckBunny.mp4 a Image001.png) a nastaví soubor mp4 jako primární soubor v datovém zdroji. Funkce **EncodeWithOverlay** používá k vytvoření úlohy kódování vlastní přednastavený soubor, který mu byl předán (například následující přednastavení).


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
> Zdrojový soubor videa a soubor překryvné bitové kopie musí být ve stejném datovém zdroji a soubor videa musí být nastaven jako primární soubor v tomto datovém zdroji.
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


### <a name="xml-preset"></a>Přednastavení XML
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


## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Vložení tiché zvukové stopy, pokud vstup nemá žádný zvuk
Ve výchozím nastavení, pokud odešlete vstup do kodéru, který obsahuje pouze video a žádný zvuk, pak výstupní datový zdroj obsahuje soubory, které obsahují pouze data videa. Někteří hráči nemusí být schopni zpracovat tyto výstupní datové proudy. Toto nastavení můžete použít k vynucení kodéru přidat tichou zvukovou stopu do výstupu v tomto scénáři.

Chcete-li vynutit kodér k vytvoření datového zdroje, který obsahuje tichou zvukovou stopu, když vstup nemá žádný zvuk, zadejte hodnotu InsertSilenceIfNoAudio.

Můžete vzít libovolné přednastavení MES zdokumentované v [této](media-services-mes-presets-overview.md) části a provést následující změny:

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

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Zakázat automatické odstranění prokládání
Zákazníci nemusí dělat nic, pokud se jim líbí prokládání obsah, který má být automaticky de-prokládání. Když je automatické odstranění prokládání zapnuté (výchozí), MES provádí automatickou detekci prokládaných snímků a pouze de-prokládání snímků označených jako prokládané.

Automatické deprokládání můžete vypnout. Tato možnost se nedoporučuje.

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


## <a name="audio-only-presets"></a><a id="audio_only"></a>Přednastavení pouze pro zvuk
Tato část ukazuje dvě přednastavení MES pouze pro zvuk: AAC Audio a AAC Good Quality Audio.

### <a name="aac-audio"></a>AAC Audio
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

### <a name="aac-good-quality-audio"></a>AAC kvalitní zvuk
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

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Zřetězit dva nebo více video souborů

Následující příklad ukazuje, jak můžete vygenerovat přednastavení pro zřetězení dvou nebo více video souborů. Nejběžnější scénář je, když chcete přidat záhlaví nebo trailer k hlavnímu videu. Zamýšlené použití je, když video soubory upravované společně sdílejí vlastnosti (rozlišení videa, kmitočet snímků, počet zvukových stop atd.). Měli byste dbát na to, abyste nekomprominujte videa s různými snímkovými frekvencemi nebo s různým počtem zvukových stop.

>[!NOTE]
>Současný návrh funkce zřetězení očekává, že vstupní videoklipy jsou konzistentní, pokud jde o rozlišení, kmitočet snímků atd. 

### <a name="requirements-and-considerations"></a>Požadavky a úvahy

* Vstupní videa by měla mít pouze jednu zvukovou stopu.
* Vstupní videa by měla mít stejnou kmitočet snímků.
* Videa musíte nahrát do samostatných datových zdrojů a nastavit je jako primární soubor v každém podkladu.
* Musíte znát dobu trvání vašich videí.
* Níže uvedené přednastavené příklady předpokládají, že všechna vstupní videa začínají časovým razítkem nula. Hodnoty StartTime je třeba upravit, pokud mají videa jiné počáteční časové razítko, jak je tomu obvykle u živých archivů.
* Přednastavení JSON obsahuje explicitní odkazy na hodnoty AssetID vstupních aktiv.
* Ukázkový kód předpokládá, že přednastavení JSON bylo uloženo do místního souboru, například "C:\supportFiles\preset.json". Také předpokládá, že dva datové zdroje byly vytvořeny nahráním dvou video souborů a že znáte výsledné hodnoty AssetID.
* Fragment kódu a přednastavení JSON ukazují příklad zřetězení dvou video souborů. Můžete ji rozšířit na více než dvě videa:

  1. Volající úkol. InputAssets.Add() opakovaně přidávat další videa, v pořadí.
  2. Provádění odpovídající úpravy "Zdroje" prvek v JSON přidáním další položky, ve stejném pořadí.

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

Aktualizujte vlastní přednastavení pomocí ID datových zdrojů, které chcete zřetězit, a příslušným časovým segmentem pro každé video.

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

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Oříznutí videa pomocí kodéru Media Encoder Standard
Podívejte se na téma Oříznutí videí s tématem [Standard kodéru médií.](media-services-crop-video.md)

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Vložení stopy videa, pokud vstup nemá žádné video

Ve výchozím nastavení, pokud odešlete vstup do kodéru, který obsahuje pouze zvuk a žádné video, pak výstupní datový zdroj obsahuje soubory, které obsahují pouze zvuková data. Některé přehrávače, včetně Azure Media Player (viz [toto)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)nemusí být schopen zpracovat tyto datové proudy. Toto nastavení můžete použít k vynucení kodéru přidat monochromatické video stopy do výstupu v tomto scénáři.

> [!NOTE]
> Vynucení kodéru vložit výstup video stopy zvyšuje velikost výstupního assetu a tím i náklady vynaložené na úlohu kódování. Měli byste spustit testy k ověření, že toto výsledné zvýšení má pouze mírný dopad na měsíční poplatky.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Vložení videa pouze při nejnižším toku

Předpokládejme, že používáte přednastavení kódování více datových toku, například ["H264 Multiple Bitrate 720p",](media-services-mes-preset-h264-multiple-bitrate-720p.md) abyste zakódovali celý vstupní katalog pro streamování, který obsahuje kombinaci video souborů a souborů pouze zvuku. V tomto scénáři, pokud vstup nemá žádné video, můžete vynutit kodér vložit monochromatické video stopy na nejnižší přenosový tok, na rozdíl od vkládání videa na každém výstupu přenosovou rychlost. Chcete-li toho dosáhnout, musíte použít **InsertBlackIfIfNoVideoBottomLayerOnly** příznak.

Můžete vzít libovolné přednastavení MES zdokumentované v [této](media-services-mes-presets-overview.md) části a provést následující změny:

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

Při použití XML použijte condition="InsertBlackIfNoVideoBottomLayerOnly" jako atribut prvku **H264Video** a Condition="InsertSilenceIfNoAudio" jako atribut **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Vkládání videa při všech výstupních přenosových rychlostech
Předpokládejme, že používáte přednastavení kódování více datových toku, například ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) pro kódování celého vstupního katalogu pro streamování, který obsahuje kombinaci video souborů a souborů pouze zvuku. V tomto scénáři, pokud vstup nemá žádné video, můžete vynutit kodér vložit monochromatické video stopy na všechny výstupní přenosové rychlosti. Tím je zajištěno, že vaše výstupní datové zdroje jsou všechny homogenní s ohledem na počet stop videa a zvukových stop. Chcete-li toho dosáhnout, musíte zadat příznak "InsertBlackIfNoVideo".

Můžete vzít libovolné přednastavení MES zdokumentované v [této](media-services-mes-presets-overview.md) části a provést následující změny:

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

Při použití XML použijte condition="InsertBlackIfNoVideo" jako atribut prvku **H264Video** a Condition="InsertSilenceIfNoAudio" jako atribut **AACAudio**.

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

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Otočení videa
Standard [media encoder podporuje](media-services-dotnet-encode-with-media-encoder-standard.md) otáčení úhlem 0/90/180/270. Výchozí chování je "Auto", kde se snaží zjistit metadata rotace v příchozí video souboru a kompenzovat ji. Do jednoho z přednastavení definovaných v [této](media-services-mes-presets-overview.md) části zahrňte následující element **Zdroje:**

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

Další informace o tom, jak kodér interpretuje nastavení Šířka a výška v přednastavení při aktivaci kompenzace otočení, naleznete v [tomto](media-services-mes-schema.md#PreserveResolutionAfterRotation) tématu.

Hodnotu "0" můžete použít k označení kodéru ignorovat metadata otočení, pokud jsou k dispozici, ve vstupním videu.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled kódování mediálních služeb](media-services-encode-asset.md)
