---
title: Indexování mediálních souborů pomocí Azure Media Indexer
description: Azure Media Indexer umožňuje provést prohledávatelný obsah vašich mediálních souborů a vygenerovat fulltextový přepis pro uzavřené titulků a klíčová slova. Toto téma ukazuje, jak používat Media Indexer.
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
ms.date: 02/10/2019
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: 4bc11e4f622943cdb72253ecd758c27a026495fa
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991154"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexování mediálních souborů pomocí Azure Media Indexer
Azure Media Indexer umožňuje provést prohledávatelný obsah vašich mediálních souborů a vygenerovat fulltextový přepis pro uzavřené titulků a klíčová slova. Mediální soubory můžete zpracovávat po jednom nebo v dávkách.  

> [!IMPORTANT]
> Názvy při indexování obsahu, ujistěte se, že používají soubory multimédií, které mají jasné řeči (bez pozadí Hudba, šum, účinky nebo mikrofon hiss). Tady je několik příkladů odpovídající obsahu: zaznamenané schůzky, přednášek nebo prezentace. Následující obsah, nemusí být vhodný pro indexování: filmy, televizní pořady, cokoli pomocí smíšené audio a zvukové efekty špatně zaznamenaného obsahu s šum na pozadí (hiss).
> 
> 

Úloha indexování můžete vygenerovat následující výstup:

* Soubory s titulky uvedené v následujících formátech: **SÁMŠTINA**, **TTML**, a **WebVTT**.
  
    Soubory s titulky zahrnují značku Recognizability, která skóre, které se úloha indexování na základě toho, jak rozpoznat řeč v zdroje videa.  Hodnota Recognizability slouží k obrazovce výstupních souborů pro použitelnost. Nízké skóre znamená špatné indexování výsledky z důvodu kvality zvuku.
