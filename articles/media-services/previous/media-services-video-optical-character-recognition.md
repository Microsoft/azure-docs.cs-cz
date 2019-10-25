---
title: Digitalizace textu pomocí Azure Media Analytics optické rozpoznávání znaků | Microsoft Docs
description: Azure Media Analytics optické rozpoznávání znaků (OCR) umožňuje převést textový obsah v videosouborech na upravitelný a prohledávatelný digitální text.  To vám umožní automatizovat extrakci smysluplných metadat z obrazového signálu vašeho média.
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
ms.openlocfilehash: 48b5136505c3d0cb5e2e2027f832655e4b3445bf
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881744"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Použití Azure Media Analytics k převodu textového obsahu v videosouborech na digitální text  

> [!IMPORTANT]
> Prohlédněte si [plány vyřazení](media-services-analytics-overview.md#retirement-plans) některých multimediálních procesorů.

## <a name="overview"></a>Přehled
Pokud potřebujete extrahovat textový obsah z vašich videosouborů a vygenerovat upravitelný, prohledávatelný digitální text, měli byste použít Azure Media Analytics optické rozpoznávání znaků (OCR). Tento procesor Azure Media detekuje textový obsah ve vašich videosouborech a generuje textové soubory pro použití. Optické rozpoznávání znaků umožňuje automatizovat extrakci smysluplných metadat z obrazového signálu vašeho média.

Při použití ve spojení s vyhledávacím modulem můžete své médium snadno indexovat podle textu a rozšířit zjistitelnost obsahu. To je velmi užitečné ve vysoce textovém videu, jako je záznam videa nebo snímek obrazovky prezentace prezentace. Procesor médií v Azure OCR je optimalizovaný pro digitální text.

Procesor s médii **Azure Media OCR** je momentálně ve verzi Preview.

Tento článek obsahuje podrobné informace o **platformě Azure Media OCR** a ukazuje, jak ji použít s Media Services SDK pro .NET. Další informace a příklady najdete v [tomto blogu](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Vstupní soubory OCR
Videosoubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="task-configuration"></a>Konfigurace úlohy
Konfigurace úlohy (přednastavení). Při vytváření úlohy pomocí **Azure Media OCR**je nutné zadat předvolby konfigurace pomocí JSON nebo XML. 

>[!NOTE]
>Modul rozpoznávání OCR převezme oblast obrázku s minimální 40 pixely na maximálně 32000 pixelů jako platný vstup na výšku i šířku.
>

### <a name="attribute-descriptions"></a>Popisy atributů
| Název atributu | Popis |
| --- | --- |
|AdvancedOutput| Pokud nastavíte AdvancedOutput na hodnotu true, bude výstup JSON obsahovat pozice pro každé jedno slovo (kromě frází a oblastí). Pokud nechcete zobrazit tyto podrobnosti, nastavte příznak na false. Výchozí hodnota je false (NEPRAVDA). Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Jazyk |(volitelné) popisuje jazyk textu, který má být hledán. Jedna z následujících možností: automaticky rozpoznat (výchozí), arabština, ChineseSimplified, ChineseTraditional, Čeština Dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, polština, italština, japonština, korejština, norština, ruština, portugalština, rumunština, ruština SerbianCyrillic, SerbianLatin, slovenština, španělština, švédština, turečtina. |
| TextOrientation |(volitelné) popisuje orientaci textu, který chcete vyhledat.  "Left" znamená, že horní část všech písmen je odkazována směrem doleva.  Výchozí text (jako ten, který se dá najít v knize), se dá volat nahoru.  Jedna z následujících možností: automaticky rozpoznat (výchozí), nahoru, doprava, dolů, doleva. |
| TimeInterval |(volitelné) popisuje vzorkovací frekvenci.  Výchozí hodnota je každých 1/2 sekund.<br/>Formát JSON – HH: mm: ss. Zabezpečené úložiště (výchozí hodnota je 00:00:00.500)<br/>Formát XML – primitivum doby trvání W3C XSD (výchozí PT 0.5) |
| DetectRegions |volitelné Pole objektů DetectRegion určujících oblasti v rámci videa, ve kterých se má detekovat text<br/>Objekt DetectRegion je tvořen následujícími čtyřmi celočíselnými hodnotami:<br/>Vlevo – pixely od levého okraje<br/>Horní – pixely od horního okraje<br/>Width – Šířka oblasti v pixelech<br/>Height – výška oblasti v pixelech |

#### <a name="json-preset-example"></a>Příklad přednastaveného formátu JSON

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

#### <a name="xml-preset-example"></a>Příklad přednastavení XML

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
Výstupem procesoru s médii OCR je soubor JSON.

### <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON
Výstup optického rozpoznávání videa poskytuje data rozdělená na znaky nalezené ve videu.  Můžete použít atributy, jako je například jazyk nebo orientace, k doladití přesně slova, která vás zajímají při analýze. 

Výstup obsahuje následující atributy:

| Element | Popis |
| --- | --- |
| Timescale |"takty" za sekundu videa |
| Posun |časový posun pro časová razítka. Ve verzi 1,0 rozhraní API pro video bude tato hodnota vždycky 0. |
| Framerate |Snímků za sekundu videa |
| Délk |Šířka videa v pixelech |
| Výška |Výška videa v pixelech |
| Fragments |pole časových bloků videa, na které jsou metadata v bloku |
| zahájení |počáteční čas fragmentu v taktech |
| úkolu |Délka fragmentu v "taktech" |
| interval |interval každé události v rámci daného fragmentu |
| stránka events |pole obsahující oblasti |
| . |objekt představující zjištěná slova nebo fráze |
| language |jazyk textu zjištěného v rámci oblasti |
| změny |orientace textu zjištěného v rámci oblasti |
| přím |pole řádků textu zjištěných v rámci oblasti |
| text |skutečný text |

### <a name="json-output-example"></a>Příklad výstupu JSON
Následující příklad výstupu obsahuje obecné informace o videu a několik fragmentů videa. V každém fragmentu videa obsahuje každou oblast, která je detekována nástrojem OCR MP s jazykem a jeho orientací textu. Oblast také obsahuje všechny wordové řádky v této oblasti s textem řádku, pozicí řádku a všemi slovy (obsahem a jistotou Wordu) na tomto řádku. Níže je uveden příklad a jsou vloženy komentáře.

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

Následující program ukazuje, jak:

1. Vytvořte Asset a nahrajte do něj mediální soubor.
2. Vytvoří úlohu s použitím souboru konfigurace nebo přednastavení OCR.
3. Stáhněte si výstupní soubory JSON. 
   
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

## <a name="provide-feedback"></a>Poskytnout zpětnou vazbu
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Azure Media Services](media-services-analytics-overview.md)

