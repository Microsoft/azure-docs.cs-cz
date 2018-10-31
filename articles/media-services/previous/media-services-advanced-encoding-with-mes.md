---
title: Pokročilé kódování pomocí přizpůsobení předvoleb MES | Dokumentace Microsoftu
description: Toto téma ukazuje, jak provádět pokročilé kódování pomocí přizpůsobení předvoleb Media Encoderu Standard úloh.
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
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 4601628fd0fbdbd80c2b159a4578e25cb4e3c4c5
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250763"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Pokročilé kódování přizpůsobením přednastavení MES 

## <a name="overview"></a>Přehled

Toto téma ukazuje, jak přizpůsobení předvoleb Media Encoderu Standard. [Kódování pomocí Media Encoderu Standard s využitím vlastní předvolby](media-services-custom-mes-presets-with-dotnet.md) téma ukazuje, jak pomocí .NET vytvořit úlohu kódování a úlohu, která spustí tuto úlohu. Jakmile upravíte přednastavení, zadejte vlastní předvolby pro úlohu kódování. 

>[!NOTE]
>Pokud používáte přednastavení XML, ujistěte se, že chcete zachovat pořadí prvků, jak je znázorněno v následující ukázky XML (například KeyFrameInterval by měl předcházet SceneChangeDetection).
>

V tomto tématu na vlastní předvolby, které provádějí následující úkoly kódování je ukázán.

## <a name="support-for-relative-sizes"></a>Podpora pro relativní velikosti

Při generování miniatur, není potřeba vždy určete výstupní šířku a výšku v pixelech. Můžete je zadat v procentech v rozsahu [1 %,..., 100 %].

### <a name="json-preset"></a>Přednastavení JSON
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>Přednastavení XML
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Generování miniatur

Tato část ukazuje, jak přizpůsobit přednastavení, která generuje miniatury. Přednastavení definovaná níže obsahuje informace o tom, jak chcete zakódovat váš soubor, jakož i informace potřebné ke generování miniatur. Můžete využít některý z přednastavení MES zdokumentované [to](media-services-mes-presets-overview.md) a přidejte kód, který generuje miniatury.  

> [!NOTE]
> **SceneChangeDetection** nastavení do následujícího nastavení lze upravit pouze na hodnotu true, pokud se kódování s jednou přenosovou rychlostí videa. Pokud se kódování videa s více přenosovými rychlostmi a nastavte **SceneChangeDetection** na hodnotu true, vrátí kodér chybu.  
>
>

Informace o schématu najdete v tématu [to](media-services-mes-schema.md) tématu.

