---
title: Indexování mediálních souborů pomocí Azure Media Indexer
description: Azure Media Indexer umožňuje zpřístupnění obsahu mediálních souborů a vytvoření fulltextového přepisu pro skryté titulky a klíčová slova. V tomto tématu se dozvíte, jak používat Media Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b72cca319772fa52829c6b95237e15f083a2e7
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962658"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexování mediálních souborů pomocí Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Doporučujeme, aby zákazníci migrovali ze služby indexer V1 a služby indexer v2, aby používali [Základní režim Media Services V3 AudioAnalyzerPreset](../latest/analyze-video-audio-files-concept.md). Vyřadí se procesory [Azure Media Indexer](media-services-index-content.md) media a [Azure Media Indexer 2 ve verzi Preview](./legacy-components.md) . Informace o datech vyřazení najdete v tématu tyto [starší součásti](legacy-components.md) .

Azure Media Indexer umožňuje zpřístupnění obsahu mediálních souborů a vytvoření fulltextového přepisu pro skryté titulky a klíčová slova. V dávce můžete zpracovat jeden mediální soubor nebo několik mediálních souborů.  

Při indexování obsahu se ujistěte, že používáte mediální soubory, které mají jasný zvuk (bez hudby, hluku, efektů nebo mikrofonu Hiss). Mezi příklady příslušného obsahu patří: zaznamenané schůzky, přednášky nebo prezentace. Následující obsah nemusí být vhodný pro indexování: filmy, televizní pořady, cokoli se smíšeným zvukovým a zvukovým efektem, špatně zaznamenaného obsahu s hlukem na pozadí (Hiss).

Úloha indexování může generovat následující výstupy:

* Soubory titulků jsou skryté v následujících formátech: **ttml** a **WebVTT**.
  
    Soubory skrytých titulků obsahují značku s názvem rozpoznatelné, která vyhodnotí úlohu indexování na základě toho, jak rozpoznávání řeči ve zdrojovém videu je.  Pro použitelnost můžete použít hodnotu rozpoznatelné výstupní soubory obrazovky. Nízké skóre by znamenalo špatné indexování výsledků kvůli kvalitě zvuku.
* Soubor klíčových slov (XML).

Tento článek ukazuje, jak vytvořit úlohy indexování k **indexování prostředku** a **indexu více souborů**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Použití konfigurace a souborů manifestu pro indexování úloh
Pro úlohy indexování můžete zadat více podrobností pomocí konfigurace úlohy. Můžete například určit, která metadata se mají použít pro váš mediální soubor. Tato metadata používá modul jazyka k rozšíření svého slovníku a výrazně vylepšuje přesnost rozpoznávání řeči.  Je také možné zadat požadované výstupní soubory.

Můžete také zpracovat více mediálních souborů najednou pomocí souboru manifestu.

Další informace najdete v tématu [předvolba úlohy pro Azure Media Indexer](./legacy-components.md).

## <a name="index-an-asset"></a>Indexace prostředku
Následující metoda nahraje mediální soubor jako Asset a vytvoří úlohu, která indexuje Asset.

Pokud není zadán žádný konfigurační soubor, soubor média bude indexován se všemi výchozími nastaveními.

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
Ve výchozím nastavení generuje úloha indexování následující výstupní soubory. Soubory jsou uloženy v prvním výstupním prostředku.

Pokud je k dispozici více než jeden vstupní mediální soubor, indexer vygeneruje soubor manifestu pro výstupy úlohy s názvem ' JobResult.txt '. Pro každý vstupní soubor médií jsou výsledné TTML, WebVTT a soubory klíčových slov sekvenčně očíslovány a pojmenovány pomocí aliasu.

