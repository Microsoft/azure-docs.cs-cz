---
title: 'Kurz: C# a AI přes objekty BLOB Azure'
titleSuffix: Azure Cognitive Search
description: Krokování příklad extrakce textu a zpracování přirozeného jazyka přes obsah v úložišti objektů Blob pomocí C# a Azure Cognitive Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851134"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Kurz: Ke generování prohledávatelného obsahu z objektů BLOB Azure použijte C# a AI.

Pokud máte nestrukturovaný text nebo image v úložišti objektů Blob Azure, [kanál obohacení AI](cognitive-search-concept-intro.md) můžete extrahovat informace a vytvořit nový obsah, který je užitečný pro fulltextové vyhledávání nebo scénáře dolování znalostí. V tomto kurzu Jazyka C# použijte optické rozpoznávání znaků (OCR) na obrázky a provádět zpracování přirozeného jazyka k vytvoření nových polí, které můžete využít v dotazech, omezujících okolností a filtry.

Tento kurz používá c# a [.NET SDK](https://aka.ms/search-sdk) k provedení následujících úkolů:

> [!div class="checklist"]
> * Začněte s aplikačními soubory a image v úložišti objektů Blob Azure.
> * Definujte kanál pro přidání rozpoznávání OCR, extrakce textu, detekce jazyka, rozpoznávání entit a klíčových frází.
> * Definujte index pro uložení výstupu (nezpracovaný obsah plus dvojice název-hodnota generovaný kanálem).
> * Spusťte kanál ke spuštění transformace a analýzy a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohaté syntaxe dotazu.

Pokud nemáte předplatné Azure, otevřete si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Můžete použít bezplatnou službu pro tento kurz. Bezplatná vyhledávací služba vás omezí na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve službě prostor pro přijetí nových zdrojů.

## <a name="download-files"></a>Stažení souborů

