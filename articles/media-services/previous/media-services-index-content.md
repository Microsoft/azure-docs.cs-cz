---
title: Indexování mediálních souborů pomocí Azure Media Indexer
description: Azure Media Indexer umožňuje zajistit obsah souborů médií s možností vyhledávání a ke generování fulltextového přepis pro skryté titulky nebo klíčová slova. Toto téma ukazuje způsob použití Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: 320d8cfa21c36e0f0d751d29d461a0023ab563e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788904"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexování mediálních souborů pomocí Azure Media Indexer
Azure Media Indexer umožňuje zajistit obsah souborů médií s možností vyhledávání a ke generování fulltextového přepis pro skryté titulky nebo klíčová slova. Mediální soubory můžete zpracovávat po jednom nebo v dávkách.  

> [!IMPORTANT]
> Během indexování obsah, nezapomeňte použít mediální soubory, které mají jasné řeči (bez pozadí Hudba, šumu, efekty nebo mikrofon hiss). Některé příklady příslušný obsah: zaznamenávají schůzek, přednášek nebo prezentací. Následující obsah nemusí být vhodný pro indexování: filmy, televizní pořady cokoli s smíšený zvuk a zvukové efekty špatně zaznamenány obsah s hluku na pozadí (hiss).
> 
> 

Úlohu indexování může generovat výstupy následující:

* Uzavřený titulek soubory v následujících formátech: **SAMI**, **TTML**, a **WebVTT**.
  
    Soubory titulků zahrnují značku Recognizability, která skóre indexování úlohy podle toho, jak rozpoznatelném řeči na videu zdroje.  Hodnota Recognizability slouží k obrazovce výstupní soubory pro použitelnost. Nízké skóre bude znamenat nedostatečný indexování výsledky z důvodu kvalitu zvuku.