| Název souboru | Description |
| --- | --- |
| **InputFileName. ttml**<br/>**InputFileName. vtt** |Soubory skrytého titulku (CC) ve formátech TTML a WebVTT<br/><br/>Je možné je použít k zpřístupnění zvukových a videosouborů lidem s postižením sluchu.<br/><br/>Soubory skrytých titulků obsahují značku s názvem <b>rozpoznatelné</b> , která vyhodnotí úlohu indexování na základě toho, jak rozpoznávání řeči ve zdrojovém videu je.  Pro použitelnost můžete použít hodnotu <b>rozpoznatelné</b> výstupní soubory obrazovky. Nízké skóre by znamenalo špatné indexování výsledků kvůli kvalitě zvuku. |
| **InputFileName.kw.xml<br/> InputFileName.info** |Klíčové slovo a soubory informací <br/><br/>Soubor s klíčovým slovem je soubor XML, který obsahuje klíčová slova extrahovaná z obsahu řeči s informacemi o četnosti a posunu. <br/><br/>Informační soubor je textový soubor, který obsahuje podrobné informace o jednotlivých rozpoznaných termínech. První řádek je zvláštní a obsahuje skóre pro rozpoznávání. Každý další řádek je seznam oddělený tabulátorem následujících dat: čas spuštění, čas ukončení, slovo/fráze, důvěra. Časy jsou uvedené v sekundách a jistota se zadává jako číslo od 0-1. <br/><br/>Příklad řádku: "1,20 1,45 Word 0,67" <br/><br/>Tyto soubory se dají použít pro různé účely, jako je například, k provádění analýz řeči nebo k vystavení vyhledávačům, jako je Bing, Google nebo Microsoft SharePoint, aby se mediální soubory lépe zjistitelné nebo dokonce používaly k doručování dalších relevantních reklam. |
| **JobResult.txt** |Výstupní manifest, přítomen pouze při indexování více souborů obsahujících následující informace:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Chyba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Pokud nejsou všechny vstupní mediální soubory úspěšně indexovány, úloha indexování nebude úspěšná s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

## <a name="index-multiple-files"></a>Indexovat více souborů
Následující metoda nahraje více mediálních souborů jako Asset a vytvoří úlohu pro indexování všech těchto souborů v dávce.

Vytvoří se soubor manifestu s příponou. lst a nahraje se do assetu. Soubor manifestu obsahuje seznam všech souborů assetů. Další informace najdete v tématu [předvolba úlohy pro Azure Media Indexer](./legacy-components.md).

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

### <a name="partially-succeeded-job"></a>Částečně úspěšné úlohy
Pokud nejsou všechny vstupní mediální soubory úspěšně indexovány, úloha indexování nebude úspěšná s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

Vygenerují se stejné výstupy (jako úlohy, které byly úspěšně dokončeny). Můžete se podívat na výstupní soubor manifestu a zjistit, které vstupní soubory se nezdařily, podle hodnot sloupce chyby. Pro vstupní soubory, které selhaly, nebudou vygenerovány výsledné TTML, WebVTT a soubory klíčových slov.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Předvolba úlohy pro Azure Media Indexer
Zpracování z Azure Media Indexer lze přizpůsobit zadáním volitelné předvolby úlohy vedle této úlohy.  Následující popis popisuje formát konfiguračního souboru XML.

