---
title: Kurz C# použití AI v Azure BLOBs
titleSuffix: Azure Cognitive Search
description: Projděte si příklad extrakce textu a zpracování přirozeného jazyka přes obsah ve službě BLOB Storage pomocí jazyka C# a sady Azure Kognitivní hledání .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: da7a80842bec68fde8cc44401bb04c2dd061741f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787954"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Kurz: prohledávatelný obsah generovaný AI z objektů blob Azure pomocí sady .NET SDK

Pokud máte v úložišti objektů BLOB v Azure nestrukturovaný text nebo obrázky, [kanál pro rozšíření AI](cognitive-search-concept-intro.md) může extrahovat informace a vytvořit nový obsah pro scénáře fulltextového vyhledávání nebo dolování ve znalostní bázi. 

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Definování kanálu, který překročí objekty BLOB pomocí rozpoznávání OCR, rozpoznávání jazyka, entit a klíčových frází.
> * Spuštění kanálu pro vyvolání transformací a vytvoření a načtení indexu vyhledávání.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohatou syntaxí dotazu.

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="overview"></a>Přehled

V tomto kurzu se používá C# a Klientská knihovna **Azure.Search.Documents** k vytvoření zdroje dat, indexu, indexeru a dovednosti.

Dovednosti využívá integrované dovednosti založené na rozhraní API služeb Cognitive Services. Mezi kroky v kanálu patří optické rozpoznávání znaků (OCR) na obrázcích, rozpoznávání jazyka na textu, extrakce klíčových frází a rozpoznávání entit (organizace). Nové informace jsou uloženy v nových polích, která lze využít v dotazech, omezujících vlastnostech a filtrech.

