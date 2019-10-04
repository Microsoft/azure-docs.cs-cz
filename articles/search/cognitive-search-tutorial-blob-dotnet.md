---
title: C#Kurz pro volání rozhraní API služeb Cognitive Services v kanálu indexování – Azure Search
description: V tomto kurzu si Projděte příklad extrakce dat, přirozeného jazyka a zpracování AI v Azure Search indexování pro extrakci a transformaci dat.
manager: nitinme
author: MarkHeff
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: b40cd63062e961848eb1ab6b956e63a83a634817
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936942"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Kurz: volání rozhraní API služeb Cognitive Services v kanálu indexování Azure Search

V tomto kurzu se seznámíte s příznámkou rozšíření programovacích dat v Azure Search s využitím *dovedností rozpoznávání*. Dovednosti jsou zajištěny při zpracování přirozeného jazyka (NLP) a možností analýzy obrázků v Cognitive Services. Prostřednictvím složení a konfigurace dovednosti můžete extrahovat text a text reprezentace obrázku nebo naskenovaného souboru dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Konečný výsledek je bohatě doplňující obsah v indexu Azure Search vytvořeným kanálem indexování s použitím AI.

V tomto kurzu použijete sadu .NET SDK k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření kanálu indexování, který rozšiřuje ukázková data v cestě do indexu
> * Použití integrovaných dovedností: optické rozpoznávání znaků, fúze textu, detekce jazyka, rozdělení textu, rozpoznávání entit, extrakce klíčových frází
> * Naučte se, jak pomocí mapování vstupů na výstupy v dovednosti zřetězit dovednosti společně.
> * Provádění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstupem je fulltextově prohledávatelný index na Azure Search. Index můžete vylepšit dalšími standardními možnostmi, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md)a [filtry](search-filters.md).

Tento kurz běží na bezplatné službě, ale počet bezplatných transakcí je omezený na 20 dokumentů za den. Pokud chcete spustit tento kurz ve stejný den více než jednou, použijte menší sadu souborů, abyste se mohli přizpůsobit více spuštění.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset připojit Fakturovatelné Cognitive Services prostředku. Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se používají následující služby, nástroje a data. 

