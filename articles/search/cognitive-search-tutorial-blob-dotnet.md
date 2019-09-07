---
title: C#Kurz pro volání rozhraní API služeb Cognitive Services v kanálu indexování – Azure Search
description: Tento kurz vás krok za krokem provede ukázkovou extrakcí dat a zpracováním přirozeného jazyka a obrázků pomocí umělé inteligence v indexování Azure Search s cílem extrahovat a transformovat data.
manager: nitinme
author: MarkHeff
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 260f6a6141903ea1fd7edcfe2e031091bba322be
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744756"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Návodu Volání rozhraní API služeb Cognitive Services v kanálu indexování Azure Search

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Dovednosti jsou zajištěny při zpracování přirozeného jazyka (NLP) a možností analýzy obrázků v Cognitive Services. Prostřednictvím složení a konfigurace dovednosti můžete extrahovat text a text reprezentace obrázku nebo naskenovaného souboru dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Konečný výsledek je bohatě doplňující obsah v indexu Azure Search vytvořeným kanálem indexování s použitím AI.

V tomto kurzu použijete sadu .NET SDK k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření kanálu indexace, který rozšiřuje ukázková data na cestě k indexu
> * Použití integrovaných dovedností: optické rozpoznávání znaků, fúze textu, detekce jazyka, rozdělení textu, rozpoznávání entit, extrakce klíčových frází
> * Zjištění, jak mapováním vstupů na výstupy v sadě dovedností řetězit dovednosti za sebe
> * Spouštění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstup indexu v Azure Search, který se dá prohledávat fulltextově Index můžete rozšířit pomocí dalších standardních funkcí, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md) a [filtry](search-filters.md).

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

