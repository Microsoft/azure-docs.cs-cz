---
title: Indexování mediálních souborů pomocí Azure Media Indexer
description: Azure Media Indexer umožňuje prohledávat obsah mediálních souborů a generovat fulltextový přepis pro skryté titulky a klíčová slova. Toto téma ukazuje, jak používat Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 7ccc2d5956b44a8cd85f19e0905539c32f58bc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163994"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexování mediálních souborů pomocí Azure Media Indexer

> [!NOTE]
> Mediální procesor **Azure Media Indexer** bude vyřazen. Data vyřazení naleznete v tomto tématu [starších součástí.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahradí tento starší mediální procesor. Další informace najdete [v tématu Migrace z Azure Media Indexer a Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

Azure Media Indexer umožňuje prohledávat obsah mediálních souborů a generovat fulltextový přepis pro skryté titulky a klíčová slova. V dávce můžete zpracovat jeden mediální soubor nebo více mediálních souborů.  

Při indexování obsahu používejte mediální soubory, které mají jasnou řeč (bez hudby na pozadí, šumu, efektů nebo syčení mikrofonu). Příklady vhodného obsahu jsou: nahrané schůzky, přednášky nebo prezentace. Následující obsah nemusí být vhodný pro indexování: filmy, televizní pořady, cokolise se smíšenými zvukovými a zvukovými efekty, špatně nahraný obsah s šumem na pozadí (syčení).

Úloha indexování může generovat následující výstupy:

* Soubory skrytých titulků v následujících formátech: **TTML**a **WebVTT**.
  
    Soubory skrytých titulků obsahují značku nazvanou Rozpoznatelnost, která zkresluje úlohu indexování na základě toho, jak rozpoznatelná je řeč ve zdrojovém videu.  Můžete použít hodnotu Rozpoznatelnost na obrazovku výstupní soubory pro použitelnost. Nízké skóre by znamenalo špatné výsledky indexování kvůli kvalitě zvuku.
* Soubor klíčových slov (XML).

Tento článek ukazuje, jak vytvořit indexování úloh indexovat **indexovat datový zdroj** a **indexovat více souborů**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Použití konfiguračních souborů a souborů manifestů pro úlohy indexování
Pomocí konfigurace úloh můžete zadat další podrobnosti o úlohách indexování. Můžete například určit, která metadata se mají použít pro mediální soubor. Tato metadata se používá modul jazyka rozšířit svou slovní zásobu a výrazně zlepšuje přesnost rozpoznávání řeči.  Můžete také zadat požadované výstupní soubory.

Pomocí souboru manifestu můžete také zpracovat více mediálních souborů najednou.

Další informace najdete [v tématu Přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexování datového zdroje
Následující metoda odešle mediální soubor jako datový zdroj a vytvoří úlohu pro indexování datového zdroje.

Pokud není zadán žádný konfigurační soubor, mediální soubor je indexován se všemi výchozími nastaveními.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Výstupní soubory
Ve výchozím nastavení generuje úloha indexování následující výstupní soubory. Soubory jsou uloženy v prvním výstupním datovém zdroji.

Pokud existuje více než jeden vstupní mediální soubor, Indexer generuje soubor manifestu pro výstupy úloh s názvem JobResult.txt. Pro každý vstupní mediální soubor jsou výsledné soubory TTML, WebVTT a klíčové ho klíčového slova postupně číslovány a pojmenovány pomocí "Alias".

| Název souboru | Popis |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |Soubory skrytých titulků (CC) ve formátech TTML a WebVTT.<br/><br/>Mohou být použity k tomu, aby byly zvukové a video soubory přístupné osobám se sluchovým postižením.<br/><br/>Soubory skrytých titulků obsahují značku nazvanou <b>Rozpoznatelnost,</b> která zkresluje úlohu indexování na základě toho, jak rozpoznatelná je řeč ve zdrojovém videu.  Můžete použít hodnotu <b>Rozpoznatelnost</b> na obrazovku výstupní soubory pro použitelnost. Nízké skóre by znamenalo špatné výsledky indexování kvůli kvalitě zvuku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Klíčové slovo a informační soubory. <br/><br/>Soubor klíčových slov je soubor XML, který obsahuje klíčová slova extrahovaná z obsahu řeči s informacemi o frekvenci a posunu. <br/><br/>Informační soubor je soubor ve formátu prostého textu, který obsahuje podrobné informace o každém rozpoznaný termín. První řádek je zvláštní a obsahuje skóre rozpoznatelnosti. Každý následující řádek je seznam následujících dat oddělených tabulátory: počáteční čas, čas ukončení, slovo/fráze, důvěra. Časy jsou uvedeny v sekundách a důvěra je uvedena jako číslo od 0-1. <br/><br/>Příklad řádku: "1.20 1.45 slovo 0.67" <br/><br/>Tyto soubory mohou být použity pro řadu účelů, jako je například provádět analýzu řeči, nebo vystaveny vyhledávačům, jako je Bing, Google nebo Microsoft SharePoint, aby se mediální soubory více zjistitelné, nebo dokonce použít k poskytování relevantnějších reklam. |
| **Soubor JobResult.txt** |Výstupní manifest, který se vyskytuje pouze při indexování více souborů, obsahující následující informace:<br/><br/><table border="1"><tr><th>Soubor vstupů</th><th>Alias</th><th>Délka média</th><th>Chyba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Pokud nejsou všechny vstupní mediální soubory úspěšně indexovány, úloha indexování se nezdaří s kódem chyby 4000. Další informace naleznete v [tématu Chybové kódy](#error_codes).

## <a name="index-multiple-files"></a>Indexování více souborů
Následující metoda odešle více mediálních souborů jako datový zdroj a vytvoří úlohu pro indexování všech těchto souborů v dávce.

Je vytvořen soubor manifestu s příponou ".lst" a nahraje se do datového zdroje. Soubor manifestu obsahuje seznam všech souborů datových zdrojů. Další informace najdete [v tématu Přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Částečně úspěšná úloha
Pokud nejsou všechny vstupní mediální soubory úspěšně indexovány, úloha indexování se nezdaří s kódem chyby 4000. Další informace naleznete v [tématu Chybové kódy](#error_codes).

Jsou generovány stejné výstupy (jako úspěšné úlohy). Můžete odkazovat na výstupní soubor manifestu a zjistit, které vstupní soubory se nezdařily, podle hodnot sloupce Chyba. Pro vstupní soubory, které se nezdařily, výsledné TTML, WebVTT a klíčové slovo soubory nebudou generovány.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a>Přednastavení úlohy pro Azure Media Indexer
Zpracování z Azure Media Indexer lze přizpůsobit poskytnutím volitelné úlohy přednastavení vedle úkolu.  Následující text popisuje formát této konfigurace xml.

| Name (Název) | Vyžadovat | Popis |
| --- | --- | --- |
| **Vstupní** |false (nepravda) |Soubory majetku, které chcete indexovat.</p><p>Azure Media Indexer podporuje následující formáty mediálních souborů: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Název souboru (y) můžete zadat v atributu **názvu** nebo **seznamu** **vstupního** prvku (jak je znázorněno níže). Pokud neurčíte, který soubor datového zdroje má být indexován, bude primární soubor vybrán. Pokud není nastaven žádný primární soubor datového zdroje, je indexován první soubor ve vstupním datovém zdroji.</p><p>Chcete-li explicitně zadat název souboru datového zdroje, proveďte následující:<br/>`<input name="TestFile.wmv">`<br/><br/>Můžete také indexovat více souborů datových zdrojů najednou (až 10 souborů). Použijte následující postup:<br/><br/><ol class="ordered"><li><p>Vytvořte textový soubor (soubor manifestu) a poskytněte mu příponu LST. </p></li><li><p>Přidejte do tohoto souboru manifestu seznam všech názvů souborů datových zdrojů ve vstupním datovém zdroji. </p></li><li><p>Přidejte (nahrajte) soubor manifestu do datového zdroje.  </p></li><li><p>Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>`<input list="input.lst">`</li></ol><br/><br/>Poznámka: Pokud do souboru manifestu přidáte více než 10 souborů, úloha indexování se nezdaří s kódem chyby 2006. |
| **Metadata** |false (nepravda) |Metadata pro zadané soubory datových zdrojů, které se používají pro přizpůsobení slovní zásoby.  Užitečné připravit Indexer rozpoznat nestandardní slovní zásobu slova, jako jsou správná vlastní jménem.<br/>`<metadata key="..." value="..."/>` <br/><br/>Můžete zadat **hodnoty** pro předdefinované **klíče**. V současné době jsou podporovány následující klíče:<br/><br/>"title" a "description" - používá se pro přizpůsobení slovní zásoby, aby vyladit jazykový model pro vaši práci a zlepšit přesnost rozpoznávání řeči.  Hodnoty osiva Internet vyhledává kontextově relevantní textové dokumenty, pomocí obsahu rozšířit vnitřní slovník po dobu trvání úlohy indexování.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkce** <br/><br/> Přidáno ve verzi 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR"). |false (nepravda) |Funkce Rozpoznávání řeči má následující klávesy nastavení:<table><tr><th><p>Klíč</p></th>        <th><p>Popis</p></th><th><p>Příklad hodnoty</p></th></tr><tr><td><p>Jazyk</p></td><td><p>Přirozený jazyk, který má být rozpoznán v multimediálním souboru.</p></td><td><p>Angličtina, španělština</p></td></tr><tr><td><p>TitulkyFormáty</p></td><td><p>seznam požadovaných formátů výstupních titulků oddělených středníkem (pokud existuje)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Pravda; False</p></td></tr><tr><td><p>Generovat klíčová slova</p></td><td><p>Logický příznak určující, zda je vyžadován soubor XML klíčového slova.</p></td><td><p>Pravda; False. </p></td></tr><tr><td><p>Titulek forcefull</p></td><td><p>Logický příznak určující, zda má být vynucováno úplné titulky (bez ohledu na úroveň spolehlivosti).  </p><p>Výchozí hodnota je false, v takovém případě jsou slova a fráze, které mají méně než 50 % spolehlivosti, vynechány z výstupů konečných titulků a nahrazeny třemi tečkami ("...").  Elipsy jsou užitečné pro kontrolu kvality titulků a auditování.</p></td><td><p>Pravda; False. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Kódy chyb
V případě chyby azure media indexer by měl hlásit zpět jeden z následujících kódů chyb:

| kód | Name (Název) | Možné důvody |
| --- | --- | --- |
| 2000 |Neplatná konfigurace |Neplatná konfigurace |
| 2001 |Neplatné vstupní prostředky |Chybí vstupní majetek nebo prázdný majetek. |
| 2002 |Neplatný manifest |Manifest je prázdný nebo manifest obsahuje neplatné položky. |
| 2003 |Stažení mediálního souboru se nezdařilo. |Neplatná adresa URL v souboru manifestu. |
| 2004 |Nepodporovaný protokol |Protokol adresy URL média není podporován. |
| 2005 |Nepodporovaný typ souboru |Typ souboru vstupního média není podporován. |
| 2006 |Příliš mnoho vstupních souborů |Ve vstupním manifestu je více než 10 souborů. |
| 3000 |Dekódování mediálního souboru se nezdařilo. |Kodek nepodporovaného média <br/>– nebo –<br/> Poškozený mediální soubor <br/>– nebo –<br/> Žádný zvukový proud ve vstupním médiu. |
| 4000 |Indexování dávek bylo částečně úspěšné. |Některé vstupní mediální soubory se nepodařilo indexovat. Další informace naleznete <a href="#output_files">v tématu Output files</a>. |
| ostatní |Vnitřní chyby |Obraťte se na tým podpory. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Podporované jazyky
V současné době jsou podporovány anglické a španělské jazyky.  

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