| Name | Vyžadovat | Description |
| --- | --- | --- |
| **vstup** |false (nepravda) |Soubory prostředků, které chcete indexovat.</p><p>Azure Media Indexer podporuje následující formáty mediálních souborů: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Můžete zadat název souboru (y) v atributu **název** nebo **seznam** **vstupního** elementu (jak je vidět níže). Pokud neurčíte, který soubor prostředků se má indexovat, je primární soubor vybrán. Pokud není nastaven žádný soubor primárního majetku, bude indexován první soubor ve vstupním prostředku.</p><p>Chcete-li explicitně zadat název souboru assetu, udělejte toto:<br/>`<input name="TestFile.wmv">`<br/><br/>Můžete také indexovat více souborů prostředků najednou (až 10 souborů). Použijte následující postup:<br/><br/><ol class="ordered"><li><p>Vytvořte textový soubor (soubor manifestu) a sdělte mu příponu. lst. </p></li><li><p>Do tohoto souboru manifestu přidejte seznam všech názvů souborů assetu ve vstupním prostředku. </p></li><li><p>Přidejte (nahrajte) soubor manifestu do assetu.  </p></li><li><p>Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>`<input list="input.lst">`</li></ol><br/><br/>Poznámka: Pokud do souboru manifestu přidáte více než 10 souborů, úloha indexování se nezdaří a zobrazí se kód chyby 2006. |
| **mezipaměť** |false (nepravda) |Metadata pro zadané soubory prostředků, které se používají pro úpravu slovníku  Slouží k přípravě indexeru k rozpoznávání slov nestandardního slovníku, jako jsou třeba řádná podstatná jména.<br/>`<metadata key="..." value="..."/>` <br/><br/>Můžete zadávat **hodnoty** pro předdefinované **klíče**. V současné době jsou podporovány následující klíče:<br/><br/>"title" a "Description" – slouží k úpravě slovníku pro úpravu jazykového modelu pro vaši úlohu a zlepšení přesnosti rozpoznávání řeči.  Hodnoty dodané k Internetu vyhledají kontextově relevantní textové dokumenty a využívají obsah k rozšíření vnitřního slovníku po dobu trvání úlohy indexování.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **funkce** <br/><br/> Přidáno ve verzi 1,2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči (ASR). |false (nepravda) |Funkce rozpoznávání řeči má následující klíče nastavení:<table><tr><th><p>Klíč</p></th>        <th><p>Popis</p></th><th><p>Příklad hodnoty</p></th></tr><tr><td><p>Jazyk</p></td><td><p>Přirozený jazyk, který se má rozpoznat v multimediálním souboru</p></td><td><p>Angličtina, španělština</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>středníkem oddělený seznam požadovaných výstupních formátů titulků (pokud existují)</p></td><td><p>ttml; WebVTT</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Podmínka Chybné</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Logický příznak určující, zda je vyžadován soubor XML s klíčovým slovem.</p></td><td><p>Podmínka Chybné. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Logický příznak určující, zda se mají vynutit úplné titulky (bez ohledu na úroveň spolehlivosti).  </p><p>Výchozí hodnota je false. v tom případě se slova a fráze, které mají úroveň spolehlivosti nižší než 50%, vynechají z konečných výstupů titulků a nahrazené třemi tečkami ("...").  Tři tečky jsou užitečné pro řízení kvality a auditování kvality titulků.</p></td><td><p>Podmínka Chybné. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Kódy chyb
V případě chyby by Azure Media Indexer měla nahlásit jeden z následujících kódů chyb:

| Kód | Name | Možné důvody |
| --- | --- | --- |
| 2000 |Neplatná konfigurace |Neplatná konfigurace |
| 2001 |Neplatné vstupní prostředky |Chybí vstupní assety nebo prázdný Asset. |
| 2002 |Neplatný manifest |Manifest je prázdný nebo manifest obsahuje neplatné položky. |
| 2003 |Nepovedlo se stáhnout mediální soubor. |V souboru manifestu je neplatná adresa URL. |
| 2004 |Nepodporovaný protokol |Protokol adresy URL média není podporován. |
| 2005 |Nepodporovaný typ souboru |Typ vstupního mediálního souboru se nepodporuje. |
| 2006 |Příliš mnoho vstupních souborů |Vstupní manifest obsahuje více než 10 souborů. |
| 3000 |Nepovedlo se dekódovat mediální soubor. |Nepodporovaný kodek multimédií <br/>nebo<br/> Poškozený mediální soubor <br/>nebo<br/> Ve vstupních médiích nejsou žádné zvukové streamy. |
| 4000 |Dávkové indexování bylo dokončeno částečně. |Některé ze vstupních mediálních souborů se nepovedlo indexovat. Další informace najdete v tématu <a href="#output_files">výstupní soubory</a>. |
| other |Vnitřní chyby |Obraťte se prosím na tým podpory. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Podporované jazyky
V současné době jsou podporovány anglické a španělské jazyky.  

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Azure Media Services](./legacy-components.md)

[Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview](./legacy-components.md)