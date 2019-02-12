---
title: Digitalizaci text pomocí Azure Media Analytics OCR | Dokumentace Microsoftu
description: Azure Media Analytics OCR (optické rozpoznávání znaků) umožňuje převést textový obsah v videosouborů do lze upravovat, prohledávatelné digitální textu.  To umožňuje automatizovat extrakce smysluplná metadata z videa signál médií.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: e0fa769c9071cac0dccaf43c312c80c7d097e345
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005127"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Pomocí Azure Media Analytics můžete převést textový obsah v videosouborů na digitální text  
## <a name="overview"></a>Přehled
Pokud potřebujete extrahování textového obsahu z videosouborů odvodily a generování digitální textu lze upravovat, prohledávatelné, měli byste použít Azure Media Analytics OCR (optické rozpoznávání znaků). Tento procesor médií Azure detekuje textový obsah v video soubory a vygeneruje textových souborů pro použití. OCR umožňuje automatizovat extrakce smysluplná metadata z videa signál médií.

Při použití ve spojení s vyhledávacím webem, můžete snadno indexu médií podle textu a zlepšit vyhledatelnost svůj obsah. To je velmi užitečné ve vysoce textové video, jako je nahrávání videa nebo snímek obrazovky prezentaci prezentace. Procesor médií OCR Azure je optimalizovaná pro digitální textu.

**Azure Media OCR** procesor médií je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media OCR** a ukazuje, jak používat ve službě Media Services SDK pro .NET. Další informace a příklady najdete v tématu [tento blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR vstupních souborů
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration"></a>Konfigurace úlohy
Konfigurace úlohy (nastavení). Při vytváření úlohy s **Azure Media OCR**, je nutné zadat konfigurace přednastavení pomocí XML nebo JSON. 

>[!NOTE]
>Modulu OCR zabere jenom určitá oblast obrázku s minimální 40 pixelů na maximální délku 32 000 pixelů jako platný vstup v obou výšku či šířku.
>

### <a name="attribute-descriptions"></a>Popis atributu
| Název atributu | Popis |
| --- | --- |
|AdvancedOutput| Pokud nastavíte AdvancedOutput na hodnotu true, bude obsahovat výstup JSON poziční data pro každý jednoslovné (kromě oblastí a fráze). Pokud chcete zobrazit tyto informace nechcete, nastavte příznak na hodnotu false. Výchozí hodnota je false. Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Jazyk |(volitelné) popisuje jazyk textu, pro který se vás pod rouškou. Jeden z následujících akcí: Automatické rozpoznávání (výchozí), arabština, ChineseSimplified, ChineseTraditional, čeština dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, SerbianCyrillic, SerbianLatin , Slovenština, španělština, švédština, turečtina. |
| TextOrientation |(volitelné) popisuje orientaci textu, pro který se vás pod rouškou.  "Left" znamená, že horní části všechna písmena jsou nasměruje levé straně.  Výchozí text (např., které lze nalézt v knize) lze volat "Nahoru" orientovaný.  Jeden z následujících akcí: Automatické rozpoznávání (výchozí), až, Jo, dolů, doleva. |
| TimeInterval |(volitelné) popisuje vzorkovací frekvenci.  Výchozí hodnota je každých 1/2 sekundy.<br/>Formát JSON – hh: mm:. Služby Zabezpečené úložiště (výchozí 00:00:00.500)<br/>Formát XML – doba trvání primitivní W3C XSD (výchozí PT0.5) |
| DetectRegions |(volitelné) Pole objektů DetectRegion určení oblastí v rámci video rámce, ve kterém chcete detekovat text.<br/>DetectRegion objekt je vytvořen z následujících čtyř celočíselných hodnot:<br/>Vlevo – pixelů od levého okraje<br/>TOP – pixelů od horní okraj<br/>Šířka – Šířka oblasti v pixelech<br/>Výška – výšku oblasti v pixelech |

#### <a name="json-preset-example"></a>Příklad JSON s předvolby

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

#### <a name="xml-preset-example"></a>Předvolby ukázkový kód XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
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

## <a name="ocr-output-files"></a>OCR výstupní soubory
Procesor médií OCR výstupem je soubor JSON.

### <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON
Výstup videa OCR obsahuje čas segmentované data znaků nalezených ve videu.  Vám pomůže atributům, jako je jazyk nebo orientace phon se změnami na přesně tak, že máte zájem o analýze slova. 

Výstup obsahuje následující atributy:

| Element | Popis |
| --- | --- |
| Timescale |"značky" za sekundu videa |
| Posun |časový posun, časových razítek. Ve verzi 1.0 rozhraní API pro Video bude vždy 0. |
| Framerate |Snímků za sekundu videa |
| Šířka |Šířka videa v pixelech |
| Výška |výšku v pixelech |
| Fragments |pole založené na čase bloky dat videa, do kterého metadata je rozdělený do bloků dat |
| start |Počáteční čas fragment v "značky" |
| doba trvání |Délka fragment v "značky" |
| interval |Interval každá událost v rámci dané fragmentu |
| stránka events |pole obsahující oblastí |
| oblast |objekt představující zjistil slova nebo fráze |
| language |jazyk textu zjistil v rámci oblasti |
| Orientace |orientace textu zjistil v rámci oblasti |
| řádky |pole řádků textu zjistil v rámci oblasti |
| text |vlastní text |

### <a name="json-output-example"></a>Příklad výstupu JSON
Následující příklad výstupu obsahuje obecné informace o videu a několik fragmentů videa. V každé video fragment obsahuje každou oblast, která zjistí OCR sady Management Pack s jazykem a jeho orientaci textu. Oblast také obsahuje každý jednotlivý řádek slova v této oblasti se na řádku textu, pozice na řádku a každé slovo informace (obsahu aplikace word, umístění a spolehlivosti) v tomto řádku. Následuje příklad a můžu dát některé vložené komentáře.

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

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje postup:

1. Vytvoření assetu a uložit do assetu soubor média.
2. Vytvoření úlohy pomocí souboru konfigurace/přednastavení optické rozpoznávání znaků.
3. Stažení výstupních souborů JSON. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Příklad:

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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

