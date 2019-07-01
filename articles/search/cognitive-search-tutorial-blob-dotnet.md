---
title: C#Kurz pro volání rozhraní API služeb Cognitive Services v kanál indexování – Azure Search
description: Tento kurz vás krok za krokem provede ukázkovou extrakcí dat a zpracováním přirozeného jazyka a obrázků pomocí umělé inteligence v indexování Azure Search s cílem extrahovat a transformovat data.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 7fc33f89797fe0e3cc3a9832c9dc084194cf6603
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485765"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Kurz: Volání rozhraní API služeb Cognitive Services v Azure Search indexování kanálu

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Dovednosti využívají možnosti analýzy image ve službě Cognitive Services a zpracování přirozeného jazyka (NLP). Prostřednictvím složení dovednosti a konfigurace můžete extrahovat text a textové reprezentace obrázek nebo soubor naskenovaného dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Konečným výsledkem bude bohatý další obsah v indexu Azure Search, vytvořený kanál indexování založené na AI.

V tomto kurzu pomocí sady .NET SDK k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření kanálu indexace, který rozšiřuje ukázková data na cestě k indexu
> * Použít předdefinované dovednosti: optické rozpoznávání znaků, sloučení textu, rozpoznávání jazyka, rozdělení textu, rozpoznávání entit, extrakce klíčových frází
> * Zjištění, jak mapováním vstupů na výstupy v sadě dovedností řetězit dovednosti za sebe
> * Spouštění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstup indexu v Azure Search, který se dá prohledávat fulltextově Index můžete rozšířit pomocí dalších standardních funkcí, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md) a [filtry](search-filters.md).

V tomto kurzu běží na bezplatné služby, ale počet transakcí zdarma je omezený na 20 dokumenty za den. Pokud chcete spustit v tomto kurzu více než jednou v jednom dni, použijte menší soubor nastavit tak, aby se vejde do více spuštění.

> [!NOTE]
> Jak rozbalit obor zvýšením počtu zpracování, přidáním více dokumentů nebo přidání další algoritmy AI, musíte připojit fakturovatelné prostředku služeb Cognitive Services. Poplatky se účtují při volání rozhraní API ve službě Cognitive Services a extrakci image jako součást fáze hádání dokumentu ve službě Azure Search. Neúčtují žádné poplatky pro extrakci textu z dokumentů.
>
> Provádění předdefinované dovednosti, se účtuje za stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto kurzu. 