1. Otevřete tuto [složku OneDrivu](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klepněte pravým tlačítkem myši na soubor zip a vyberte **možnost Extrahovat vše**. K dispozici je 14 souborů různých typů. Použijte všechny z nich pro tento kurz.

## <a name="1---create-services"></a>1 - Vytváření služeb

Tento kurz používá Azure Cognitive Search pro indexování a dotazy, Cognitive Services na back-endu pro obohacení AI a úložiště objektů blob Azure k poskytování dat. Tento kurz zůstane pod bezplatným přidělením 20 transakcí na indexer za den ve službách Cognitive Services, takže jediné služby, které potřebujete vytvořit, jsou vyhledávání a ukládání.

Pokud je to možné, vytvořte ve stejné oblasti a skupině prostředků pro blízkost a správu. V praxi může být váš účet Azure Storage v libovolné oblasti.

### <a name="start-with-azure-storage"></a>Začněte s Azure Storage

1. [Přihlaste se na portál Azure](https://portal.azure.com/) a klikněte na + Vytvořit **prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště společnosti Microsoft.

   ![Vytvořit účet úložiště](media/cognitive-search-tutorial-blob/storage-account.png "Vytvořit účet úložiště")

1. Na kartě Základy jsou požadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli spravovat společně.

   + **Název účtu úložiště**. Pokud si myslíte, že můžete mít více prostředků stejného typu, použijte název k oddělení podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění**. Pokud je to možné, zvolte stejné umístění, které se používá pro Azure Cognitive Search a Cognitive Services. Jedno místo ruší poplatky za šířku pásma.

   + **Typ účtu**. Zvolte výchozí, *StorageV2 (pro obecné účely v2)*.

1. Chcete-li službu vytvořit, klepněte na **tlačítko Revize + Vytvořit.**

1. Po jeho vytvoření kliknutím na **Přejít na zdroj** otevřete stránku Přehled.

1. Klikněte na **službu Objektů blob.**

1. Klepnutím na **tlačítko + Kontejner** vytvořte kontejner a pojmenujte jej *basic-demo-data-pr*.

1. Vyberte *základní demo-data-pr* a pak kliknutím na **tlačítko Nahrát** otevřete složku, do které jste uložili soubory pro stahování. Vyberte všech čtrnáct souborů a klepnutím na **tlačítko OK** nahrajte.

   ![Nahrání ukázkových souborů](media/cognitive-search-quickstart-blob/sample-data.png "Nahrání ukázkových souborů")

1. Než opustíte Azure Storage, získejte připojovací řetězec, abyste mohli formulovat připojení v Azure Cognitive Search. 

   1. Přejděte zpět na stránku Přehled vašeho účtu úložiště (jako příklad jsme použili *blobstragewestus).* 
   
   1. V levém navigačním podokně vyberte **přístupové klávesy** a zkopírujte jeden z připojovacích řetězců. 

   Připojovací řetězec je adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Uložte připojovací řetězec do programu Poznámkový blok. Budete ji potřebovat později při nastavování připojení ke zdroji dat.

### <a name="cognitive-services"></a>Cognitive Services

Obohacení umělou a ii je podpořeno službami Cognitive Services, včetně analýzy textu a počítačového vidění pro přirozené zpracování jazyka a obrazu. Pokud vaším cílem bylo dokončit skutečný prototyp nebo projekt, by v tomto bodě poskytování cognitive services (ve stejné oblasti jako Azure Cognitive Search), takže můžete připojit k operacím indexování.

Pro toto cvičení však můžete přeskočit zřizování prostředků, protože Azure Cognitive Search můžete připojit k cognitive services na pozadí a poskytnout 20 volných transakcí na spuštění indexeru. Vzhledem k tomu, že tento kurz používá 7 transakcí, je povolená povolenka dostatečná. Pro větší projekty plánujte na zřizování služeb Cognitive Services na úrovni S0 s průběžným platbami. Další informace naleznete v [tématu Attach Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí komponentou je Azure Cognitive Search, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto návodu můžete použít úroveň Free. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíče api správce a adresy URL pro Azure Cognitive Search

K interakci se službou Azure Cognitive Search budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

   Získejte také klíč dotazu. Je osvědčeným postupem pro vydávání požadavků na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správce a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2 - Nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který lze spustit v centru .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Sada Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které umožňují spravovat indexy, zdroje dat, indexery a dovednosti, stejně jako nahrávat a spravovat dokumenty a spouštět dotazy, aniž byste museli řešit podrobnosti http a json. Tyto klientské knihovny jsou všechny distribuovány jako balíčky NuGet.

Pro tento projekt nainstalujte verzi `Microsoft.Azure.Search` 9 nebo novější balíček NuGet.

1. Otevřete konzolu Správce balíčků. Vyberte **možnost Nástroje,** > vyberte**konzolu Správce balíčků****nástroje** > NuGet . 

1. Přejděte na [stránku balíčku Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Vyberte nejnovější verzi (9 nebo novější).

1. Zkopírujte příkaz Správce balíčků.

1. Vraťte se do konzoly Správce balíčků a spusťte příkaz, který jste zkopírovali v předchozím kroku.

Dále nainstalujte `Microsoft.Extensions.Configuration.Json` nejnovější balíček NuGet.

1. Vyberte **nástroje** > **NuGet Správce** > balíčků**Spravovat balíčky NuGet pro řešení...**. 

1. Klikněte na **Procházet** a vyhledejte balíček `Microsoft.Extensions.Configuration.Json` NuGet. 

1. Vyberte balíček, vyberte projekt, potvrďte, že verze je nejnovější stabilní verze, a klepněte na tlačítko **Instalovat**.

### <a name="add-service-connection-information"></a>Přidání informací o připojení služby

1. Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte **Přidat** > **novou položku...** . 

1. Pojmenujte `appsettings.json` soubor a vyberte **Přidat**. 

1. Zahrňte tento soubor do výstupního adresáře.
    1. Klepněte pravým `appsettings.json` tlačítkem myši na **položku Vlastnosti**. 
    1. Změňte hodnotu **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.

1. Zkopírujte níže uvedený JSON do nového souboru JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Přidejte informace o vyhledávací službě a účtu úložiště objektů blob. Připomeňme, že tyto informace můžete získat z kroků zřizování služeb uvedených v předchozí části.

### <a name="add-namespaces"></a>Přidání oborů názvů

V `Program.cs`aplikace přidejte následující obory názvů.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Vytvoření klienta

Vytvořte instanci třídy v `SearchServiceClient` části `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`vytvoří nové `SearchServiceClient` pomocí hodnoty, které jsou uloženy v konfiguračním souboru aplikace (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> Třída `SearchServiceClient` spravuje připojení k vyhledávací službě. Aby se zabránilo otevírání příliš mnoha připojení, měli byste se pokusit sdílet jednu instanci třídy `SearchServiceClient` v rámci aplikace, pokud je to možné. Její metody jsou bezpečné pro přístup z více vláken a takové sdílení umožňují.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Přidat funkci pro ukončení programu během poruchy

Tento kurz je určen k pochopení každého kroku kanálu indexování. Pokud existuje kritický problém, který brání programu ve vytváření zdroje dat, sady dovedností, indexu nebo indexeru, program vytvoří chybovou zprávu a ukončí, aby bylo možné problém pochopit a vyřešit.

Přidat `ExitProgram` `Main` do zpracování scénářů, které vyžadují ukončení programu.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - Vytvoření potrubí

V Azure Cognitive Search, Zpracování AI dochází během indexování (nebo přilnutí dat). Tato část návodu vytvoří čtyři objekty: zdroj dat, definice indexu, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

`SearchServiceClient` obsahuje vlastnost `DataSources`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytvoření, seznam, aktualizace nebo odstranění zdrojů dat Azure Cognitive Search.

Vytvořte `DataSource` novou instanci voláním `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage`vyžaduje zadat název zdroje dat, připojovací řetězec a název kontejneru objektu blob.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Pro úspěšný požadavek metoda vrátí zdroj dat, který byl vytvořen. Pokud dojde k potížím s požadavkem, jako je například neplatný parametr, metoda vyvolá výjimku.

Nyní přidejte `Main` řádek a `CreateOrUpdateDataSource` zavolejte funkci, kterou jste právě přidali.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Sestavte a spusťte řešení. Vzhledem k tomu, že se jedná o váš první požadavek, zkontrolujte na webu Azure Portal a potvrďte, že zdroj dat byl vytvořen v Azure Cognitive Search. Na stránce řídicího panelu vyhledávací služby ověřte, že dlaždice Zdroje dat má novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice zdrojů dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice zdrojů dat na portálu")

### <a name="step-2-create-a-skillset"></a>Krok 2: Vytvoření sady dovedností

V této části definujete sadu kroků obohacení, které chcete použít pro vaše data. Každý krok obohacení se nazývá *dovednost* a sada kroků obohacení *skillset*. Tento kurz používá [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) pro skillset:

+ [Optické rozpoznávání znaků](cognitive-search-skill-ocr.md) pro rozpoznání tištěného a ručně psaného textu v obrazových souborech.

+ [Text Sloučení](cognitive-search-skill-textmerger.md) pro sloučení textu z kolekce polí do jednoho pole.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Text Rozdělit](cognitive-search-skill-textsplit.md) rozdělit velký obsah na menší bloky před voláním klíčové fráze extrakce dovednost a schopnost rozpoznávání entity. Extrakce klíčových frází a rozpoznávání entit přijímají vstupy o 50 000 znacích nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů blob.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze

Během počátečního zpracování Azure Cognitive Search rozlouskne každý dokument číst obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. Jako takové nastavte vstup ```"/document/content"``` tak, aby používal tento text. 

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Dovednost Rozpoznávání OCR

Dovednost **Rozpoznávání OCR** extrahuje text z obrázků. Tato dovednost předpokládá, že existuje normalized_images pole. Chcete-li generovat toto pole, dále ```"imageAction"``` v kurzu nastavíme ```"generateNormalizedImages"```konfiguraci v definici indexeru na .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Sloučit dovednost

V této části vytvoříte dovednost **Sloučit,** která sloučí pole obsahu dokumentu s textem vytvořeným dovedností Rozpoznávání OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Dovednost detekce jazyka

Dovednost **Rozpoznávání jazyka** detekuje jazyk vstupního textu a hlásí jeden kód jazyka pro každý dokument odeslaný na žádost. Výstup dovednosti **detekce jazyka** použijeme jako součást vstupu do dovednosti **Rozdělení textu.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Dovednost rozdělení textu

Níže **rozdělená** dovednost rozdělí text podle stránek a omezí délku stránky `String.Length`na 4 000 znaků měřeno písmenem . Algoritmus se pokusí rozdělit text na bloky, `maximumPageLength` které jsou maximálně ve velikosti. V tomto případě algoritmus bude dělat to nejlepší prolomit větu na hranici věty, takže `maximumPageLength`velikost bloku může být o něco menší než .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Dovednost rozpoznávání entity

Tato `EntityRecognitionSkill` instance je nastavena `organization`na rozpoznávání typu kategorie . Dovednost **Rozpoznávání entit** může také `person` `location`rozpoznat typy kategorií a .

Všimněte si, že pole ```"/document/pages/*"``` "kontext" je nastaveno na hvězdičku, což ```"/document/pages"```znamená, že krok obohacení je volán pro každou stránku v části .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Dovednost extrakce klíčových frází

Stejně `EntityRecognitionSkill` jako instance, která byla právě vytvořena, je pro každou stránku dokumentu volána dovednost **Extrakce klíčových frází.**

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Vytvoření a vytvoření sady dovedností

Sestavte `Skillset` pomocí dovedností, které jste vytvořili.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Do položky `Main`přidejte následující řádky.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Krok 3: Vytvoření indexu

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Vytvořit třídu DemoIndex

Pole pro tento index jsou definována pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. 

Přidáme třídu modelu do nového souboru C#. Klikněte pravým tlačítkem **Add** > myši na projekt a vyberte přidat `DemoIndex.cs`novou**položku...**, vyberte "Třída" a pojmenujte soubor , pak vyberte **Přidat**.

Nezapomeňte označit, že chcete použít typy `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` z oborů názvů a.

Přidejte níže uvedenou `DemoIndex.cs` definici třídy modelu a zahrňte ji do stejného oboru názvů, kde vytvoříte index.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
``` -->

Nyní, když jste definovali třídu `Program.cs` modelu, můžete vytvořit definici indexu poměrně snadno. Název tohoto indexu bude `demoindex`. Pokud index s tímto názvem již existuje, bude odstraněn.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Během testování můžete zjistit, že se pokoušíte vytvořit index více než jednou. Z tohoto důvodu zkontrolujte, zda index, který se chystáte vytvořit již existuje před pokusem o jeho vytvoření.

Do položky `Main`přidejte následující řádky.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```
 -->

Další informace o definování indexu najdete v tématu [vytvoření indexu (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Vytvoření a spuštění indexeru

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí.

+ PoleMapování jsou zpracovány před skillset, mapování zdrojových polí ze zdroje dat na cílová pole v indexu. Pokud jsou názvy a typy polí na obou koncích stejné, není vyžadováno žádné mapování.

+ OutputFieldMappings jsou zpracovány po skillset, odkazování sourceFieldNames, které neexistují, dokud dokument praskání nebo obohacení vytvoří. TargetFieldName je pole v indexu.

Kromě připojení vstupů k výstupům můžete také použít mapování polí ke sloučení datových struktur. Další informace naleznete v tématu [Mapování obohacených polí na index s prohledávatelným způsobem](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Do položky `Main`přidejte následující řádky.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Očekávejte, že vytvoření indexeru bude trvat trochu času na dokončení. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumejte vytváření indexeru

Kód se ```"maxFailedItems"``` nastaví na -1, který instruuje indexovací modul ignorovat chyby během importu dat. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Všimněte ```"dataToExtract"``` si také, že je nastavena na ```"contentAndMetadata"```. Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. Sada ```"imageAction"``` ```"generateNormalizedImages"``` konfigurace v kombinaci s dovedností OCR a dovedností sloučení textu říká indexeru, aby extrahoval text z obrázků (například slovo "stop" ze znaménku stop provozu) a vkládal jej jako součást pole obsahu. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Sledování indexování

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda je indexer `GetStatus` stále spuštěn, použijte metodu.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo`představuje aktuální stav a historii spuštění indexeru.

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy).

Do položky `Main`přidejte následující řádky.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Hledání

Po dokončení indexování můžete spustit dotazy, které vrátí obsah jednotlivých polí. Ve výchozím nastavení Azure Cognitive Search vrátí prvních 50 nejlepších výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v tématu [Jak zobrazit výsledky stránky v Azure Cognitive Search](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

Do položky `Main`přidejte následující řádky.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`vytvoří nové `SearchIndexClient` pomocí hodnoty, které jsou uloženy v konfiguračním souboru aplikace (appsettings.json). Všimněte si, že se používá klíč rozhraní API dotazu vyhledávací služby a není klíč správce.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Přidejte následující kód do `Main`. První try-catch vrátí definici indexu s názvem, typem a atributy každého pole. Druhým je parametrizovaný dotaz, `Select` kde určuje, která pole mají `organizations`být zahrnuta do výsledků, například . Vyhledávací řetězec `"*"` vrátí veškerý obsah jednoho pole.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Tento postup opakujte pro další pole: obsah, jazykKód, klíčové fráze a organizace v tomto cvičení. Pomocí vlastnosti [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) můžete vrátit více polí pomocí seznamu odděleného čárkami.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Ukázkový kód pro tento kurz kontroluje existující objekty a odstraní je, takže můžete znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů, zdrojů dat a dovedností.

## <a name="takeaways"></a>Shrnutí

Tento kurz demonstroval základní kroky pro vytváření obohaceného kanálu indexování prostřednictvím vytváření součástí: zdroj dat, skillset, index a indexer.

Byly zavedeny [vestavěné dovednosti](cognitive-search-predefined-skills.md) spolu s definicí dovedností a mechanikou řetězení dovedností prostřednictvím vstupů a výstupů. Také jste `outputFieldMappings` zjistili, že v definici indexeru je vyžadovánpro směrování obohacené hodnoty z kanálu do indexu prohledávatelné na Azure Cognitive Search služby.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni se všemi objekty v kanálu obohacení AI, podívejme se blíže na definice dovedností a individuální dovednosti.

> [!div class="nextstepaction"]
> [Jak vytvořit sadu dovedností](cognitive-search-defining-skillset.md)
