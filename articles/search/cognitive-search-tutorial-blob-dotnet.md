---
title: Kurz C# použití AI v Azure BLOBs
titleSuffix: Azure Cognitive Search
description: Projděte si příklad extrakce textu a zpracování přirozeného jazyka přes obsah ve službě BLOB Storage pomocí jazyka C# a sady Azure Kognitivní hledání .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 13e3f5a7d86d2e9b705fbeb104ba4f8eb690cb3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534093"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Kurz: prohledávatelný obsah generovaný AI z objektů blob Azure pomocí sady .NET SDK

Pokud máte v úložišti objektů BLOB v Azure nestrukturovaný text nebo obrázky, [kanál pro rozšíření AI](cognitive-search-concept-intro.md) může extrahovat informace a vytvořit nový obsah, který je vhodný pro scénáře fulltextového vyhledávání nebo dolování ve znalostní bázi. V tomto kurzu C# použijte na obrázcích použití optického rozpoznávání znaků (OCR) a proveďte zpracování v přirozeném jazyce, abyste vytvořili nová pole, která můžete využít v dotazech, omezujících vlastnostech a filtrech.

Tento kurz používá jazyk C# a [sadu .NET SDK](/dotnet/api/overview/azure/search) k provádění následujících úloh:

> [!div class="checklist"]
> * Začněte s aplikačními soubory a obrázky v úložišti objektů BLOB v Azure.
> * Definováním kanálu můžete přidat optické rozpoznávání znaků, extrakci textu, rozpoznávání jazyka, entitu a klíčová fráze.
> * Definujte index pro uložení výstupu (nezpracovaný obsah, plus páry název-hodnota vygenerované kanály).
> * Spusťte kanál, abyste mohli začít transformovat a analyzovat a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohatou syntaxí dotazu.

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

