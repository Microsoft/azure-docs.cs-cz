---
title: Digitalizace textu pomocí ocr služby Azure Media Analytics | Dokumenty společnosti Microsoft
description: Azure Media Analytics OCR (optické rozpoznávání znaků) umožňuje převést textový obsah v video souborech do upravitelného digitálního textu, který lze prohledávat.  To umožňuje automatizovat extrakci smysluplných metadat z video signálu média.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918304"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Převod textového obsahu ve videosouborech na digitální text pomocí Azure Media Analytics  

> [!NOTE]
> Mediální procesor **Azure Media OCR** bude vyřazen. Datum vyřazení naleznete v tématu [starších součástí.](legacy-components.md)

## <a name="overview"></a>Přehled
Pokud potřebujete extrahovat textový obsah ze souborů videa a vygenerovat upravitelný digitální text, který lze prohledávat, měli byste použít OCR Azure Media Analytics (optické rozpoznávání znaků). Tento mediální procesor Azure detekuje textový obsah v souborech videa a generuje textové soubory pro vaše použití. Rozpoznávání OCR umožňuje automatizovat extrakci smysluplných metadat z videosignálu média.

Při použití ve spojení s vyhledávačem můžete snadno indexovat média podle textu a zlepšit zjistitelnost obsahu. To je velmi užitečné ve vysoce textové video, jako je nahrávání videa nebo screen-capture prezentace prezentace. Mediální procesor Azure OCR je optimalizovaný pro digitální text.

Mediální procesor **Azure Media OCR** je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media OCR** a ukazuje, jak ji používat s SadmMedia Media Services pro .NET. Další informace a příklady naleznete v [tomto blogu](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Vstupní soubory rozpoznávání OCR
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration"></a>Konfigurace úlohy
Konfigurace úlohy (přednastavení). Při vytváření úlohy pomocí **azure media ocr**je nutné zadat přednastavení konfigurace pomocí JSON nebo XML. 

>[!NOTE]
>Modul OCR přebírá pouze oblast obrázku s minimálně 40 pixelů na maximálně 32000 pixelů jako platný vstup v obou výšky/šířky.
>

### <a name="attribute-descriptions"></a>Popisy atributů
| Název atributu | Popis |
| --- | --- |
|Rozšířené výstup| Pokud nastavíte AdvancedOutput na true, výstup JSON bude obsahovat poziční data pro každé slovo (kromě frází a oblastí). Pokud nechcete zobrazit tyto podrobnosti, nastavte příznak na false. Výchozí hodnota je False. Další informace naleznete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Jazyk |(nepovinné) popisuje jazyk textu, pro který je třeba hledat. Jeden z následujících: AutoDetect (výchozí), arabština, čínštinaZjednoduše, čínštinaTradiční, česká dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, SrbštinaCyrillic, SrbskoLatin, Slovenská, Španělština, Švédština, Turečtina. |
| TextOrientace |(nepovinné) popisuje orientaci textu, pro který chcete hledat.  "Vlevo" znamená, že horní část všech písmen je namířena doleva.  Výchozí text (jako ten, který lze nalézt v knize) lze nazvat "Up" orientovaný.  Jeden z následujících: AutoDetect (výchozí), Nahoru, Vpravo, Dolů, Vlevo. |
| Časový interval |(nepovinné) popisuje vzorkovací frekvenci.  Výchozí hodnota je každých 1/2 sekundy.<br/>Formát JSON – HH:mm:ss. SSS (výchozí 00:00:00.500)<br/>Formát XML – Základní doba trvání W3C XSD (výchozí PT0.5) |
| DetectRegions |(nepovinné) Pole DetectRegion objekty určující oblasti v rámci videa, ve kterém chcete zjistit text.<br/>Objekt DetectRegion je tvořen následujícími čtyřmi hodnotami celého čísla:<br/>Vlevo – obrazové body z levého okraje<br/>Nahoře – obrazové body s horním okrajem<br/>Šířka – šířka oblasti v pixelech<br/>Výška – výška oblasti v pixelech |

#### <a name="json-preset-example"></a>Přednastavený příklad JSON

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Přednastavený příklad XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>Výstupní soubory OCR
Výstupem mediálního procesoru OCR je soubor JSON.

### <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON
Výstup Video OCR poskytuje časově segmentovaná data o postavách nalezených ve videu.  Můžete použít atributy, jako je jazyk nebo orientace zdokonalit-in na přesně slova, která máte zájem o analýzu. 

Výstup obsahuje následující atributy:

| Element | Popis |
| --- | --- |
| Timescale |"zaškrtávací tlačítko" za sekundu videa |
| Posun |časový posun pro časová razítka. Ve verzi 1.0 video API to bude vždy 0. |
| Framerate |Snímky za sekundu videa |
| šířka |šířka videa v pixelech |
| height |výška videa v pixelech |
| Fragments |pole časově založených bloků videa, do kterého jsou metadata rozdělena do bloků dat |
| start |počáteční čas fragmentu v "klíšťachách" |
| doba trvání |délka fragmentu v "klíšťachách" |
| interval |interval každé události v rámci daného fragmentu |
| stránka events |pole obsahující oblasti |
| oblast |objekt představující zjištěná slova nebo fráze |
| language |jazyk textu detekovaného v rámci oblasti |
| Orientace |orientace textu detekovaného v rámci oblasti |
| čáry |pole řádků textu zjištěných v rámci oblasti |
| text |skutečný text |

### <a name="json-output-example"></a>Příklad výstupu JSON
Následující příklad výstupu obsahuje obecné informace o videu a několik fragmentů videa. V každém fragmentu videa obsahuje každou oblast, která je detekována OCR MP s jazykem a jeho textovou orientací. Oblast také obsahuje každý řádek slova v této oblasti s textem řádku, umístěním řádku a informacemi o každém slově (obsah slova, pozice a spolehlivost) v tomto řádku. Následuje příklad, a dal jsem nějaké komentáře vložené.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>Ukázkový kód rozhraní .NET

Následující program ukazuje, jak:

1. Vytvořte datový zdroj a nahrajte do něj mediální soubor.
2. Vytvořte úlohu s konfiguračním/přednastaveným souborem OCR.
3. Stáhněte si výstupní soubory JSON. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace OCR
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

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }

    }
}
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