+ [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat. Ujistěte se, že je účet úložiště ve stejné oblasti jako Azure Search.

+ [Ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) se skládají z malých souborových sad různých typů. 

+ [Nainstalujte Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Abyste mohli komunikovat se službou Azure Search, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste přidali Azure Search k vašemu předplatnému, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Vzorový koncový bod může vypadat jako `https://mydemo.search.windows.net`.

1. V **nastavení**@no__t**klíčů**– 1 Získejte klíč správce s úplnými právy ke službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu http a přístupového klíče]k(media/search-get-started-postman/get-url-key.png "získání koncového bodu http a přístupového klíče")

Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál rozšíření vyžádá ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [Azure Search indexer](search-indexer-overview.md). Pro toto cvičení používáme úložiště objektů BLOB k předvedení více typů obsahu.

1. [Přihlaste se k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot. V tomto kurzu se předpokládá, že název kontejneru je "Basic-demo-data-PR".

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** a nahrajte [vzorová data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Zdrojové soubory v úložišti objektů BLOB v Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Po načtení ukázkových souborů Získejte název kontejneru a připojovací řetězec pro úložiště objektů BLOB. To můžete udělat tak, že přejdete na svůj účet úložiště v Azure Portal vyberete **přístupové klíče**a potom zkopírujete pole **připojovací řetězec** .

   Připojovací řetězec by měl být adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutí sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v tématu [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který může běžet v .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Sada Azure Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které umožňují spravovat vaše indexy, zdroje dat, indexery a dovednosti a také nahrávat a spravovat dokumenty a provádět dotazy, a to vše bez nutnosti zabývat se podrobnostmi o http a JSON. . Tyto klientské knihovny jsou distribuovány jako balíčky NuGet.

Pro tento projekt bude nutné nainstalovat verzi 9 balíčku NuGet `Microsoft.Azure.Search` a nejnovější balíček NuGet `Microsoft.Extensions.Configuration.Json`.

Nainstalujte balíček NuGet `Microsoft.Azure.Search` pomocí konzoly Správce balíčků v aplikaci Visual Studio. Konzolu Správce balíčků otevřete výběrem **nástrojů** > **správce balíčků NuGet** > **Konzola správce balíčků**. Pokud chcete spustit příkaz, přejděte na [stránku balíčku NuGet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search), vyberte verze 9 a zkopírujte příkaz Správce balíčků. V konzole správce balíčků spusťte tento příkaz.

Chcete-li nainstalovat balíček NuGet `Microsoft.Extensions.Configuration.Json` v aplikaci Visual Studio, vyberte **nástroje** **správce balíčků NuGet** >  –  > **Spravovat balíčky NuGet pro řešení...** . Vyberte Procházet a vyhledejte balíček NuGet `Microsoft.Extensions.Configuration.Json`. Po nalezení vyberte balíček, vyberte projekt, potvrďte, že verze je nejnovější stabilní verze, a pak vyberte nainstalovat.

## <a name="add-azure-search-service-information"></a>Přidat informace o Azure Search službě

Aby bylo možné se připojit ke službě Azure Search, budete muset do svého projektu přidat informace o vyhledávací službě. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **přidat** > **novou položku...** . Pojmenujte soubor `appsettings.json` a vyberte **Přidat**. 

Tento soubor bude nutné zahrnout do výstupního adresáře. Uděláte to tak, že kliknete pravým tlačítkem na `appsettings.json` a vyberete **vlastnosti**. Změňte hodnotu **Kopírovat do výstupního adresáře** na **Kopírovat novější**.

Zkopírujte následující kód JSON do nového souboru JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Přidejte službu Search a informace o účtu úložiště objektů BLOB.

Informace o vyhledávací službě můžete získat na stránce vyhledávacího účtu v Azure Portal. Název účtu bude na hlavní stránce a klíče najdete tak, že vyberete **klíče**.

Připojovací řetězec objektu blob můžete získat tak, že přejdete na účet úložiště v Azure Portal, vyberete **přístupové klíče**a pak zkopírujete pole **připojovací řetězec** .

## <a name="add-namespaces"></a>Přidat obory názvů

Tento kurz používá mnoho různých typů z různých oborů názvů. Aby bylo možné tyto typy použít, přidejte následující pro `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Vytvoření klienta

Vytvořit instanci `SearchServiceClient` třídy.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` vytvoří nové `SearchServiceClient` s použitím hodnot, které jsou uložené v konfiguračním souboru aplikace (appSettings. JSON).

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
> Třída `SearchServiceClient` spravuje připojení k vaší vyhledávací službě. Aby nedocházelo k otevření příliš velkého počtu připojení, měli byste zkusit sdílet jednu instanci `SearchServiceClient` v aplikaci, pokud je to možné. Jeho metody jsou bezpečné pro přístup z více vláken, které umožňují takové sdílení.
> 
> 

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Vytvořte novou instanci `DataSource` voláním `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` vyžaduje, abyste zadali název zdroje dat, připojovací řetězec a název kontejneru objektů BLOB.

I když se v tomto kurzu nepoužívá, je definovaná taky zásada podmíněného odstranění, která se používá k identifikaci odstraněných objektů blob na základě hodnoty sloupce obnovitelného odstranění. Následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost metadata `IsDeleted` s hodnotou `true`.

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

Nyní, když jste inicializoval objekt `DataSource`, vytvořte zdroj dat. `SearchServiceClient` má vlastnost `DataSources`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytvoření, výpisu, aktualizaci nebo odstranění zdrojů dat Azure Search.

V případě úspěšné žádosti vrátí metoda zdroj dat, který byl vytvořen. Pokud dojde k potížím s požadavkem, například s neplatným parametrem, metoda vyvolá výjimku.

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

Vzhledem k tomu, že se jedná o váš první požadavek, zkontrolujte Azure Portal a potvrďte, že byl zdroj dat vytvořen v Azure Search. Na stránce řídicí panel služby vyhledávání ověřte, zda dlaždice zdroje dat obsahuje novou položku. Je možné, že budete muset několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice zdroje dat]na(./media/cognitive-search-tutorial-blob/data-source-tile.png "dlaždici zdroje dat") na portálu na portálu

## <a name="create-a-skillset"></a>Vytvoření dovednosti

V této části definujete sadu kroků obohacení, které chcete použít pro vaše data. Každý krok obohacení se nazývá *dovednost* a sada kroků obohacení o *dovednosti*. V tomto kurzu se používá [předdefinovaná znalost vnímání](cognitive-search-predefined-skills.md) pro dovednosti:

+ [Optické rozpoznávání znaků](cognitive-search-skill-ocr.md) pro rozpoznávání vytištěného a rukopisného textu v souborech obrázků.

+ [Sloučení textu](cognitive-search-skill-textmerger.md) pro konsolidaci textu z kolekce polí do jednoho pole.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md) k identifikaci jazyka obsahu.

+ [Rozdělení textu](cognitive-search-skill-textsplit.md) pro rozdělení velkého obsahu do menších bloků před voláním dovednosti pro extrakci klíčových frází a dovedností pro rozpoznávání entit. Extrakce klíčových frází a rozpoznávání entit přijímají vstupy o 50 000 nebo méně znaků. Některé z ukázkových souborů je třeba rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md) vyžádat horní klíčové fráze.

Při počátečním zpracování Azure Search napraskliny každý dokument, aby bylo možné číst obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, je umístěn do generovaného pole ```content```, jednu pro každý dokument. V takovém případě nastavte zadání jako ```"/document/content"``` pro použití tohoto textu. 

Výstupy mohou být mapovány na index, který slouží jako vstup pro podřízenou dovednost, nebo v obou případech s kódem jazyka. V indexu je kód jazyka užitečný pro filtrování. V podobě vstupu je kód jazyka používán dovednostmi analýzy textu k informování lingvistických pravidel kolem dělení slov.

Další informace o základech dovednosti naleznete v tématu [How to define a dovednosti](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Dovednost OCR

Dovednost **optického rozpoznávání znaků** extrahuje text z obrázků. Tato dovednost předpokládá, že existuje pole normalized_images. K vygenerování tohoto pole později v tomto kurzu nastavíme konfiguraci ```"imageAction"``` v definici indexeru na ```"generateNormalizedImages"```.

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

### <a name="merge-skill"></a>Sloučit dovednost

V této části vytvoříte dovednost **sloučení** , která sloučí pole obsahu dokumentu s textem vytvořeným dovedností OCR.

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

### <a name="language-detection-skill"></a>Dovednost pro detekci jazyka

**Rozpoznávání jazyka** dovednost detekuje jazyk vstupního textu a oznamuje každému dokumentu odeslanému na žádost jeden kód jazyka. Výstupy **rozpoznávání jazyka** dovednosti použijeme jako součást vstupu pro dovednost **rozdělení textu** .

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

### <a name="text-split-skill"></a>Dovednost rozdělení textu

Níže uvedená **rozdělená** dovednost rozdělí text na stránky a omezí délku stránky na 4 000 znaků, jak je měřené `String.Length`. Algoritmus se pokusí rozdělit text na bloky, které mají velikost maximálně `maximumPageLength`. V takovém případě bude algoritmus nejlépe přerušit větu na hranici věty, takže velikost bloku dat může být mírně menší než `maximumPageLength`.

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

### <a name="entity-recognition-skill"></a>Dovednost pro rozpoznávání entit

Tato instance `EntityRecognitionSkill` je nastavena na hodnotu rozpoznat typ kategorie `organization`. Dovednosti v **rozpoznávání entit** můžou také rozpoznat typy kategorií `person` a `location`.

Všimněte si, že pole "Context" je nastaveno na hodnotu ```"/document/pages/*"``` s hvězdičkou, což znamená, že krok rozšíření je volán pro každou stránku v rámci ```"/document/pages"```.

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

### <a name="key-phrase-extraction-skill"></a>Dovednost pro extrakci klíčových frází

Stejně jako instance `EntityRecognitionSkill`, kterou jste právě vytvořili, je pro každou stránku dokumentu volána **extrakce klíčových frází** dovednost.

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

### <a name="build-and-create-the-skillset"></a>Sestavení a vytvoření dovednosti

Sestavte `Skillset` pomocí dovedností, které jste vytvořili.

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

Vytvořte dovednosti ve vyhledávací službě.

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

V této části definujete schéma indexu zadáním polí, která se mají zahrnout do prohledávatelný index, a atributů hledání pro každé pole. Pole mají typ a mohou přebírat atributy, které určují, jak se pole používá (prohledávatelné, seřaditelné a tak dále). Názvy polí v indexu nejsou nutné přesně odpovídat názvům polí ve zdroji. V pozdějším kroku přidáte mapování polí v indexeru pro připojení polí zdroj-cíl. Pro tento krok definujte index pomocí konvence pojmenování polí, které se vztahují k vaší vyhledávací aplikaci.

Toto cvičení používá následující pole a typy polí:

| názvy polí: | `id`       | obsah   | languageCode | keyPhrases         | subjekt     |
|--------------|----------|-------|----------|--------------------|-------------------|
| typy polí: | EDM. String|EDM. String| EDM. String| List < EDM. > řetězců  | List < EDM. > řetězců  |


### <a name="create-demoindex-class"></a>Vytvoření třídy DemoIndex

Pole pro tento index jsou definována pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu v souvislosti s vyhledáváním. 

Přidáme třídu modelu do nového C# souboru. Klikněte pravým tlačítkem na projekt a vyberte **přidat** > **novou položku...** , vyberte "třídu" a pojmenujte soubor `DemoIndex.cs` a pak vyberte **Přidat**.

Nezapomeňte určit, že chcete použít typy z oborů názvů `Microsoft.Azure.Search` a `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Přidejte následující definici třídy modelu do `DemoIndex.cs` a zahrňte ji do stejného oboru názvů, kde vytvoříte index.

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

Teď, když jste definovali třídu modelu, zpátky `Program.cs` můžete vytvořit definici indexu poměrně snadno. Název tohoto indexu bude "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Během testování se můžete setkat s tím, že se pokoušíte vytvořit index více než jednou. Z tohoto důvodu zkontrolujte, zda index, který se chystáte vytvořit, již existuje před pokusem o jeho vytvoření.

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

Další informace o definování indexu najdete v tématu [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a provádění transformací

Zatím jste vytvořili zdroj dat, dovednosti a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md) , který každou část spojí do jedné dvoufázové operace. Chcete-li spojit tyto dohromady v indexeru, je nutné definovat mapování polí.

+ FieldMappings se zpracovávají před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

+ OutputFieldMappings se zpracovávají po dovednosti a odkazují na sourceFieldNames, které neexistují, dokud je nevytvoří dokument pro vyhodnocování nebo rozšíření. TargetFieldName je pole v indexu.

Kromě toho, že se zapojovat vstupy do výstupů, můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

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

Očekává se, že dokončení vytváření indexeru bude trvat trochu dlouho. I když je datová sada malá, analytické dovednosti jsou náročné na výpočetní výkon. Některé dovednosti, jako je například analýza obrázků, jsou dlouhotrvající.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k problémům s navázáním dat, mapování vstupů a výstupů nebo pořadí operací se zobrazí v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumat vytváření indexeru

Kód nastaví ```"maxFailedItems"``` na-1, což dává modulu indexování pokyn, aby při importu dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je několik dokumentů. U většího zdroje dat byste měli nastavit hodnotu větší než 0.

Všimněte si také, že hodnota ```"dataToExtract"``` je nastavená na ```"contentAndMetadata"```. Tento příkaz říká indexeru, aby automaticky rozbalí obsah z různých formátů souborů a také metadata týkající se jednotlivých souborů.

Po extrahování obsahu můžete nastavit `imageAction` pro extrakci textu z obrázků nalezených ve zdroji dat. Sada ```"imageAction"``` nastavená na konfiguraci ```"generateNormalizedImages"```, v kombinaci s dovedností OPTICKÉho textu a nástrojem pro sloučení textu, říká indexeru, aby vybalily text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vložil jej jako součást pole Content. Toto chování platí pro obrázky vložené do dokumentů (můžete si představit obrázek v PDF) a také obrázky nalezené ve zdroji dat, například soubor. JPG.

## <a name="check-indexer-status"></a>Kontrolovat stav indexeru

Po definování indexeru se spustí automaticky při odeslání žádosti. V závislosti na tom, jaké dovednosti rozpoznávání jste definovali, indexování může trvat déle, než očekáváte. Chcete-li zjistit, zda indexer stále běží, použijte metodu `GetStatus`.

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

`IndexerExecutionInfo` představuje aktuální stav a historii spouštění indexeru.

Upozornění jsou společná s některými zdrojovými soubory a kombinacemi dovedností a neznamenají vždy problém. V tomto kurzu jsou upozornění neškodné (například žádné textové vstupy ze souborů JPEG).
 
## <a name="query-your-index"></a>Dotazování indexu

Po dokončení indexování můžete spustit dotazy, které vracejí obsah jednotlivých polí. Ve výchozím nastavení Azure Search vrátí prvních 50 výsledků. Vzorová data jsou malá, takže výchozí funkce funguje správně. Pokud ale pracujete s většími datovými sadami, možná budete muset zahrnout parametry do řetězce dotazu, abyste vrátili více výsledků. Pokyny najdete v tématu [Azure Search výsledků na stránce](search-pagination-page-layout.md).

Jako ověřovací krok můžete zadat dotaz na index všech polí.

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

`CreateSearchIndexClient` vytvoří nové `SearchIndexClient` s použitím hodnot, které jsou uložené v konfiguračním souboru aplikace (appSettings. JSON). Všimněte si, že se používá klíč rozhraní API vyhledávací služby, a ne klíč správce.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Výstupem je schéma indexu s názvem, typem a atributy každého pole.

Odešlete druhý dotaz na `"*"`, který vrátí všechny obsahy jednoho pole, například `organizations`.

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

Opakujte pro další pole: obsah, languageCode, klíčová fráze a organizace v tomto cvičení. Pomocí čárkami odděleného seznamu můžete vracet více polí pomocí `$select`.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetovat a znovu spustit

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem pro iterace návrhu odstranění objektů z Azure Search a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Odstranění objektu vám umožní ho znovu vytvořit pomocí stejného názvu.

V tomto kurzu se postará o kontrolu stávajících indexerů a indexů a jejich odstranění, pokud už existovaly, abyste mohli znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a dovednosti.

V případě, že je váš kód vyzrálější, možná budete chtít upravit strategii opětovného sestavení. Další informace najdete v tématu [Postup opětovného sestavení indexu](search-howto-reindex.md).

## <a name="takeaways"></a>Poznatky

V tomto kurzu jste provedli základní kroky pro vytvoření obohaceného kanálu indexování prostřednictvím vytváření součástí komponenty: zdroj dat, dovednosti, index a indexer.

Byly představeny [předdefinované dovednosti](cognitive-search-predefined-skills.md) společně s definicí dovednosti a mechanismy zřetězení dovedností prostřednictvím vstupů a výstupů. Zjistili jste taky, že `outputFieldMappings` v definici indexeru je potřeba pro směrování hodnot obohacených z kanálu do indexu s možností vyhledávání v Azure Search službě.

Nakonec jste zjistili, jak testovat výsledky a obnovit systém pro další iterace. Zjistili jste, že vydávání dotazů proti indexu vrátí výstup vytvořený obohaceným kanálem indexování. Zjistili jste také, jak kontrolovat stav indexeru a které objekty odstranit před opakovaným spuštěním kanálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob vyčištění po kurzu odstraněním skupiny prostředků obsahující službu Azure Search a Azure Blob service. Za předpokladu, že jste obě služby umístili do stejné skupiny, odstraňte nyní skupinu prostředků, abyste trvale odstranili vše, včetně služeb a veškerého uloženého obsahu, který jste pro tento kurz vytvořili. Na portálu je název skupiny prostředků na stránce Přehled každé služby.

## <a name="next-steps"></a>Další kroky

Přizpůsobte nebo rozšíříte kanál s vlastními dovednostmi. Když vytvoříte vlastní dovednost a přidáte ji do dovednosti, budete moct připojit text nebo obrázek k analýze, kterou sami píšete.

> [!div class="nextstepaction"]
> [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)