* Soubor – klíčové slovo (XML).
* Zvuk indexování soubor blob (AIB) pro použití se systémem SQL server.
  
    Další informace najdete v tématu [pomocí AIB souborů pomocí Azure Media Indexer a SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Tento článek ukazuje, jak vytvořit indexování úloh **indexu prostředek** a **indexu více souborů**.

Nejnovější aktualizace Azure Media Indexer, najdete v části [Media Services blogy](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Pomocí souborů manifestu a konfigurace pro indexování úlohy
Můžete zadat další podrobnosti pro indexování úkoly pomocí úlohy konfigurace. Můžete například zadat které metadat pro váš soubor média. Tato metadata používá stroj jazyk rozšířit jeho termínů a výrazně zlepšuje přesnost rozpoznávání řeči.  Také budete moci zadat požadované výstupní soubory.

Můžete také zpracovat více mediálních souborů najednou pomocí souboru manifestu.

Další informace najdete v tématu [přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Index prostředek
Následující metoda odešle soubor média jako prostředek a vytvoří úlohu indexování asset.

Pokud není zadán žádný konfigurační soubor, soubor média je indexovaný s všechna výchozí nastavení.

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
### <a id="output_files"></a>Výstupní soubory
Ve výchozím nastavení úlohu indexování generuje následující výstupní soubory. Soubory jsou uloženy v první výstupní asset.

Když je více než jeden soubor vstupními médii, Indexer generuje soubor manifestu pro výstupy úlohy, s názvem 'JobResult.txt'. Pro každý vstupní soubor média, výsledná AIB, SAMI, TTML, WebVTT a soubory – klíčové slovo postupně číslované a s názvem pomocí "Alias".

| Název souboru | Popis |
| --- | --- |
| **InputFileName.aib** |Zvukový soubor indexování objektů blob. <br/><br/> Zvukový soubor indexování Blob (AIB) je binární soubor, který lze vyhledat v systému Microsoft SQL server pomocí fulltextové vyhledávání.  Soubor AIB je výkonnější než soubory jednoduché titulku, protože obsahuje alternativami pro jednotlivých slov, což mnohem širší možnosti vyhledávání. <br/> <br/>Vyžaduje instalaci doplňku Indexer SQL na počítači spuštěné služby Microsoft SQL server 2008 nebo novější. Hledání AIB pomocí nástroje Microsoft SQL server fulltextové vyhledávání poskytuje přesnější výsledky hledání než hledání titulků soubory generované WAMI. Je to proto, že AIB obsahuje slovo alternativy, které zvukových podobné, zatímco soubory titulků obsahují nejvyšší word spolehlivosti pro každý segment zvukovém souboru. Pokud je hledání mluvené slovo upmost důležitost, pak se doporučuje použít AIB ve spojení se serverem Microsoft SQL Server.<br/><br/> Chcete-li stáhnout doplněk, klikněte na tlačítko <a href="http://aka.ms/indexersql">Azure Media Indexer SQL rozšíření</a>. <br/><br/>Je také možné využívat jiné vyhledávací weby, jako je například Apache Lucene/Solr jednoduše indexování video, na základě titulků a soubory XML – klíčové slovo, ale to bude mít za následek méně přesné výsledky hledání. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Uzavřené soubory popisek (kopie) v SAMI, TTML a WebVTT formáty.<br/><br/>Můžete se používají k Zpřístupněte soubory audia a videa pro osoby s postižením sluchu.<br/><br/>Uzavřené titulek soubory zahrnují značku <b>Recognizability</b> která skóre úlohu indexování, podle toho, jak rozpoznatelném řeči na videu zdroje.  Můžete použít hodnotu <b>Recognizability</b> na obrazovce výstupní soubory pro použitelnost. Nízké skóre bude znamenat nedostatečný indexování výsledky z důvodu kvalitu zvuku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |– Klíčové slovo a informace o soubory. <br/><br/>Soubor – klíčové slovo je soubor XML, který obsahuje klíčová slova extrahuje z obsahu řeči četnost a informace o posunu. <br/><br/>Informace o soubor je soubor ve formátu prostého textu, který obsahuje podrobné informace o jednotlivých termín rozpoznána. První řádek je speciální a obsahuje Recognizability skóre. Každý další řádek je karta oddělený seznam následující data: spuštění doba, čas ukončení, slovo nebo frázi, spolehlivosti. Časy jsou uvedeny v sekundách a je jistota je zadána jako číslo od 0-1. <br/><br/>Příklad řádku: "word 1,20 1,45 0.67" <br/><br/>Tyto soubory můžete použít z mnoha důvodů, jako je třeba provést analýzu řeči nebo vystavený pro vyhledávací weby, jako je Google nebo Bing, Microsoft SharePoint aby mediálních souborů zjistitelná nebo dokonce slouží k poskytování relevantnější reklamy. |
| **JobResult.txt** |Výstup manifestu, pouze při indexování více souborů, který obsahuje následující informace:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Chyba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Není-li všechny vstupní mediální soubory jsou úspěšně indexovaná, indexování úloha selže s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

## <a name="index-multiple-files"></a>Index více souborů
Následující metodu odešle více souborů médií jako prostředek a vytvoří úlohu indexování všechny tyto soubory v dávce.

Soubor manifestu s příponou ".lst" je vytvořený a nahrává do assetu. Soubor manifestu obsahuje seznam všech souborů asset. Další informace najdete v tématu [přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Částečně úspěšně úlohy
Není-li všechny vstupní mediální soubory jsou úspěšně indexovaná, indexování úloha selže s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

Jsou generovány stejné výstupy (jako úspěšně úloh). Najdete do výstupního souboru manifestu a zjistěte, které vstupní soubory se nepodařilo podle hodnoty ve sloupcích chyby. Pro vstupní soubory, které se nezdařilo, výsledná AIB, SAMI, TTML, WebVTT a klíčové slovo není generovány soubory.

### <a id="preset"></a> Přednastavení úloh pro Azure Media Indexer
Zpracování z Azure Media Indexer lze přizpůsobit zadáním nepovinná úloha přednastavení společně se úloha.  Následující text popisuje formát tento konfigurační soubor xml.

| Název | Vyžadovat | Popis |
| --- | --- | --- |
| **Vstup** |false (nepravda) |Soubory Asset, který chcete indexu.</p><p>Azure Media Indexer podporuje následující formáty souborů: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Můžete zadat název souboru (s) v **název** nebo **seznamu** atribut **vstupní** – element (jak je znázorněno níže). Pokud nezadáte, který soubor asset má index, primární soubor vydán. Pokud není nastaven žádný soubor primární asset, je indexovaný první soubor v vstupní prostředku.</p><p>Chcete-li explicitně zadat název souboru prostředku, proveďte:<br/>`<input name="TestFile.wmv">`<br/><br/>Můžete také indexu více asset soubory najednou (až 10). Použijte následující postup:<br/><br/><ol class="ordered"><li><p>Vytvořte textový soubor (soubor manifestu) a dejte mu .lst rozšíření. </p></li><li><p>Přidejte seznam všech názvů souborů asset ve vstupní asset do tohoto souboru manifestu. </p></li><li><p>Přidejte soubor thanifest (nahrávání) pro daný prostředek.  </p></li><li><p>Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>`<input list="input.lst">`</li></ol><br/><br/>Poznámka: Pokud přidáte více než 10 souborů k souboru manifestu, indexování úloha selže s kódem chyby 2006. |
| **metadata** |false (nepravda) |Metadata pro daný prostředek soubory, které používají pro úpravu termínů.  Užitečné připravit Indexer rozpoznat nestandardní termínů slova, jako jsou podstatná jména správné.<br/>`<metadata key="..." value="..."/>` <br/><br/>Můžete zadat **hodnoty** pro předdefinované **klíče**. Aktuálně jsou podporovány následující klíče:<br/><br/>"title" a "Popis" - použít pro přizpůsobení termínů k vylepšení jazyk, ve model pro úlohu a přesnosti rozpoznávání řeči.  Hodnoty počáteční hodnoty hledání na Internetu najít kontextově relevantní textu dokumentů, pomocí obsah k posílení interní slovník pro dobu trvání vašeho úkolu indexování.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkce** <br/><br/> Přidaná do verze 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR"). |false (nepravda) |Rozpoznávání řeči funkce má následující nastavení klíče:<table><tr><th><p>Klíč</p></th>        <th><p>Popis</p></th><th><p>Příklad hodnoty</p></th></tr><tr><td><p>Jazyk</p></td><td><p>Přirozeného jazyka rozpoznat v multimediálních souborů.</p></td><td><p>Angličtina, španělština</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>středníky oddělený seznam titulek požadovaný výstupní formáty (pokud existuje)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Logický příznak určující, zda je soubor AIB požadované (pro použití se službou SQL Server a zákazník Indexer IFilter).  Další informace najdete v tématu <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">pomocí AIB souborů pomocí Azure Media Indexer a SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Logický příznak určující, zda je požadovaný soubor XML – klíčové slovo.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Logický příznak určující, zda chcete-li vynutit úplnou titulky (bez ohledu na úroveň spolehlivosti).  </p><p>Výchozí hodnota je nastavena hodnota false, v takovém případě slova a slovní spojení, které mají méně než 50 % budou vynechaný výstupy poslední popisek a nahrazuje symbol tří teček ("...").  Na symbol tří teček jsou užitečné pro ovládací prvek popisek kvality a auditování.</p></td><td><p>True; False. </p></td></tr></table> |

### <a id="error_codes"></a>Kódy chyb
V případě chybu, by měl sestav Azure Media Indexer zpět jednu z následujících kódů chyb:

| Kód | Název | Možné příčiny |
| --- | --- | --- |
| 2000 |Neplatná konfigurace |Neplatná konfigurace |
| 2001 |Neplatné vstupní prostředky |Chybí vstupní prostředky nebo prázdný asset. |
| 2002 |Neplatný manifest |Manifest je prázdný nebo obsahuje neplatné položky manifestu. |
| 2003 |Nepodařilo se stáhnout soubor média |Neplatná adresa URL v souboru manifestu. |
| 2004 |Nepodporovaný protokol |Protokol adresy URL média není podporován. |
| 2005 |Nepodporovaný typ souboru |Typ souboru vstupní média není podporován. |
| 2006 |Příliš mnoho vstupní soubory |V manifestu vstupní jsou víc než 10 soubory. |
| 3000 |Soubor média dekódování se nezdařilo |Kodeků média není podporovaný. <br/>nebo<br/> Soubor poškozená média <br/>nebo<br/> Žádné zvukové datový proud v vstupními médii. |
| 4000 |Batch indexování byla částečně úspěšná |Některé soubory vstupní média se nezdařilo indexovat. Další informace najdete v tématu <a href="#output_files">výstupní soubory</a>. |
| ostatní |Vnitřní chyby |Obraťte se na tým podpory. indexer@microsoft.com |

## <a id="supported_languages"></a>Podporované jazyky
Aktuálně jsou podporované jazyky angličtinu a slovenštinu. Další informace najdete v tématu [v příspěvku blogu verze v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[AIB soubory pomocí Azure Media Indexer a SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexování mediálních souborů pomocí Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

