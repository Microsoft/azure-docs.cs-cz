---
title: Postup generování miniatur pomocí kodéru Media Encoder Standard a .NET
description: Toto téma ukazuje, jak pomocí .NET kódování prostředku a generování miniatur pomocí kodéru Media Encoder Standard současně.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 9f717f0ae70c503d3527d5df2e6556c120146f3b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249305"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Postup generování miniatur pomocí kodéru Media Encoder Standard a .NET

Můžete generovat jednu nebo více miniatur z vaše vstupní video v pomocí kodéru Media Encoder Standard [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), nebo [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formátů souboru obrázku. Můžete odeslat úlohy, které vytvářejí pouze obrázky, nebo můžete kombinovat generování miniatur pomocí kódování. Tento článek obsahuje několik ukázky XML a JSON miniatur předvolby pro takové scénáře. Na konci tohoto článku je [ukázkový kód](#code_sample) , který ukazuje, jak provádět úlohu kódování pomocí Media Services .NET SDK.

Další podrobnosti o prvky, které se používají v ukázkové přednastavení, měli byste si přečíst [schéma Media Encoderu Standard](media-services-mes-schema.md).

Přečtěte si [aspekty](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) oddílu.
    
## <a name="example-of-a-single-png-file-preset"></a>Příklad přednastavení "jeden soubor PNG"

Následující přednastavený kontext JSON a XML slouží k vytvoření jeden výstupní soubor PNG z prvních několik sekund vstupním videu, kde kodér díky best effort pokus hledání "zajímavý" snímek. Všimněte si, že výstup rozměry obrázku jsou nastavené na 100 %, což znamená, že byly stejné rozměry vstupního videa. Všimněte si také, jak je nutné nastavit "Formát" v "Výstupy" tak, aby odpovídaly použití "PngLayers" v části "Kodeky". 

### <a name="json-preset"></a>Přednastavení JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>Přednastavení XML

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Příklad přednastavení "řadu obrázků JPEG"

Následující nastavení JSON a XML slouží k vytvoření sadu 10 obrázků na časová razítka 5 čtvrtletí % 15 %,..., 95 % vstupní časové osy, kde je zadán velikost bitové kopie se u vstupního videa.

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

### <a name="xml-preset"></a>Přednastavení XML
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Příklad přednastavení "jednu image na konkrétní časové razítko"

Následující nastavení JSON a XML slouží k vytvoření jedné image ve formátu JPEG na nulté sekundě 30 videí vstupu. Tato předvolba očekává, že na více než 30 sekund doby trvání vstupního videa (jiná úloha selže).

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

### <a name="xml-preset"></a>Přednastavení XML
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Příklad přednastavení "miniatur na různá řešení"

Následující nastavení je možné vytvářet miniatury na různá řešení v jednom úkolu. V příkladu v umístění % 5 15 %,..., 95 % vstupní časové osy, kodér generuje dvě Image – jednu na 100 % vstupního rozlišení videí a jiných ve výši 50 %.

Všimněte si použití makra {rozlišení} v názvu souboru; označuje kodér používat šířku a výšku, které jste vytvořili v části kódování předvolby při generování názvu souboru výstupu imagí. To zároveň pomáhá snadno rozlišit různé obrázky

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

### <a name="xml-preset"></a>Přednastavení XML
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Příklad generování miniatury při kódování

Přestože všechny výše uvedené příklady mají popsáno, jak můžete odeslat úlohu kódování, pouze vytvoří Image, můžete také kombinovat, kódování videa a zvuku s generování miniatur. Sdělte následující JSON a XML přednastavení **kodéru Media Encoder Standard** ke generování miniatury při kódování.

### <a id="json"></a>Přednastavení JSON
Informace o schématu najdete v tématu [to](https://msdn.microsoft.com/library/mt269962.aspx) článku.

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

### <a id="xml"></a>Přednastavení XML
Informace o schématu najdete v tématu [to](https://msdn.microsoft.com/library/mt269962.aspx) článku.

```csharp
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
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a id="code_sample"></a>Kódování videa a generování miniatur pomocí .NET

Následující příklad kódu používá sadu Media Services .NET SDK k provádění následujících úloh:

* Vytvořte úlohu kódování.
* Získání odkazu na kodéru Media Encoder Standard.
* Načíst Předvolby [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) nebo [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) , které obsahují kódování přednastavení a také informace potřebné ke generování miniatur. To můžete uložit [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) nebo [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) na soubor a použít následující kód k načtení souboru.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Přidáte jednoho kódování úkolu do úlohy. 
* Zadejte vstupní asset kódovaný.
* Vytvoření výstupní asset, který obsahuje zakódovanému assetu.
* Přidáte obslužnou rutinu události chcete zkontrolovat průběh úlohy.
* Odeslání úlohy.

Najdete v článku [vývoj pro Media Services s .NET](media-services-dotnet-how-to-use.md) článku Pokyny o tom, jak nastavit vývojové prostředí.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
                    TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="considerations"></a>Požadavky
Platí následující aspekty:

* Použití explicitního časová razítka pro začátek/krok/rozsahu předpokládá, že vstupní zdroj je dlouhý alespoň 1 minuta.
* JPG nebo Png/BmpImage elementy mají počáteční krok a být v rozsahu atributy řetězce – to může být interpretován jako:
  
  * Číslo snímku, pokud jsou nezáporná celá čísla, například "Start": "120",
  * Vzhledem ke zdrojové doby trvání, pokud je vyjádřená s příponou %, například "Start": "15 %", nebo
  * Časové razítko, pokud je vyjádřená jako hh: mm:... formát. Například "Start": "00: 01:00"
    
    Můžete kombinovat a párovat zápisy, jako je prosím.
    
    Kromě toho Start také podporuje speciálního makra: {osvědčené}, která se pokusí určit prvního "zajímavý" rámce obsahu Poznámka: (krok a rozsahu jsou ignorovány při spuštění nastavený na {nejlepší})
  * Výchozí: Spustit: {nejlepší}
* Výstupní formát musí být explicitně zadat pro každou formát obrázku: Jpg nebo Png/BmpFormat. Pokud je přítomen, odpovídá MES JpgVideo k JpgFormat a tak dále. OutputFormat zavádí nové makro konkrétní kodek obrázků: {Index}, které musí být k dispozici (jednou a jen jednou) pro formáty výstupu bitové kopie.

## <a name="next-steps"></a>Další postup

Můžete zkontrolovat [průběh úlohy](media-services-check-job-progress.md) během úlohy kódování čeká na vyřízení.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Media Services kódování – přehled](media-services-encode-asset.md)