Přečtěte si [aspekty](#considerations) oddílu.

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

* Použití explicitního časová razítka pro začátek/krok/rozsahu předpokládá, že vstupní zdroj je dlouhý alespoň 1 minuta.
* JPG nebo Png/BmpImage elementy mají počáteční krok a být v rozsahu atributy řetězce – to může být interpretován jako:

  * Číslo snímku, pokud jsou nezáporná celá čísla, například "Start": "120",
  * Vzhledem ke zdrojové doby trvání, pokud je vyjádřená s příponou %, například "Start": "15 %", nebo
  * Časové razítko, pokud je vyjádřená jako hh: mm:... formátování, například "Start": "00: 01:00"

    Můžete kombinovat a párovat zápisy, jako je prosím.

    Kromě toho Start také podporuje speciálního makra: {osvědčené}, která se pokusí určit prvního "zajímavý" rámce obsahu Poznámka: (krok a rozsahu jsou ignorovány při spuštění nastavený na {nejlepší})
  * Výchozí: Spustit: {nejlepší}
* Výstupní formát musí být explicitně zadat pro každou formát obrázku: Jpg nebo Png/BmpFormat. Pokud je přítomen, odpovídá MES JpgVideo k JpgFormat a tak dále. OutputFormat zavádí nové makro konkrétní kodek obrázků: {Index}, které musí být k dispozici (jednou a jen jednou) pro formáty výstupu bitové kopie.

## <a id="trim_video"></a>Střih videa (výstřižek)
Tato část pojednává o Úprava přednastavení kodér klipu nebo oříznout vstupního videa, kde je vstupní soubor mezzanine takzvané nebo soubor na vyžádání. Kodér také umožňuje oříznout nebo oříznout prostředku, která je zachycena nebo archivovat v živém datovém proudu – podrobnosti k tomu je k dispozici v [tento blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Oříznout videí, můžete provést některý z přednastavení MES zdokumentované [to](media-services-mes-presets-overview.md) oddílu a upravit **zdroje** – element (jak je vidět níže). Hodnota StartTime musí odpovídat absolutní časové razítko vstupního videa. Například pokud první snímek vstupní video má časové razítko 12:00:10.000, pak StartTime by měl být alespoň 12:00:10.000 nebo novější. V následujícím příkladu předpokládáme, že vstupní video má výchozí časové razítko nula. **Zdroje** by měl být umístěny na začátku přednastavený kontext.

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
Oříznout videí, můžete provést některý z přednastavení MES zdokumentované [tady](media-services-mes-presets-overview.md) a upravovat **zdroje** – element (jak je vidět níže).

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

## <a id="overlay"></a>Vytvořit překrytí

Media Encoder Standard vám umožní překryv image do existujícího videa. V současné době jsou podporovány následující formáty: png, jpg, gif a bmp. Předvolba definovaná níže je příklad základní překryvné video.

Kromě definování soubor přednastavení, máte také umožní Media Services vědět, který soubor v prostředku je obrázek překrytí a soubor, který je zdrojem videa do kterého chcete překrýt bitovou kopii. Soubor videa musí být **primární** souboru.

Pokud používáte .NET, přidejte tyto dvě funkce definované v příkladu .NET [to](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tématu. **UploadMediaFilesFromFolder** funkce nahraje soubory ze složky (například BigBuckBunny.mp4 a Image001.png) a nastaví soubor mp4 primární soubor v prostředku. **EncodeWithOverlay** funkce používá vlastní předvolby soubor, který byl předán (například přednastavený kontext, který následuje) Chcete-li vytvořit úlohu kódování.


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
> Nastavení překrytí krytí není podporováno.
>
> Zdrojový soubor videa a překrytí soubor bitové kopie musí být ve stejném prostředku a souboru videa se musí nastavit jako primární soubor do tohoto prostředku.
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


## <a id="silent_audio"></a>Vložit tiché zvuková stopa, pokud nemá žádný zvukový vstup
Ve výchozím nastavení Pokud odesíláte vstupní hodnota pro kodér, který obsahuje pouze video a zvuk, pak výstupního prostředku obsahuje soubory, které obsahují pouze videa data. Některé přehrávače nemusí být schopna zpracovávat takové výstupní datové proudy. Toto nastavení slouží k vynucení kodéru pro přidání do výstupu v tomto scénáři tiché zvuková stopa.

K vynucení kodér vytvořit asset, který obsahuje tiché zvukové stopy po žádný zvukový vstup, zadejte hodnotu "InsertSilenceIfNoAudio".

Můžete využít některý z přednastavení MES dokumentovány v článku [to](media-services-mes-presets-overview.md) části a provést následující změny:

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

## <a id="deinterlacing"></a>Zakázat automatické zrušení prokládání.
Zákazníci nemusí nic dělat, když chtějí prokládání obsah bude automaticky zruší prokládané. Po prokládání Zrušení automatického (výchozí) systém řízení výroby provádí automatické zjišťování prokládaných snímků a pouze zrušení interlaces rámce s označením prokládané.

Můžete vypnout automatické zrušení prokládání. Tato možnost se nedoporučuje.

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


## <a id="audio_only"></a>Přednastavení pouze se zvukem
Tato část ukazuje dvě pouze se zvukem přednastavení MES: AAC zvuku a AAC vhodné kvality zvuku.

### <a name="aac-audio"></a>Zvuk AAC
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

### <a name="aac-good-quality-audio"></a>Zvuk kvalitních AAC
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

## <a id="concatenate"></a>Zřetězení dvou nebo více souborů videa

Následující příklad ukazuje, jak můžete generovat přednastavení ke zřetězení dvou nebo více souborů videa. Nejběžnější scénář je, pokud chcete přidat záhlaví nebo koncová část do hlavního videa. Zamýšlené použití je při videosoubory upravovaný společně sdílejí vlastnosti (rozlišení, frekvence snímků, zvukové stopy počet atd.). Které byste měli věnovat pozornost nechcete kombinovat videa z různých snímkových nebo s různým počtem zvukové stopy.

>[!NOTE]
>Současný návrh zřetězení funkce očekává, že vstupní videoklipy jsou konzistentní vzhledem k aplikacím z hlediska rozlišení, frekvence snímků atd. 

### <a name="requirements-and-considerations"></a>Požadavky a předpoklady

* Vstupního videa by měl mít jenom jedna zvuková stopa.
* Vstupního videa by měly mít stejnou frekvenci snímků.
* Musíte nahrání videa do samostatné prostředky a nastavit jako primární soubor v každé asset videa.
* Je potřeba vědět trvání videa.
* Níže přednastavených příklad vychází z předpokladu, že všechny vstupní videa spustit s časovým razítkem nula. Budete muset upravit hodnoty StartTime, pokud videa, která mají různé výchozí časové razítko, jako je tomu u živých obvykle.
* Přednastavení JSON umožňuje explicitní odkazy na hodnoty AssetID vstupní prostředků.
* Vzorový kód předpokládá, že přednastavení JSON se uložil do místního souboru, jako je například "C:\supportFiles\preset.json". Předpokládá také, zda byly vytvořeny dva prostředky tím, že nahrajete videa dva soubory, a znát výsledné hodnoty AssetID.
* Fragment kódu a JSON přednastavení ukazuje příklad zřetězení dvou souborů videa. Můžete ji rozšířit na více než dvě videa podle:

  1. Volání úlohy. InputAssets.Add() opakovaně přidáte další videa v pořadí.
  2. Prvek "Zdroje" ve formátu JSON, provádění odpovídající upraví tak, že přidáte další položky, ve stejném pořadí.

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

Aktualizujte vaše vlastní předvolbu s ID sady prostředků, které chcete zřetězit a segment správný čas pro každé video.

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

## <a id="crop"></a>Oříznutí videa pomocí kodéru Media Encoder Standard
Zobrazit [oříznutí videa pomocí kodéru Media Encoder Standard](media-services-crop-video.md) tématu.

## <a id="no_video"></a>Vložení videa sledovat při vstupu nemá žádné video

Ve výchozím nastavení Pokud odesíláte vstupní hodnota pro kodér, který obsahuje pouze zvuku a žádná videa, pak výstupní asset obsahuje soubory, které obsahují pouze zvuková data. Některé přehrávače, včetně Azure Media Playeru (viz [to](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nemusí být schopna zpracovávat takové datové proudy. Toto nastavení slouží k vynucení kodéru pro přidání monochromatický sledovat videa do výstupu v tomto scénáři.

> [!NOTE]
> Vynucení kodéru pro vložení videa sledovat výstupu zvýší velikost výstupu Asset, a tím náklady vzniklé pro úlohu kódování. Měli byste spustit testy k ověření, že toto výsledné zvýšení má pouze mírné vliv na vaše měsíční poplatky.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Vložení videa v pouze nejnižší přenosové rychlosti

Předpokládejme, že používáte více přenosovými rychlostmi kódování přednastavení, jako ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) ke kódování celý vstupní katalogu pro streamování, která obsahuje kombinaci videosoubory a zvukové soubory. V tomto scénáři při vstupu nemá žádná videa, můžete chtít vynutit kodér vložit monochromatický sledovat videa na nejnižší bitrate na rozdíl od vkládání videa na každou výstupní s přenosovou rychlostí. K dosažení tohoto cíle, budete muset použít **InsertBlackIfNoVideoBottomLayerOnly** příznak.

Můžete využít některý z přednastavení MES dokumentovány v článku [to](media-services-mes-presets-overview.md) části a provést následující změny:

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

Při použití XML, použijte podmínku = "InsertBlackIfNoVideoBottomLayerOnly" jako atribut pro **H264Video** elementu a podmínka = "InsertSilenceIfNoAudio" jako atribut na **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Vložení videa vůbec výstup přenosových rychlostí
Předpokládejme, že používáte více přenosovými rychlostmi kódování přednastavení, jako ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) ke kódování celý vstupní katalogu pro streamování, která obsahuje kombinaci videosoubory a zvukové soubory. V tomto scénáři při vstupu nemá žádná videa, můžete chtít vynutit kodéru pro vložení monochromatický sledovat videa vůbec přenosových rychlostí výstup. To zajistí, že výstup prostředky jsou všechny homogenní s ohledem na počet běhů videí a zvukových stop. K dosažení tohoto cíle, budete muset zadat příznak "InsertBlackIfNoVideo".

Můžete využít některý z přednastavení MES dokumentovány v článku [to](media-services-mes-presets-overview.md) části a provést následující změny:

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

Při použití XML, použijte podmínku = "InsertBlackIfNoVideo" jako atribut pro **H264Video** elementu a podmínka = "InsertSilenceIfNoAudio" jako atribut na **AACAudio**.

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

## <a id="rotate_video"></a>Otočit videa
[Kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) podporuje rotaci úhel 0/90/180 nebo 270. Výchozí chování je "Auto", kde se pokusí zjistit metadata otočení příchozí videosoubor a jako kompenzaci za to. Patří **zdroje** jedno z přednastavení definovaný v elementu [to](media-services-mes-presets-overview.md) části:

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

Viz také [to](media-services-mes-schema.md#PreserveResolutionAfterRotation) tématu pro další informace o jak kodér interpretuje nastavení šířky a výšky v nastavení, když se aktivuje otočení kompenzace.

Hodnotu "0" slouží k označení kodér ignorovat otočení metadat, pokud jsou k dispozici ve vstupním videu.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Media Services kódování – přehled](media-services-encode-asset.md)