## <a name="prerequisites"></a>Předpoklady

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [ Balíček NuGetAzure.Search.Documents 11. x](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> Pro tento kurz můžete použít bezplatnou vyhledávací službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-sample-data"></a>Stáhněte si ukázková data

Ukázková data se skládají ze 14 souborů smíšeného obsahu, které v pozdějším kroku nahrajete do úložiště objektů BLOB v Azure.

1. Otevřete tuto [složku OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klikněte pravým tlačítkem na soubor zip a vyberte **Extrahovat vše** . Existuje 14 souborů různých typů. Pro toto cvičení budete používat 7.

Můžete si také stáhnout zdrojový kód pro tento kurz. Zdrojový kód je ve složce **tutorial-AI-obohacení/V11** v úložišti [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Cognitive Services v back-endu pro rozšíření AI a Azure Blob Storage k poskytnutí dat. Tento kurz zůstává v rámci bezplatného přidělení 20 transakcí na indexer za den v Cognitive Services, takže je potřeba, abyste vytvořili jenom služby pro vyhledávání a úložiště.

Pokud je to možné, vytvořte oba ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být váš účet Azure Storage v jakékoli oblasti.

### <a name="start-with-azure-storage"></a>Začínáme s Azure Storage

1. [Přihlaste se k Azure Portal](https://portal.azure.com/) a klikněte na **+ vytvořit prostředek** .

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště od Microsoftu.

   ![Vytvořit účet úložiště](media/cognitive-search-tutorial-blob/storage-account.png "Vytvořit účet úložiště")

1. Na kartě základy jsou vyžadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   * **Skupina prostředků** . Vyberte existující jednu nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli souhrnně spravovat.

   * **Název účtu úložiště:** Pokud se domníváte, že máte více prostředků stejného typu, použijte název k jednoznačnému odstranění podle typu a oblasti, například *blobstoragewestus* . 

   * **Umístění:** Pokud je to možné, vyberte stejné umístění, které se používá pro Azure Kognitivní hledání a Cognitive Services. Jediné místo má za vyrušení poplatky za šířku pásma.

   * **Druh účtu** . Vyberte výchozí *StorageV2 (obecné účely v2)* .

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** službu vytvořte.

1. Po vytvoření klikněte na **Přejít k prostředku** a otevřete stránku Přehled.

1. Klikněte na služba **BLOB** Service.

1. Kliknutím na **+ kontejner** vytvořte kontejner a pojmenujte ho *ozubeného kola-Search-demo* .

1. Vyberte *ozubeného kola-Search-demo* a potom kliknutím na **Odeslat** otevřete složku, kam jste uložili soubory ke stažení. Vyberte všechny čtrnáct souborů a kliknutím na **OK** nahrávání nahrajte.

   ![Nahrání ukázkových souborů](media/cognitive-search-quickstart-blob/sample-data.png "Nahrání ukázkových souborů")

1. Než ponecháte Azure Storage, Získejte připojovací řetězec, abyste mohli formulovat připojení v Azure Kognitivní hledání. 

   1. Přejděte zpět na stránku Přehled vašeho účtu úložiště (jako příklad jsme použili *blobstoragewestus* ). 

   1. V levém navigačním podokně vyberte **přístupové klíče** a zkopírujte jeden z připojovacích řetězců. 

   Připojovací řetězec je adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Uložte připojovací řetězec do poznámkového bloku. Budete ho potřebovat později při nastavování připojení ke zdroji dat.

### <a name="cognitive-services"></a>Cognitive Services

Obohacení AI je zajištěno Cognitive Services, včetně Analýza textu a Počítačové zpracování obrazu pro zpracování přirozeného jazyka a obrazu. Pokud by vaším cílem bylo dokončit skutečný prototyp nebo projekt, měli byste v tomto okamžiku zřídit Cognitive Services (ve stejné oblasti jako Azure Kognitivní hledání), abyste ho mohli připojit k operacím indexování.

Pro toto cvičení ale můžete přeskočit zřizování prostředků, protože Azure Kognitivní hledání se může připojit k Cognitive Services na pozadí a poskytnout vám 20 bezplatných transakcí na indexer. Vzhledem k tomu, že tento kurz používá 14 transakcí, je bezplatné přidělení dostatečné. Pro větší projekty Naplánujte zřizování Cognitive Services na úrovni průběžných plateb. Další informace najdete v tématu věnovaném [připojení Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md) nebo [najít existující vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) ve vašem předplatném.

K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopírování klíče rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

Abyste mohli komunikovat se službou Azure Kognitivní hledání, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Zkopírujte klíč správce pro plná práva ke službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-javascript/service-name-and-keys.png)

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který může běžet v .NET Core.

### <a name="install-azuresearchdocuments"></a>Nainstalovat Azure.Search.Documents

[Sada Azure kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search) se skládá z klientské knihovny, která umožňuje spravovat vaše indexy, zdroje dat, indexery a dovednosti a také nahrávat a spravovat dokumenty a provádět dotazy, a to vše bez nutnosti zabývat se podrobnostmi o http a JSON. Tato Klientská knihovna je distribuována jako balíček NuGet.

Pro tento projekt nainstalujte verzi 11 nebo novější z `Azure.Search.Documents` a nejnovější verze nástroje `Microsoft.Extensions.Configuration` .

1. V aplikaci Visual Studio vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení...**

1. Vyhledejte [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Vyberte nejnovější verzi a pak klikněte na **nainstalovat** .

1. Zopakováním předchozích kroků nainstalujte [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) a [Microsoft.Extensions.Configuration.Jsna](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Přidat informace o připojení služby

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Přidat**  >  **novou položku...** 

1. Zadejte název souboru `appsettings.json` a vyberte **Přidat** . 

1. Zahrnout tento soubor do výstupního adresáře.
    1. Klikněte pravým tlačítkem na `appsettings.json` a vyberte **vlastnosti** . 
    1. Změňte hodnotu **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější** .

1. Zkopírujte následující kód JSON do nového souboru JSON.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Přidejte službu Search a informace o účtu úložiště objektů BLOB. Odvolání, že tyto informace můžete získat z kroků zřizování služby, které jsou uvedené v předchozí části.

Pro **SearchServiceUri** zadejte úplnou adresu URL.

### <a name="add-namespaces"></a>Přidat obory názvů

V `Program.cs` přidejte následující obory názvů.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Vytvoření klienta

Vytvoří instanci `SearchIndexClient` a `SearchIndexerClient` v rámci `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Klienti se připojují k vaší vyhledávací službě. Aby nedocházelo k otevření příliš velkého počtu připojení, měli byste zkusit sdílet jednu instanci v aplikaci, pokud je to možné. Metody jsou bezpečné pro přístup z více vláken umožňující takové sdílení.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Přidání funkce pro ukončení programu během selhání

Tento kurz vám pomůže pochopit jednotlivé kroky kanálu indexování. Pokud dojde k závažnému problému, který programu brání v vytváření zdroje dat, dovednosti, indexu nebo indexeru, program zobrazí zprávu o chybě a ukončí tak, aby bylo možné tento problém pochopit a vyřešit.

Přidejte `ExitProgram` do `Main` pro zpracování scénářů, které vyžadují ukončení programu.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3. vytvoření kanálu

Ve službě Azure Kognitivní hledání se při indexování (nebo ingestování dat) objevuje zpracování AI. Tato část návodu vytvoří čtyři objekty: zdroj dat, index definice, dovednosti, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

`SearchIndexerClient` má [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) vlastnost, kterou lze nastavit na `SearchIndexerDataSourceConnection` objekt. Tento objekt poskytuje všechny metody, které potřebujete k vytvoření, výpisu, aktualizaci nebo odstranění zdrojů dat Azure Kognitivní hledání.

Vytvořte novou `SearchIndexerDataSourceConnection` instanci voláním `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` . Následující kód vytvoří zdroj dat typu `AzureBlob` .

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

V případě úspěšné žádosti vrátí metoda zdroj dat, který byl vytvořen. Pokud dojde k potížím s požadavkem, například s neplatným parametrem, metoda vyvolá výjimku.

Nyní přidejte řádek do `Main` pro volání `CreateOrUpdateDataSource` funkce, kterou jste právě přidali.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Sestavte a spusťte řešení. Vzhledem k tomu, že se jedná o váš první požadavek, zkontrolujte Azure Portal a potvrďte, že zdroj dat byl vytvořen v Azure Kognitivní hledání. Na stránce Přehled vyhledávací služby ověřte, že seznam zdrojů dat obsahuje novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice zdroje dat na portálu")

### <a name="step-2-create-a-skillset"></a>Krok 2: vytvoření dovednosti

V této části definujete sadu kroků obohacení, které chcete použít pro vaše data. Každý krok obohacení se nazývá *dovednost* a sada kroků rozšíření *dovednosti* . V tomto kurzu se používá integrovaný program pro [rozpoznávání hlasu](cognitive-search-predefined-skills.md) pro dovednosti:

* [Optické rozpoznávání znaků](cognitive-search-skill-ocr.md) pro rozpoznávání vytištěného a rukopisného textu v souborech obrázků.

* [Sloučení textu](cognitive-search-skill-textmerger.md) pro konsolidaci textu z kolekce polí do jednoho pole.

* [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

* [Rozdělení textu](cognitive-search-skill-textsplit.md) pro rozdělení velkého obsahu do menších bloků před voláním dovednosti pro extrakci klíčových frází a dovedností pro rozpoznávání entit. Extrakce klíčových frází a rozpoznávání entit přijímají vstupy o 50 000 nebo méně znaků. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

* [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

* [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze

Při počátečním zpracování Kognitivní hledání Azure vypraskliní každý dokument a extrahuje obsah z různých formátů souborů. Text, který pochází ze zdrojového souboru, je umístěn do generovaného `content` pole, jeden pro každý dokument. V takovém případě nastavte vstup `"/document/content"` pro použití tohoto textu. Obsah obrázku je umístěn do generovaného `normalized_images` pole zadaného v dovednosti jako `/document/normalized_images/*` .

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Dovednost OCR

Dovednost **optického rozpoznávání znaků** extrahuje text z obrázků. Tato dovednost předpokládá, že pole normalized_images existuje. K vygenerování tohoto pole později v tomto kurzu nastavíme ```"imageAction"``` konfiguraci v definici indexeru na ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Sloučit dovednost

V této části vytvoříte dovednost **sloučení** , která sloučí pole obsahu dokumentu s textem vytvořeným dovedností optického rozpoznávání znaků.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Dovednost pro detekci jazyka

**Rozpoznávání jazyka** dovednost detekuje jazyk vstupního textu a oznamuje každému dokumentu odeslanému na žádost jeden kód jazyka. Výstupy **rozpoznávání jazyka** dovednosti použijeme jako součást vstupu pro dovednost **rozdělení textu** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Dovednost rozdělení textu

Níže uvedená **rozdělená** dovednost rozdělí text na stránky a omezí délku stránky na 4 000 znaků měřených podle `String.Length` . Algoritmus se pokusí rozdělit text na bloky, které mají největší `maximumPageLength` velikost. V takovém případě algoritmus provede svůj nejlepší postup pro rozdělení věty na hranici věty, takže velikost bloku dat může být menší než `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Dovednost pro rozpoznávání entit

Tato `EntityRecognitionSkill` instance je nastavena na hodnotu rozpoznat typ kategorie `organization` . Dovednosti v **rozpoznávání entit** můžou také rozpoznat typy kategorií `person` a `location` .

Všimněte si, že pole "Context" je nastaveno na ```"/document/pages/*"``` hvězdičku, což znamená, že krok rozšíření je volán pro každou stránku v rámci ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Dovednost pro extrakci klíčových frází

Stejně jako `EntityRecognitionSkill` instance, kterou jste právě vytvořili, je pro každou stránku dokumentu volána **extrakce klíčových frází** dovednost.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Sestavení a vytvoření dovednosti

Sestavte se s `Skillset` využitím dovedností, které jste vytvořili.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Přidejte následující řádky do `Main` .

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
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Krok 3: vytvoření indexu

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí | Typy polí |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Vytvoření třídy DemoIndex

Pole pro tento index jsou definována pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. 

Přidáme třídu modelu do nového souboru jazyka C#. Klikněte pravým tlačítkem na projekt a vyberte **Přidat**  >  **novou položku...** , vyberte "třídu" a pojmenujte soubor a `DemoIndex.cs` pak vyberte **Přidat** .

Nezapomeňte určit, že chcete použít typy z `Azure.Search.Documents.Indexes` `System.Text.Json.Serialization` oboru názvů a.

Přidejte následující definici třídy modelu do `DemoIndex.cs` a zahrňte ji do stejného oboru názvů, kde vytvoříte index.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Teď, když jste definovali třídu modelu, `Program.cs` můžete vytvořit definici indexu poměrně snadno. Název tohoto indexu bude `demoindex` . Pokud již index s tímto názvem existuje, bude odstraněn.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Během testování se můžete setkat s tím, že se pokoušíte vytvořit index více než jednou. Z tohoto důvodu zkontrolujte, zda index, který se chystáte vytvořit, již existuje před pokusem o jeho vytvoření.

Přidejte následující řádky do `Main` .

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Přidejte následující příkaz using pro vyřešení odkazu jednoznačného použití.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Další informace o konceptech indexů najdete v tématu [Create index (REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: vytvoření a spuštění indexeru

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí.

* FieldMappings se zpracovávají před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

* OutputFieldMappings se zpracovávají po dovednosti a odkazují na sourceFieldNames, které neexistují, dokud je nevytvoří dokument pro vyhodnocování nebo rozšíření. TargetFieldName je pole v indexu.

Kromě toho, že se zapojovat vstupy do výstupů, můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Přidejte následující řádky do `Main` .

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Očekává se, že dokončení zpracování indexeru trvá nějakou dobu. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumat vytváření indexeru

Kód nastaví `"maxFailedItems"`  na-1, který dává modulu indexování pokyn, aby při importu dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Všimněte si také, že `"dataToExtract"` je nastavena na `"contentAndMetadata"` . Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. V `"imageAction"` `"generateNormalizedImages"` kombinaci s dovedností rozpoznávání textu a dovedností a sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4. monitorování indexování

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda indexer stále běží, použijte `GetStatus` metodu.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
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
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` představuje aktuální stav a historii provádění indexeru.

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy).

Přidejte následující řádky do `Main` .

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 – hledání

V rámci služby Azure Kognitivní hledání tutorial konzolové aplikace typicky před spuštěním dotazů, které vracejí výsledky, přidáme 2 sekundový interval, ale vzhledem k tomu, že se rozšíření trvá několik minut, zavřeme konzolovou aplikaci a použijeme místo toho jiný přístup.

Nejjednodušší možnost je [Průzkumník vyhledávání](search-explorer.md) na portálu. Nejdřív můžete spustit prázdný dotaz, který vrátí všechny dokumenty, nebo cílené hledání, které vrátí nový obsah pole vytvořený kanálem. 

1. V Azure Portal na stránce Přehled hledání vyberte **indexy** .

1. **`demoindex`** V seznamu vyhledejte. Měl by obsahovat 14 dokumentů. Pokud je počet dokumentů nulový, indexer je buď stále spuštěný, nebo se stránka ještě neaktualizovala. 

1. Vyberte **`demoindex`** . První karta je Průzkumník vyhledávání.

1. Obsah lze prohledávat ihned po načtení prvního dokumentu. Chcete-li ověřit existenci obsahu, spusťte nespecifikovaný dotaz kliknutím na tlačítko **Hledat** . Tento dotaz vrátí všechny aktuálně indexované dokumenty a poskytne vám představu o tom, co index obsahuje.

1. V dalším kroku vložte následující řetězec pro více spravovatelných výsledků: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Vzorový kód pro tento kurz kontroluje existující objekty a odstraní je, abyste mohli znovu spustit kód. Portál můžete také použít k odstranění indexů, indexerů, zdrojů dat a dovednosti.

## <a name="takeaways"></a>Shrnutí

V tomto kurzu jste provedli základní kroky pro vytvoření obohaceného kanálu indexování prostřednictvím vytváření součástí komponenty: zdroj dat, dovednosti, index a indexer.

Byly představeny [předdefinované dovednosti](cognitive-search-predefined-skills.md) společně s definicí dovednosti a mechanismy zřetězení dovedností prostřednictvím vstupů a výstupů. Zjistili jste taky, že `outputFieldMappings` v definici indexeru se vyžaduje směrování hodnot obohacených z kanálu do indexu s možností vyhledávání ve službě Azure kognitivní hledání.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení se všemi objekty v kanálu obohacení AI, se podíváme na dovednosti definice a jednotlivé dovednosti.

> [!div class="nextstepaction"]
> [Vytvoření dovednosti](cognitive-search-defining-skillset.md)