Abyste mohli komunikovat se službou Azure Search, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. [Přihlaste se k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot. V tomto kurzu se předpokládá, že název kontejneru je "Basic-demo-data-PR".

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** a nahrajte [vzorová data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. To můžete udělat tak, že přejdete na svůj účet úložiště v Azure Portal vyberete **přístupové klíče**a potom zkopírujete pole **připojovací řetězec** .

   Připojovací řetězec by měl být adresa URL, která vypadá nějak takto:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který může běžet v .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Sada Azure Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které umožňují spravovat vaše indexy, zdroje dat, indexery a dovednosti a také nahrávat a spravovat dokumenty a provádět dotazy, a to vše bez nutnosti zabývat se podrobnostmi o http a JSON. . Tyto klientské knihovny jsou distribuovány jako balíčky NuGet.

Pro tento projekt budete muset nainstalovat verzi 9 `Microsoft.Azure.Search` balíčku NuGet a nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

Nainstalujte balíček `Microsoft.Azure.Search` NuGet pomocí konzoly Správce balíčků v aplikaci Visual Studio. Chcete-li otevřít konzolu Správce balíčků, vyberte **nástroje** > správce**balíčků** > NuGet**Konzola správce balíčků**. Pokud chcete spustit příkaz, přejděte na [stránku balíčku NuGet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search), vyberte verze 9 a zkopírujte příkaz Správce balíčků. V konzole správce balíčků spusťte tento příkaz.

Chcete-li `Microsoft.Extensions.Configuration.Json` nainstalovat balíček NuGet v aplikaci Visual Studio, vyberte **nástroje** >  > **Správce balíčků NuGet** **Spravovat balíčky NuGet pro řešení...** . Vyberte Procházet a vyhledejte `Microsoft.Extensions.Configuration.Json` balíček NuGet. Po nalezení vyberte balíček, vyberte projekt, potvrďte, že verze je nejnovější stabilní verze, a pak vyberte nainstalovat.

## <a name="add-azure-search-service-information"></a>Přidat informace o Azure Search službě

Aby bylo možné se připojit ke službě Azure Search, budete muset do svého projektu přidat informace o vyhledávací službě. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Přidat** > **novou položku...** Zadejte název souboru `appsettings.json` a vyberte **Přidat**. 

Tento soubor bude nutné zahrnout do výstupního adresáře. Provedete to tak, že `appsettings.json` kliknete pravým tlačítkem na a vyberete **vlastnosti**. Změňte hodnotu **Kopírovat do výstupního adresáře** na **Kopírovat novější**.

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

Tento kurz používá mnoho různých typů z různých oborů názvů. Aby bylo možné tyto typy použít, přidejte následující do `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Vytvoření klienta

Vytvořte instanci `SearchServiceClient` třídy.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`Vytvoří nový `SearchServiceClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appSettings. JSON).

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

Vytvořte novou `DataSource` instanci voláním `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage`vyžaduje, abyste zadali název zdroje dat, připojovací řetězec a název kontejneru objektů BLOB.

I když se v tomto kurzu nepoužívá, je definovaná taky zásada podmíněného odstranění, která se používá k identifikaci odstraněných objektů blob na základě hodnoty sloupce obnovitelného odstranění. Následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost `IsDeleted` metadata s hodnotou `true`.

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

Teď, když jste `DataSource` objekt inicializoval, vytvořte zdroj dat. `SearchServiceClient` obsahuje vlastnost `DataSources`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytvoření, výpisu, aktualizaci nebo odstranění zdrojů dat Azure Search.

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

Vzhledem k tomu, že je to váš první požadavek, podívejte se na portál Azure Portal a potvrďte, že se zdroj dat vytvořil ve službě Azure Search. Na stránce řídicího panelu vyhledávací služby ověřte, že dlaždice Zdroje dat má novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje.

  ![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice Zdroje dat na portálu")

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V této části definujete sadu kroků obohacení, které chcete použít pro vaše data. Každý krok obohacení se nazývá *dovednost* a sada kroků obohacení o *dovednosti*. V tomto kurzu se pro sadu dovedností používají [předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md):

+ [Optické rozpoznávání znaků](cognitive-search-skill-ocr.md) pro rozpoznávání vytištěného a rukopisného textu v souborech obrázků.

+ [Sloučení textu](cognitive-search-skill-textmerger.md) pro konsolidaci textu z kolekce polí do jednoho pole.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md) pro rozdělení velkého obsahu do menších bloků před voláním dovednosti pro extrakci klíčových frází a dovedností pro rozpoznávání entit. Extrakce klíčových frází a rozpoznávání entit přijímají vstupy o 50 000 nebo méně znaků. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze

Při počátečním zpracování Azure Search napraskliny každý dokument, aby bylo možné číst obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. V takovém případě nastavte vstup pro jako ```"/document/content"``` pro použití tohoto textu. 

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Dovednost OCR

Dovednost **optického rozpoznávání znaků** extrahuje text z obrázků. Tato dovednost předpokládá, že existuje pole normalized_images. K vygenerování tohoto pole později v tomto kurzu nastavíme ```"imageAction"``` konfiguraci v definici indexeru na. ```"generateNormalizedImages"```

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

Níže uvedená **rozdělená** dovednost rozdělí text na stránky a omezí délku stránky na 4 000 znaků měřených podle `String.Length`. Algoritmus se pokusí rozdělit text na bloky, které mají největší `maximumPageLength` velikost. V takovém případě algoritmus provede svůj nejlepší postup pro rozdělení věty na hranici věty, takže velikost bloku dat může být menší než `maximumPageLength`.

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

Tato `EntityRecognitionSkill` instance je nastavena na hodnotu rozpoznat typ `organization`kategorie. Dovednosti v **rozpoznávání entit** můžou také rozpoznat typy `person` kategorií a. `location`

Všimněte si, že pole "Context" je nastaveno ```"/document/pages/*"``` na hvězdičku, což znamená, že krok rozšíření je volán pro každou stránku ```"/document/pages"```v rámci.

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

Stejně jako instance, kterou jste právě vytvořili, je pro každou stránku dokumentu volána extrakce klíčových frází dovednost. `EntityRecognitionSkill`

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

Sestavte se `Skillset` s využitím dovedností, které jste vytvořili.

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

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Vytvoření třídy DemoIndex

Pole pro tento index jsou definována pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. 

Přidáme třídu modelu do nového C# souboru. Klikněte pravým tlačítkem na projekt a vyberte **Přidat** > **novou položku...** , vyberte "třídu" a pojmenujte soubor `DemoIndex.cs`a pak vyberte **Přidat**.

Nezapomeňte určit, že chcete použít typy z `Microsoft.Azure.Search` oboru názvů a. `Microsoft.Azure.Search.Models`

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

Teď, když jste definovali třídu modelu, `Program.cs` můžete vytvořit definici indexu poměrně snadno. Název tohoto indexu bude "demoindex".

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

Další informace o definování indexu najdete v článku o [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a spouštění transformací

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí.

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

Očekává se, že dokončení vytváření indexeru bude trvat trochu dlouho. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi.

### <a name="explore-creating-the-indexer"></a>Prozkoumat vytváření indexeru

Kód nastaví ```"maxFailedItems"``` na-1, který dává modulu indexování pokyn, aby při importu dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Všimněte si také ```"dataToExtract"``` , že je ```"contentAndMetadata"```nastavena na. Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. V ```"imageAction"```kombinaci s dovedností rozpoznávání textu a dovedností a sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. ```"generateNormalizedImages"``` Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda indexer stále běží, použijte `GetStatus` metodu.

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

`IndexerExecutionInfo`představuje aktuální stav a historii provádění indexeru.

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy).
 
## <a name="query-your-index"></a>Dotázání indexu

Po dokončení indexování můžete spustit dotazy, které vracejí obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

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

`CreateSearchIndexClient`Vytvoří nový `SearchIndexClient` pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appSettings. JSON). Všimněte si, že se používá klíč rozhraní API vyhledávací služby, a ne klíč správce.

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

Opakujte pro další pole: obsah, languageCode, klíčová fráze a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem pro iterace návrhu odstranění objektů z Azure Search a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

V tomto kurzu se postará o kontrolu stávajících indexerů a indexů a jejich odstranění, pokud už existovaly, abyste mohli znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a dovednosti.

Až se váš kód bude blížit dokončení, možná budete chtít zdokonalit strategii opětovného sestavování. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

## <a name="takeaways"></a>Shrnutí

V tomto kurzu jste provedli základní kroky pro vytvoření obohaceného kanálu indexování prostřednictvím vytváření součástí komponenty: zdroj dat, dovednosti, index a indexer.

Představili jsme si [předdefinované dovednosti](cognitive-search-predefined-skills.md), definice sad dovedností a mechanismy, jak pomocí vstupů a výstupů řetězit dovednosti za sebe. Naučili jste se, že `outputFieldMappings` v definici indexu umožňuje směrovat rozšířené hodnoty z kanálu do prohledávatelného indexu ve službě Azure Search.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak po kurzu vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob. Za předpokladu, že jste vložili obě služby do stejné skupiny, odstraňte skupinu prostředků. Tím se trvale odstraní všechno, co v ní je, včetně služeb a veškerého uloženého obsahu, který jste v tomto kurzu vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete.

> [!div class="nextstepaction"]
> [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)