+ [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázková data. Ujistěte se, že je účet úložiště ve stejné oblasti jako Azure Search.

+ [Ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestává ze sady malý soubor různých typů. 

+ [Instalace sady Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

K interakci se službou Azure Search budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

   ![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Azure Table Storage se nepodporuje pro kognitivního vyhledávání. Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. [Přihlaste se k webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, klikněte na tlačítko **objekty BLOB**a potom klikněte na tlačítko **+ kontejner**.

1. [Vytvořte kontejner objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) obsahuje ukázková data. Můžete nastavit úroveň veřejného přístupu k některému z jeho platných hodnot. Tento kurz předpokládá, že název kontejneru je "basic-demo-data-pr".

1. Po vytvoření kontejneru ho otevřete a vyberte **nahrát** na panelu příkazů pro nahrání [ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. Můžete to udělat navigace do vašeho účtu úložiště na webu Azure Portal, vyberte **přístupové klíče**a zkopírujte **připojovací řetězec** pole.

   Připojovací řetězec by měl být adresa URL, která vypadá nějak takto:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte tím, že otevření sady Visual Studio a vytvoření nového projektu konzolové aplikace, které můžete spustit na .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Azure Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které vám umožní spravovat zdroje dat, indexy, indexery a dovednosti, stejně jako nahrávat a spravovat dokumenty a spouštění dotazů, aniž byste museli potýkat se Podrobnosti o protokolu HTTP a JSON. Tyto klientské knihovny se distribuují jako balíčky NuGet.

Pro tento projekt, budete muset nainstalovat verzi 9 `Microsoft.Azure.Search` NuGet package a nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

Nainstalujte `Microsoft.Azure.Search` balíčku NuGet pomocí konzole Správce balíčků v sadě Visual Studio. Otevřete konzoly vyberte Správce balíčků **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**. Získat příkaz ke spuštění, přejděte [stránky balíčku Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), vyberte verze 9 a zkopírujte tento příkaz Správce balíčků. V konzole Správce balíčků, spusťte tento příkaz.

Chcete-li nainstalovat `Microsoft.Extensions.Configuration.Json` balíček NuGet v sadě Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **spravovat balíčky NuGet pro řešení...** . Kliknout na Procházet a vyhledejte `Microsoft.Extensions.Configuration.Json` balíček NuGet. Až ji najdete, vyberte balíček, váš projekt, potvrďte, že je verze na nejnovější stabilní verzi a potom kliknout na tlačítko nainstalovat.

## <a name="add-azure-search-service-information"></a>Přidejte informace o službě Azure Search

Abyste se mohli připojit ke službě Azure Search je potřeba přidat informace o službě search do projektu. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **přidat** > **novou položku...**  . Název souboru `appsettings.json` a vyberte **přidat**. 

Tento soubor bude potřeba zahrnout do výstupního adresáře. Chcete-li to mohli udělat, klikněte pravým tlačítkem myši klikněte na `appsettings.json` a vyberte **vlastnosti**. Změňte hodnotu vlastnosti **kopírovat do výstupního adresáře** k **kopii novější**.

Kopie následující kód JSON do nového souboru JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Přidáte vyhledávací služby a objektů blob úložiště informace o vašem účtu.

Na stránce účtu vyhledávání na webu Azure Portal můžete získat informace o vaší vyhledávací služby. Na hlavní stránce bude mít název účtu a klíče můžete najít tak, že vyberete **klíče**.

Připojovací řetězec objektu blob můžete získat tak, že přejdete do účtu úložiště na webu Azure Portal, vyberte **přístupové klíče**a následným kopírováním **připojovací řetězec** pole.

## <a name="add-namespaces"></a>Přidat obory názvů

Tento kurz používá mnoho různých typů z různých oborů názvů. Chcete-li použít tyto typy přidejte následující text do `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Vytvoření klienta

Vytvoření instance `SearchServiceClient` třídy.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` Vytvoří novou `SearchServiceClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appsettings.json).

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
> 

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Vytvořte nový `DataSource` instance voláním `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` vyžaduje se, že zadáte název zdroje dat, připojovací řetězec a název kontejneru objektů blob.

I když není použit v tomto kurzu je zásadu obnovitelného odstranění také definován, který se používá k identifikaci odstraněné objekty BLOB na základě hodnoty sloupce s obnovitelným odstraněním. Tyto zásady bude považovat za objekt blob, která se má odstranit, pokud má vlastnost metadat `IsDeleted` s hodnotou `true`.

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

Teď, když jste inicializovali `DataSource` objektu, vytvořte zdroj dat. `SearchServiceClient` obsahuje vlastnost `DataSources`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytváření, výpisu, aktualizaci nebo odstranění zdroje dat Azure Search.

Pro požadavek úspěšná vrátí metoda zdroj dat, který byl vytvořen. Pokud dojde k nějakému problému s požadavkem, jako je například neplatný parametr, metoda vyvolá výjimku.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Vzhledem k tomu, že je to váš první požadavek, podívejte se na portál Azure Portal a potvrďte, že se zdroj dat vytvořil ve službě Azure Search. Na stránce řídicího panelu vyhledávací služby ověřte, že dlaždice Zdroje dat má novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice Zdroje dat na portálu")

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V této části definují sadu rozšíření kroky, které má být použita k vašim datům. Každý krok rozšíření je volána *dovednosti* a rozšiřování kroky *dovednosti*. V tomto kurzu se pro sadu dovedností používají [předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md):

+ [Optické rozpoznávání znaků na](cognitive-search-skill-ocr.md) rozpoznat tištěné a rukou psaný text v souborech bitové kopie.

+ [Sloučení textu](cognitive-search-skill-textmerger.md) konsolidovat text z kolekce polí do jednoho pole.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md) k rozdělení velké obsah do menších bloků před voláním dovednosti extrakce klíčových frází a rozpoznání odborností entity. Extrakce klíčových frází a rozpoznání entity přijímat vstupy 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizace z obsahu v kontejneru objektů blob.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze

Azure Search během počátečního zpracování praskliny každý dokument k načtení obsahu z jiné formáty souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. V důsledku toho nastavit jako vstup pro ```"/document/content"``` používat tento text. 

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR dovedností

**OCR** dovednosti extrahuje text z obrázků. Tato dovednosti předpokládá, že existuje normalized_images pole. K vygenerování tohoto pole v pozdější části kurzu nastavíme ```"imageAction"``` konfigurace v definici indexer ```"generateNormalizedImages"```.

```csharp
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
```

### <a name="merge-skill"></a>Sloučit dovedností

V této části vytvoříte **sloučit** dovednosti, která sloučí pole obsahu dokumentu s textem, který byl vytvořen OCR dovedností.

```csharp
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
```

### <a name="language-detection-skill"></a>Dovednosti detekce jazyka

**Rozpoznávání jazyka** dovednosti jazyk vstupního textu zjišťuje a vykazuje jeden jazyk kódu pro každý dokument podání žádosti. Použijeme výstup **rozpoznávání jazyka** dovedností jako součást vstup **rozdělení textu** dovedností.

```csharp
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
```

### <a name="text-split-skill"></a>Text rozdělit dovedností

Níže **rozdělení** dovedností se rozdělit text stránky a omezit délku stránky na 4 000 znaků pohledu `String.Length`. Algoritmus se pokusí text rozdělit do bloků dat, které jsou maximálně `maximumPageLength` velikosti. V takovém případě bude algoritmus proveďte co nejlépe konce věty na hranici věty tak velikost bloku dat může být o něco menší než `maximumPageLength`.

```csharp
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
```

### <a name="entity-recognition-skill"></a>Dovednosti rozpoznávání entit

To `EntityRecognitionSkill` instance je nastavena na rozpoznat typ kategorie `organization`. **Rozpoznávání entit** dovednosti můžete také rozpoznává kategorie typů `person` a `location`.

Všimněte si, že pole "kontext" nastavena na ```"/document/pages/*"``` hvězdičkou, to znamená kroku rozšíření je volána pro každou stránku v rámci ```"/document/pages"```.

```csharp
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
```

### <a name="key-phrase-extraction-skill"></a>Dovednosti extrakce klíčových frází

Stejně jako `EntityRecognitionSkill` instanci, kterou jste právě vytvořili, **extrakce frází klíč** dovedností se volá pro každou stránku z dokumentu.

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>Sestavování a vytváření zkušenostech

Sestavení `Skillset` pomocí dovedností, které jste vytvořili.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Vytvoření zkušenostech ve vyhledávací službě.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Vytvoření indexu

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Vytvořte třídu DemoIndex

Pole pro tento index jsou definovány pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. 

Třída modelu přidáme do nového C# souboru. Klikněte pravým tlačítkem myši na projekt a vyberte **přidat** > **novou položku...** , vyberte "Třída" a název souboru `DemoIndex.cs`a pak vyberte **přidat**.

Ujistěte se, že k označení, že chcete používat typy z `Microsoft.Azure.Search` a `Microsoft.Azure.Search.Models` obory názvů.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Přidat následující definici třídy modelu `DemoIndex.cs` a její zahrnutí stejný obor názvů, ve kterém vytvoříte index.

```csharp
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
```

Teď, když jsme nadefinovali třídu modelu, zase `Program.cs` můžete poměrně snadno vytvořit definici indexu. "Demoindex" bude mít název pro tento index.

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Při testování můžete zjistit, že se pokoušíte vytvořit index více než jednou. Z tohoto důvodu zkontrolujte, zda existuje index, který se chystáte vytvořit, již před pokusem o jeho vytvoření.

```csharp
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

Další informace o definování indexu najdete v článku o [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a spouštění transformací

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí.

+ FieldMappings se zpracovávají před zkušenostech mapování zdroje polí ze zdroje dat do cílového pole v indexu. Pokud názvy polí a typů jsou stejná na obou koncích, není třeba žádné mapování.

+ Zpracování outputFieldMappings po dovednosti, odkazující na sourceFieldNames, které neexistují až do dokumentu hádání nebo rozšiřování vytvoří je. TargetFieldName je pole v indexu.

Kromě zapojování vstupy do výstupů, můžete také použít mapování polí k vyrovnání datové struktury. Další informace najdete v tématu [způsob mapování polí bohatších možností prohledávatelný index](cognitive-search-output-field-mapping.md).

```csharp
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
    // Handle exception
}
```

Můžete očekávejte, že vytvoření indexeru zabere trochu času k dokončení. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumejte vytvoření indexeru

Nastaví kód ```"maxFailedItems"``` na hodnotu -1, který dává pokyn modulu indexování ignorovat chyby při importu dat. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Všimněte si také ```"dataToExtract"``` je nastavena na ```"contentAndMetadata"```. Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. ```"imageAction"``` Nastavena na ```"generateNormalizedImages"``` konfigurace, v kombinaci s OCR znalostí a dovedností sloučení textu, říká indexeru k extrakci textu z bitové kopie (například word "stop" od provozu Stop znak) a vloží ho jako součást pole obsahu. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, jestli stále běží indexeru, použijte `GetStatus` metody.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` představuje aktuální stav a spouštění historie indexeru.

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy).
 
## <a name="query-your-index"></a>Dotázání indexu

Po dokončení indexování můžete spouštět dotazy, které vrací obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` Vytvoří novou `SearchIndexClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appsettings.json). Všimněte si, že se používá klíč dotazu rozhraní API služby search a ne klíč správce.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```csharp
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

Opakujte pro další pole: obsah, prostředí, keyPhrases a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V počátečních fázích experimentální vývoje většina praktický pro počítají s iteracemi návrhu je odstranit objekty z Azure Search a umožňují vašemu kódu znovu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

V tomto kurzu trvalo Hotovo vyhledávají se stávající indexery a indexy a jejich odstranění, pokud už existoval tak, aby váš kód můžete znovu spustit.

Na portálu můžete použít také k odstranění indexy, indexery a dovednosti.

Až se váš kód bude blížit dokončení, možná budete chtít zdokonalit strategii opětovného sestavování. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

## <a name="takeaways"></a>Shrnutí

V tomto kurzu jsme vám ukázali základní kroky pro vytváření bohatších možností kanál indexování prostřednictvím vytváření součásti: zdroj dat, dovednosti, indexu a indexeru.

Představili jsme si [předdefinované dovednosti](cognitive-search-predefined-skills.md), definice sad dovedností a mechanismy, jak pomocí vstupů a výstupů řetězit dovednosti za sebe. Naučili jste se, že `outputFieldMappings` v definici indexu umožňuje směrovat rozšířené hodnoty z kanálu do prohledávatelného indexu ve službě Azure Search.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak po kurzu vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob. Za předpokladu, že jste vložili obě služby do stejné skupiny, odstraňte skupinu prostředků. Tím se trvale odstraní všechno, co v ní je, včetně služeb a veškerého uloženého obsahu, který jste v tomto kurzu vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete.

> [!div class="nextstepaction"]
> [Příklad: vytvoření vlastní dovednosti](cognitive-search-create-custom-skill-example.md)