* Soubor klíčových slov (XML).
* Zvuk indexování objektů blob v souboru (AIB) pro použití se serverem SQL server.
  
    Další informace najdete v tématu [pomocí AIB souborů pomocí Azure Media Indexer a systému SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Tento článek popisuje, jak vytvořit indexování úloh do **indexu prostředek** a **indexu více souborů**.

Nejnovější aktualizace pro Azure Media Indexer, naleznete v tématu [blogy Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Použití souborů manifestu a konfigurace pro indexování úloh
Můžete zadat další podrobnosti pro indexování úloh pomocí úloh konfigurace. Můžete například zadat které metadat pro mediální soubor. Tato metadata se používá modul jazyka a rozbalte jeho slovníku a výrazně zlepšuje přesnost rozpoznávání řeči.  Také budete moct zadat požadovaný výstupní soubory.

Mediální soubory můžete také najednou zpracovat pomocí souboru manifestu.

Další informace najdete v tématu [přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Index prostředku
Následující metoda nahraje soubor média jako prostředek a vytvoří úlohu k indexování assetu.

Pokud není zadán žádný konfigurační soubor, do souboru média je indexované všechna výchozí nastavení.

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
Ve výchozím nastavení úloha indexování generuje následující výstupní soubory. Soubory jsou uloženy v první výstupní asset.

Po více než jeden soubor vstupní media Indexer generuje soubor manifestu pro výstupy úlohy, s názvem "JobResult.txt". Pro každý vstupní soubor média, výsledný AIB, SAMI, TTML, WebVTT a soubory – klíčové slovo, jsou postupně číslované a pojmenované pomocí "Alias".

| Název souboru | Popis |
| --- | --- |
| **InputFileName.aib** |Zvukový soubor indexování objektů blob. <br/><br/> Zvukový soubor indexování objektů Blob (AIB) je binární soubor, který lze prohledávat pomocí fulltextového vyhledávání serveru Microsoft SQL Server.  Soubor AIB je výkonnější než jednoduché titulek soubory, protože obsahuje alternativy pro každé slovo umožňuje mnohem bohatší možnosti vyhledávání. <br/> <br/>Vyžaduje instalaci doplňku Indexer SQL na počítači spuštěný Microsoft SQL server 2008 nebo novější. Hledání AIB pomocí Microsoft SQL server fulltextové vyhledávání poskytuje přesnější výsledky hledání než hledání skryté titulky soubory generované záznamem WAMI. Je to proto, AIB obsahuje náhradní řešení aplikace word, která zní podobně, že soubory s titulky obsahují nejvyšší slovo spolehlivosti pro každý segment zvuku. Je-li vyhledat mluveného slova upmost důležitosti, se doporučuje použít AIB ve spojení se serverem Microsoft SQL Server.<br/><br/> Chcete-li stáhnout doplněk, klikněte na tlačítko <a href="https://aka.ms/indexersql">Azure Media Indexer SQL doplněk</a>. <br/><br/>Je také možné využívat další vyhledávací weby, jako je například Apache Lucene/Solr jednoduše indexování video na základě skryté titulky a soubory XML – klíčové slovo, ale to bude mít za následek méně přesné výsledky hledání. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Zavřenými soubory titulků (kopie) ve formátu WebVTT, SAMI a TTML.<br/><br/>Umožňuje, aby byly zvukových souborů a videosouborů osobám s postižením sluchu.<br/><br/>Uzavřené titulek soubory zahrnují značku <b>Recognizability</b> který stanoví skóre na základě toho, jak rozpoznat řeč v zdroje videa úloha indexování.  Můžete použít hodnotu <b>Recognizability</b> na obrazovce výstupních souborů pro použitelnost. Nízké skóre znamená špatné indexování výsledky z důvodu kvality zvuku. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Klíčové slovo a informace o souborech. <br/><br/>Soubor s klíčovým slovem je soubor XML, který obsahuje klíčová slova extrahovaná z mluveného obsahu s frekvencí a informace o posunu. <br/><br/>Informace o soubor je soubor ve formátu prostého textu, který obsahuje podrobné informace o každé období rozpoznán. První řádek je speciální a obsahuje Recognizability skóre. Každý další řádek je seznam oddělený tabulátory následující data: start čas, koncový čas, slovo nebo fráze a spolehlivosti. Časy jsou uvedeny v řádu sekund a důvěry je zadána jako číslo od 0 – 1. <br/><br/>Příklad řádku: "1,20 1,45 word 0.67" <br/><br/>Tyto soubory lze umožňuje mnoha důvodů, jako je třeba udělat řečovou analýzu, nebo vystavené pro vyhledávací weby, jako je Bing, Google nebo Microsoft SharePoint, aby mediální soubory zjistitelnější a dokonce využívat k poskytování zobrazovat reklamy. |
| **JobResult.txt** |Výstup manifestu, k dispozici pouze názvy při indexování více souborů, který obsahuje následující informace:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Chyba</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Není-li všechny vstupními multimediálními soubory jsou indexovány úspěšně, úloha indexování selže s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

## <a name="index-multiple-files"></a>Index více souborů
Následující metoda nahraje více mediálních souborů jako prostředek a vytvoří úlohu k indexování všech těchto souborů v dávce.

Soubor manifestu s příponou ".lst" je vytvořený a nahrávání do assetu. Soubor manifestu obsahuje seznam všech souborů prostředků. Další informace najdete v tématu [přednastavení úloh pro Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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
Není-li všechny vstupními multimediálními soubory jsou indexovány úspěšně, úloha indexování selže s kódem chyby 4000. Další informace najdete v tématu [kódy chyb](#error_codes).

Vygenerují se stejnou výstupy (jako úspěšné úlohy). Mohou odkazovat na výstupní soubor manifestu a zjistěte, vstupní soubory, které jsou při selhání, podle hodnoty ve sloupcích chyby. Pro vstupní soubory, které selhaly, výsledný AIB, SAMI, TTML, WebVTT a – klíčové slovo není se vygeneruje soubory.

### <a id="preset"></a> Předvolby úloh pro Azure Media Indexer
Zpracování z Azure Media Indexer je možné přizpůsobit tím, že poskytuje nepovinná úloha přednastavení spolu s úkolu.  Následující část popisuje formátu tento konfigurační soubor xml.

| Název | Vyžadovat | Popis |
| --- | --- | --- |
| **Vstup** |false (nepravda) |Soubory prostředků, kterou chcete indexu.</p><p>Azure Media Indexer podporuje následující formáty souborů médií: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Můžete zadat název souboru (s) v **název** nebo **seznamu** atribut **vstupní** – element (jak je vidět níže). Pokud nezadáte soubor, který prostředek se index, vyberou se primární soubor. Pokud není nastaven žádný soubor primární prostředek, je prvním souboru v vstupní asset indexovat.</p><p>Chcete-li explicitně zadat název souboru prostředku, proveďte:<br/>`<input name="TestFile.wmv">`<br/><br/>Můžete také indexu více souborů prostředků najednou (až 10 soubory). Použijte následující postup:<br/><br/><ol class="ordered"><li><p>Vytvořte textový soubor (soubor manifestu) a nastavte ho .lst příponu. </p></li><li><p>Přidáte seznam všech názvů souborů prostředků v vstupní asset pro tento soubor manifestu. </p></li><li><p>Přidání souboru (nahrát) v manifestu do prostředku.  </p></li><li><p>Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>`<input list="input.lst">`</li></ol><br/><br/>Poznámka: Pokud chcete přidat více než 10 souborů do souboru manifestu, úloha indexování selže s kódem chyby 2006. |
| **metadata** |false (nepravda) |Metadata pro zadaný prostředek soubory, které používají pro adaptaci slovníku.  Užitečné připravit indexeru rozpoznat nestandardní slovník slova, jako je například správných podstatná jména.<br/>`<metadata key="..." value="..."/>` <br/><br/>Můžete zadat **hodnoty** pro předdefinované **klíče**. Aktuálně jsou podporovány následující klíče:<br/><br/>"title" a "Popis" - použitý pro adaptaci slovníku upravit jazyk modelu pro vaši úlohu a zlepšit přesnost rozpoznávání řeči.  Hodnoty počáteční hodnoty hledání na Internetu najít kontextově relevantní textové dokumenty pomocí obsah k posílení interní slovník pro dobu trvání úlohy indexování.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funkce** <br/><br/> Byly přidány ve verzi 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR"). |false (nepravda) |Rozpoznávání řeči funkce má následující nastavení klíče:<table><tr><th><p>Klíč</p></th>        <th><p>Popis</p></th><th><p>Příklad hodnoty</p></th></tr><tr><td><p>Jazyk</p></td><td><p>Přirozeného jazyka k rozpoznat v multimediálním souboru.</p></td><td><p>Angličtina, španělština</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>středníkem oddělený seznam titulek požadovaný výstupní formáty (pokud existuje)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Logický příznak určující, zda soubor AIB vyžádáním (pro použití s SQL serverem a Indexer IFilter zákazníka).  Další informace najdete v tématu <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">pomocí AIB souborů pomocí Azure Media Indexer a systému SQL Server</a>.</p></td><td><p>Hodnota TRUE; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Logický příznak určující, zda soubor XML klíčových slov je povinný.</p></td><td><p>Hodnota TRUE; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Logický příznak určující, jestli se mají vynutit úplné titulků (bez ohledu na úroveň spolehlivosti).  </p><p>Výchozí hodnota je false, v takovém případě jsou slova a slovní spojení, které mají méně než 50 % spolehlivosti vynecháno z výstupů poslední popisek a nahrazuje tři tečky ("...").  Tři tečky jsou užitečné pro ovládací prvek popisek kvality a auditování.</p></td><td><p>Hodnota TRUE; False. </p></td></tr></table> |

### <a id="error_codes"></a>Kódy chyb
V případě chyby, ohlaste Azure Media Indexer zpět jednu z následující kódy chyb:

| Kód | Název | Možné důvody |
| --- | --- | --- |
| 2000 |Neplatná konfigurace |Neplatná konfigurace |
| 2001 |Neplatné vstupní prostředky |Chybí vstupní prostředky nebo prázdný asset. |
| 2002 |Neplatný manifest |Manifest je prázdný nebo obsahuje neplatné položky manifestu. |
| 2003 |Nepovedlo se stáhnout soubor média |Neplatná adresa URL v souboru manifestu. |
| 2004 |Nepodporovaný protokol |Protokol adresy URL média není podporován. |
| 2005 |Nepodporovaný typ souboru |Vstupními multimediálními typ souboru se nepodporuje. |
| 2006 |Moc velký počet vstupních souborů |Ve vstupu manifestu jsou více než 10 soubory. |
| 3000 |Nepovedlo se dekódovat mediální soubor |Kodek média není podporovaný. <br/>nebo<br/> Poškozená mediální soubor <br/>nebo<br/> Žádný zvukový datový proud ve vstupní média. |
| 4000 |Indexování služby batch částečně úspěšné |Některé vstupními multimediálními soubory se nepodařilo se indexovat. Další informace najdete v tématu <a href="#output_files">výstupní soubory</a>. |
| ostatní |Interní chyby |Obraťte se prosím na tým podpory. indexer@microsoft.com |

## <a id="supported_languages"></a>Podporované jazyky
V současné době jsou podporovány jazyky angličtinu a slovenštinu. Další informace najdete v tématu [příspěvku na blogu v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[AIB soubory pomocí Azure Media Indexer a systému SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexování mediálních souborů pomocí Azure Media Indexer 2 ve verzi Preview](media-services-process-content-with-indexer2.md)