1. Otevřete tuto [složku OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klikněte pravým tlačítkem na soubor zip a vyberte **Extrahovat vše**. Existuje 14 souborů různých typů. Pro toto cvičení budete používat 7.

Můžete si také stáhnout zdrojový kód pro tento kurz. Zdrojový kód je ve složce tutorial-AI-obohacení v úložišti [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Cognitive Services v back-endu pro rozšíření AI a Azure Blob Storage k poskytnutí dat. Tento kurz zůstává v rámci bezplatného přidělení 20 transakcí na indexer za den v Cognitive Services, takže je potřeba, abyste vytvořili jenom služby pro vyhledávání a úložiště.

Pokud je to možné, vytvořte oba ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být váš účet Azure Storage v jakékoli oblasti.

### <a name="start-with-azure-storage"></a>Začínáme s Azure Storage

1. [Přihlaste se k Azure Portal](https://portal.azure.com/) a klikněte na **+ vytvořit prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště od Microsoftu.

   ![Vytvořit účet úložiště](media/cognitive-search-tutorial-blob/storage-account.png "Vytvořit účet úložiště")

1. Na kartě základy jsou vyžadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující jednu nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli souhrnně spravovat.

   + **Název účtu úložiště:** Pokud se domníváte, že máte více prostředků stejného typu, použijte název k jednoznačnému odstranění podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění:** Pokud je to možné, vyberte stejné umístění, které se používá pro Azure Kognitivní hledání a Cognitive Services. Jediné místo má za vyrušení poplatky za šířku pásma.

   + **Druh účtu**. Vyberte výchozí *StorageV2 (obecné účely v2)*.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** službu vytvořte.

1. Po vytvoření klikněte na **Přejít k prostředku** a otevřete stránku Přehled.

1. Klikněte na služba **BLOB** Service.

1. Kliknutím na **+ kontejner** vytvořte kontejner a pojmenujte ho *ozubeného kola-Search-demo*.

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

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

Abyste mohli komunikovat se službou Azure Kognitivní hledání, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který může běžet v .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Sada Azure kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search) se skládá z několika klientských knihoven, které vám umožní spravovat vaše indexy, zdroje dat, indexery a dovednosti a také nahrávat a spravovat dokumenty a provádět dotazy, a to vše bez nutnosti zabývat se podrobnostmi o protokolech HTTP a JSON. Tyto klientské knihovny jsou distribuovány jako balíčky NuGet.

Pro tento projekt nainstalujte verzi 9 nebo novější `Microsoft.Azure.Search` balíčku NuGet.

1. V prohlížeči, navštivte [stránku Microsoft. Azure. prohledat balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Vyberte nejnovější verzi (9 nebo novější).

1. Zkopírujte příkaz Správce balíčků.

1. Otevřete konzolu Správce balíčků. Vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**. 

1. Vložte a spusťte příkaz, který jste zkopírovali v předchozím kroku.

Dále nainstalujte nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

1. Vybrat **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení...** 

1. Klikněte na **Procházet** a vyhledejte `Microsoft.Extensions.Configuration.Json` balíček NuGet. 

1. Vyberte balíček, vyberte projekt, potvrďte, že verze je nejnovější stabilní verze, a pak klikněte na **nainstalovat**.

### <a name="add-service-connection-information"></a>Přidat informace o připojení služby

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Přidat**  >  **novou položku...** 

1. Zadejte název souboru `appsettings.json` a vyberte **Přidat**. 

1. Zahrnout tento soubor do výstupního adresáře.
    1. Klikněte pravým tlačítkem na `appsettings.json` a vyberte **vlastnosti**. 
    1. Změňte hodnotu **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.

1. Zkopírujte následující kód JSON do nového souboru JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Přidejte službu Search a informace o účtu úložiště objektů BLOB. Odvolání, že tyto informace můžete získat z kroků zřizování služby, které jsou uvedené v předchozí části.

Pro **SearchServiceName**zadejte krátký název služby a ne úplnou adresu URL.

### <a name="add-namespaces"></a>Přidat obory názvů

V `Program.cs` přidejte následující obory názvů.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Vytvoření klienta

Vytvořte instanci `SearchServiceClient` třídy v rámci `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` Vytvoří nový `SearchServiceClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appsettings.json).

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

`SearchServiceClient` obsahuje vlastnost `DataSources`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytvoření, výpisu, aktualizaci nebo odstranění zdrojů dat Azure Kognitivní hledání.

Vytvořte novou `DataSource` instanci voláním `serviceClient.DataSources.CreateOrUpdate(dataSource)` . `DataSource.AzureBlobStorage` vyžaduje, abyste zadali název zdroje dat, připojovací řetězec a název kontejneru objektů BLOB.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
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

V případě úspěšné žádosti vrátí metoda zdroj dat, který byl vytvořen. Pokud dojde k potížím s požadavkem, například s neplatným parametrem, metoda vyvolá výjimku.

Nyní přidejte řádek do `Main` pro volání `CreateOrUpdateDataSource` funkce, kterou jste právě přidali.

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

Sestavte a spusťte řešení. Vzhledem k tomu, že se jedná o váš první požadavek, zkontrolujte Azure Portal a potvrďte, že zdroj dat byl vytvořen v Azure Kognitivní hledání. Na stránce řídicího panelu vyhledávací služby ověřte, že dlaždice Zdroje dat má novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice zdroje dat na portálu")

### <a name="step-2-create-a-skillset"></a>Krok 2: vytvoření dovednosti

V této části definujete sadu kroků obohacení, které chcete použít pro vaše data. Každý krok obohacení se nazývá *dovednost* a sada kroků obohacení o *dovednosti*. V tomto kurzu se používá integrovaný program pro [rozpoznávání hlasu](cognitive-search-predefined-skills.md) pro dovednosti:

+ [Optické rozpoznávání znaků](cognitive-search-skill-ocr.md) pro rozpoznávání vytištěného a rukopisného textu v souborech obrázků.

+ [Sloučení textu](cognitive-search-skill-textmerger.md) pro konsolidaci textu z kolekce polí do jednoho pole.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md) pro rozdělení velkého obsahu do menších bloků před voláním dovednosti pro extrakci klíčových frází a dovedností pro rozpoznávání entit. Extrakce klíčových frází a rozpoznávání entit přijímají vstupy o 50 000 nebo méně znaků. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze

Při počátečním zpracování vyKognitivní hledání Azure napraskliny každého dokumentu a přečte obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. V takovém případě nastavte vstup ```"/document/content"``` pro použití tohoto textu. 

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Dovednost OCR

Dovednost **optického rozpoznávání znaků** extrahuje text z obrázků. Tato dovednost předpokládá, že pole normalized_images existuje. K vygenerování tohoto pole později v tomto kurzu nastavíme ```"imageAction"``` konfiguraci v definici indexeru na ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

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

V této části vytvoříte dovednost **sloučení** , která sloučí pole obsahu dokumentu s textem vytvořeným dovedností OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

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

### <a name="language-detection-skill"></a>Dovednost pro detekci jazyka

**Rozpoznávání jazyka** dovednost detekuje jazyk vstupního textu a oznamuje každému dokumentu odeslanému na žádost jeden kód jazyka. Výstupy **rozpoznávání jazyka** dovednosti použijeme jako součást vstupu pro dovednost **rozdělení textu** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Dovednost rozdělení textu

Níže uvedená **rozdělená** dovednost rozdělí text na stránky a omezí délku stránky na 4 000 znaků měřených podle `String.Length` . Algoritmus se pokusí rozdělit text na bloky, které mají největší `maximumPageLength` velikost. V takovém případě algoritmus provede svůj nejlepší postup pro rozdělení věty na hranici věty, takže velikost bloku dat může být menší než `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

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

### <a name="entity-recognition-skill"></a>Dovednost pro rozpoznávání entit

Tato `EntityRecognitionSkill` instance je nastavena na hodnotu rozpoznat typ kategorie `organization` . Dovednosti v **rozpoznávání entit** můžou také rozpoznat typy kategorií `person` a `location` .

Všimněte si, že pole "Context" je nastaveno na ```"/document/pages/*"``` hvězdičku, což znamená, že krok rozšíření je volán pro každou stránku v rámci ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

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

### <a name="key-phrase-extraction-skill"></a>Dovednost pro extrakci klíčových frází

Stejně jako `EntityRecognitionSkill` instance, kterou jste právě vytvořili, je pro každou stránku dokumentu volána **extrakce klíčových frází** dovednost.

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

### <a name="build-and-create-the-skillset"></a>Sestavení a vytvoření dovednosti

Sestavte se s `Skillset` využitím dovedností, které jste vytvořili.

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
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
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

Přidáme třídu modelu do nového souboru jazyka C#. Klikněte pravým tlačítkem na projekt a vyberte **Přidat**  >  **novou položku...**, vyberte "třídu" a pojmenujte soubor a `DemoIndex.cs` pak vyberte **Přidat**.

Nezapomeňte určit, že chcete použít typy z `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` oboru názvů a.

Přidejte následující definici třídy modelu do `DemoIndex.cs` a zahrňte ji do stejného oboru názvů, kde vytvoříte index.

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

Teď, když jste definovali třídu modelu, `Program.cs` můžete vytvořit definici indexu poměrně snadno. Název tohoto indexu bude `demoindex` . Pokud již index s tímto názvem existuje, bude odstraněn.

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

Během testování se můžete setkat s tím, že se pokoušíte vytvořit index více než jednou. Z tohoto důvodu zkontrolujte, zda index, který se chystáte vytvořit, již existuje před pokusem o jeho vytvoření.

Přidejte následující řádky do `Main` .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Přidejte následující příkaz using pro vyřešení odkazu jednoznačného použití.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Další informace o definování indexu najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: vytvoření a spuštění indexeru

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí.

+ FieldMappings se zpracovávají před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

+ OutputFieldMappings se zpracovávají po dovednosti a odkazují na sourceFieldNames, které neexistují, dokud je nevytvoří dokument pro vyhodnocování nebo rozšíření. TargetFieldName je pole v indexu.

Kromě toho, že se zapojovat vstupy do výstupů, můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

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

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

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
Přidejte následující řádky do `Main` .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Očekává se, že dokončení vytváření indexeru bude trvat trochu dlouho. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumat vytváření indexeru

Kód nastaví ```"maxFailedItems"```  na-1, který dává modulu indexování pokyn, aby při importu dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Všimněte si také, že ```"dataToExtract"``` je nastavena na ```"contentAndMetadata"``` . Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. V ```"imageAction"``` ```"generateNormalizedImages"``` kombinaci s dovedností rozpoznávání textu a dovedností a sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4. monitorování indexování

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda indexer stále běží, použijte `GetStatus` metodu.

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

`IndexerExecutionInfo` představuje aktuální stav a historii provádění indexeru.

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy).

Přidejte následující řádky do `Main` .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 – hledání

Po dokončení indexování můžete spustit dotazy, které vracejí obsah jednotlivých polí. Ve výchozím nastavení vrátí Azure Kognitivní hledání výsledky nejvyšší 50. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v tématu [o výsledcích stránky v Azure kognitivní hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

Přidejte následující řádky do `Main` .

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

`CreateSearchIndexClient` Vytvoří nový `SearchIndexClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appsettings.json). Všimněte si, že se používá klíč rozhraní API vyhledávací služby, a ne klíč správce.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Přidejte následující kód do `Main` . První příkaz try-catch vrátí definici indexu s názvem, typem a atributy každého pole. Druhým je parametrizovaný dotaz, kde `Select` Určuje, která pole se mají zahrnout do výsledků, například `organizations` . Vyhledávací řetězec `"*"` vrátí všechny obsahy jednoho pole.

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

Opakujte pro další pole: obsah, languageCode, klíčová fráze a organizace v tomto cvičení. Můžete vrátit více polí prostřednictvím vlastnosti [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select) pomocí seznamu odděleného čárkami.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Vzorový kód pro tento kurz kontroluje existující objekty a odstraní je, abyste mohli znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů, zdrojů dat a dovednosti.

## <a name="takeaways"></a>Shrnutí

V tomto kurzu jste provedli základní kroky pro vytvoření obohaceného kanálu indexování prostřednictvím vytváření součástí komponenty: zdroj dat, dovednosti, index a indexer.

Byly představeny [předdefinované dovednosti](cognitive-search-predefined-skills.md) společně s definicí dovednosti a mechanismy zřetězení dovedností prostřednictvím vstupů a výstupů. Zjistili jste taky, že `outputFieldMappings` v definici indexeru se vyžaduje směrování hodnot obohacených z kanálu do indexu s možností vyhledávání ve službě Azure kognitivní hledání.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení se všemi objekty v kanálu obohacení AI, se podíváme na dovednosti definice a jednotlivé dovednosti.

> [!div class="nextstepaction"]
> [Vytvoření dovednosti](cognitive-search-defining-skillset.md)