---
title: Digitalizace textu pomocí Azure Media Analytics rozpoznávání znaků | Microsoft Docs
description: Rozpoznávání Azure Media Analytics znaků (optické rozpoznávání znaků) umožňuje převést textového obsahu v video soubory upravovat, vyhledávat digitální text.  To umožňuje automatizovat extrakce smysluplný metadata z video signál média.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 4a7a31b4e0069d2c94a4f109248d7b02c0b03faa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Použití Azure Media Analytics k převodu textového obsahu v videosouborů na digitální text
## <a name="overview"></a>Přehled
Pokud potřebujete k extrahování obsahu text z video soubory a generování upravovat, vyhledávat digitální text, měli byste použít rozpoznávání Azure Media Analytics znaků (optické rozpoznávání znaků). Tento procesor médií Azure zjistí textového obsahu v video soubory a vygeneruje textových souborů pro vaše použití. Rozpoznávání znaků umožňuje automatizovat extrakce smysluplný metadata z video signál média.

Při použití ve spojení s vyhledávacího webu, můžete snadno indexu médiu podle textu a vylepšit možnosti rozpoznání obsahu. To je velmi užitečné v vysoce textovou video, jako je záznam videa nebo snímek obrazovky prezentace prezentace. Procesor médií rozpoznávání znaků Azure je optimalizovaná pro digitální text.

**Rozpoznávání Azure Media znaků** procesor médií je aktuálně ve verzi Preview.

Tento článek obsahuje údaje o **rozpoznávání Azure Media znaků** a ukazuje, jak pomocí sady Media Services SDK pro .NET. Další informace a příklady naleznete v tématu [tomto blogu](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Vstupní soubory rozpoznávání znaků
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration"></a>Konfigurace úlohy
Konfigurace úlohy (přednastavených). Při vytváření úlohy s **rozpoznávání Azure Media znaků**, je nutné zadat konfiguraci přednastavení pomocí XML nebo JSON. 

>[!NOTE]
>Modul rozpoznávání znaků pouze jako platný vstup v obou výška a šířka trvá oblast bitové kopie s minimální 40 pixelů na maximální délku 32 000 pixelů.
>

### <a name="attribute-descriptions"></a>Atribut popisy
| Název atributu | Popis |
| --- | --- |
|AdvancedOutput| Pokud nastavíte AdvancedOutput na hodnotu true, budou obsahovat výstup JSON poziční data pro každou jednoho slova (kromě frází a oblasti). Pokud nechcete tyto podrobnosti zobrazíte, nastavte příznak na hodnotu false. Výchozí hodnota je false. Další informace najdete v tématu [tomto blogu](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Jazyk |(volitelné) popisuje jazyk textu, pro které chcete hledat. Jeden z následujících: AutoDetect (výchozí), Arabské, ChineseSimplified, ChineseTraditional, čeština dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, SerbianCyrillic, SerbianLatin, slovenština, španělština, švédština, turečtina. |
| TextOrientation |(volitelné) popisuje orientaci textu, pro které chcete hledat.  "Vlevo" znamená horní části všechna písmena jsou nasměruje levé straně.  Výchozí text (např., které lze nalézt v podobě knihy) je možné volat "Nahoru" orientované.  Jeden z následujících: AutoDetect (výchozí), až, vpravo, dolů, doleva. |
| TimeInterval |(volitelné) popisuje míry vzorkování.  Výchozí hodnota je každou sekundu 1/2.<br/>Formát JSON – hh: mm:. Služby Zabezpečené úložiště (výchozí 00:00:00.500)<br/>Formát XML – doba trvání primitivní W3C XSD (výchozí PT0.5) |
| DetectRegions |(volitelné) Pole objektů DetectRegion zadáte oblasti v rámci video rámce, ve kterém k detekci text.<br/>Objekt DetectRegion sestávající ze čtyř logického:<br/>Vlevo – pixelů z levého okraje<br/>TOP – pixelů z horní okraj<br/>Šířka – Šířka oblasti v pixelech<br/>Výška – výšku oblasti v pixelech |

#### <a name="json-preset-example"></a>Příklad přednastavené JSON

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

#### <a name="xml-preset-example"></a>Příklad přednastavené XML

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

## <a name="ocr-output-files"></a>Rozpoznávání znaků výstupní soubory
Výstup procesor médií rozpoznávání znaků je soubor JSON.

### <a name="elements-of-the-output-json-file"></a>Elementy výstupního souboru JSON
Výstup Video rozpoznávání znaků poskytuje segmentované čas data na znaky v videa nalezen.  Atributy, jako je například jazyk nebo orientaci můžete použít k hone-in na přesně slova, že máte zájem analýza. 

Výstup obsahuje následující atributy:

| Element | Popis |
| --- | --- |
| Časová osa |"rysky" za sekundu videa |
| Posun |časového posunu pro časová razítka. Ve verzi 1.0 rozhraní API, Video bude vždy 0. |
| kmitočet snímků |Počet snímků za sekundu videa |
| Šířka |Šířka videa v pixelech |
| Výška |Výška videa v pixelech |
| fragmenty |pole založené na čase bloků dat videa, do kterého je blokové metadata |
| start |Počáteční čas fragment v "rysky" |
| Doba trvání |Délka fragment v "rysky" |
| interval |Interval jednotlivých událostí v rámci dané fragment |
| stránka events |pole obsahující oblastí |
| oblast |objekt představující zjistil slova nebo fráze |
| Jazyk |jazyk textu zjistil v rámci oblasti |
| orientace |orientaci textu zjistil v rámci oblasti |
| řádky |pole řádků textu zjistil v rámci oblasti |
| Text |vlastní text |

### <a name="json-output-example"></a>Příklad výstupu JSON
Následující příklad výstupu obsahuje obecné informace videa a několik video fragmenty. V každé video fragmentu obsahuje každou oblast, který je zjišťován pomocí MP rozpoznávání znaků s jazyk a jeho orientaci textu. Oblast také obsahuje každý řádek aplikace word v této oblasti na řádku textu, pozice na řádku a každý word informace (word obsahu, pozice a spolehlivosti) v tomto řádku. Následuje příklad a umístíte některé vložené komentáře.

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

Program zobrazí následující postup:

1. Vytvořte asset a nahrajte soubor média do assetu.
2. Vytvoření úlohy se souborem konfigurace nebo přednastavených rozpoznávání znaků.
3. Stáhněte soubory JSON výstupu. 
   
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
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

