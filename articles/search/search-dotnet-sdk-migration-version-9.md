---
title: Upgrade na Azure Search .NET SDK verze 9
titleSuffix: Azure Cognitive Search
description: Migrace kódu do Azure Search .NET SDK verze 9 ze starších verzí. Zjistěte, co je nového a jaké změny kódu jsou požadovány.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793008"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Upgrade na Azure Search .NET SDK verze 9

Pokud používáte verzi 7.0-preview nebo starší azure [search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 9.

> [!NOTE]
> Pokud chcete použít verzi 8.0-preview k vyhodnocení funkcí, které ještě nejsou obecně dostupné, můžete také podle pokynů v tomto článku upgradovat na verzi 8.0-preview z předchozích verzí.

Obecnější návod sady SDK včetně příkladů naleznete v tématu [Jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 9 sady Azure Search .NET SDK obsahuje mnoho změn z předchozích verzí. Některé z nich jsou narušující změny, ale měly by vyžadovat pouze relativně malé změny kódu. Pokyny, jak změnit kód tak, aby používal novou verzi sady SDK, najdete v tématu [Kroky pro upgrade.](#UpgradeSteps)

> [!NOTE]
> Pokud používáte verzi 4.0-preview nebo starší, měli byste nejprve upgradovat na verzi 5 a pak upgradovat na verzi 9. Pokyny najdete [v tématu Upgrade na Azure Search .NET SDK verze 5.](search-dotnet-sdk-migration-version-5.md)
>
> Vaše instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Verzi můžete nadále používat, pokud již není nejnovější, ale doporučujeme migrovat kód a používat nejnovější verzi. Při použití rozhraní REST API, musíte zadat verzi rozhraní API v každém požadavku prostřednictvím parametru api-version. Při použití sady .NET SDK určuje verze sady SDK, kterou používáte, odpovídající verzi rozhraní REST API. Pokud používáte starší sadu SDK, můžete pokračovat v spouštění tohoto kódu bez evidenčních změn, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co je nového ve verzi 9
Verze 9 sady Azure Search .NET SDK se zaměřuje na nejnovější obecně dostupnou verzi rozhraní AZURE Search REST API, konkrétně 2019-05-06. To umožňuje používat nové funkce Azure Search z aplikace .NET, včetně následujících:

* [Obohacení umělou ai](cognitive-search-concept-intro.md) je možnost extrahovat text z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat – obohacuje obsah tak, aby byl prohledávatelnější v indexu Azure Search.
* Podpora pro [složité typy](search-howto-complex-data-types.md) umožňuje modelovat téměř všechny vnořené struktury JSON v indexu Azure Search.
* [Automatické dokončování](search-autocomplete-tutorial.md) poskytuje alternativu k **navrhnout** rozhraní API pro implementaci chování hledání podle typu. Automatické dokončování "dokončí" slovo nebo frázi, kterou uživatel právě zadává.
* [Režim analýzy JsonLines](search-howto-index-json-blobs.md), součást indexování objektů Blob Azure, vytvoří jeden vyhledávací dokument na entitu JSON, která je oddělena novým řádkem.

### <a name="new-preview-features-in-version-80-preview"></a>Nové funkce náhledu ve verzi 8.0-preview
Verze 8.0-preview Azure Search .NET SDK cíle rozhraní API verze 2017-11-11-Preview. Tato verze obsahuje všechny stejné funkce verze 9, plus:

* [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro šifrování v klidovém stavu na straně služby představují novou funkci náhledu. Kromě integrovaného šifrování v klidovém stavu spravovaného společností Microsoft můžete použít další vrstvu šifrování, kde jste jediným vlastníkem klíčů.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejprve aktualizujte odkaz `Microsoft.Azure.Search` NuGet pro použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Spravovat balíčky NuGet..." v sadě Visual Studio.

Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt. V závislosti na tom, jak je váš kód strukturován, může být úspěšně znovu sestavit. Pokud ano, jste připraveni jít!

Pokud se sestavení nezdaří, budete muset opravit každou chybu sestavení. Podrobnosti o řešení jednotlivých potenciálních chyb sestavení najdete v tématu [Nejnovější změny ve verzi 9.](#ListOfChanges)

Může se zobrazit další sestavení upozornění týkající se zastaralé metody nebo vlastnosti. Upozornění budou obsahovat pokyny k použití namísto zastaralé funkce. Například pokud vaše aplikace `DataSourceType.DocumentDb` používá vlastnost, měli byste získat upozornění, že "Tento člen je zastaralé. Použijte CosmosDb místo".

Jakmile opravíte všechny chyby sestavení nebo upozornění, můžete v aplikaci provést změny a využít tak nové funkce, pokud chcete. Nové funkce sady SDK jsou podrobně popsány v části [Co je nového ve verzi 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Nejnovější změny ve verzi 9

Existuje několik narušující změny ve verzi 9, které mohou vyžadovat změny kódu kromě opětovnésestavení aplikace.

> [!NOTE]
> Níže uvedený seznam změn není vyčerpávající. Některé změny pravděpodobně nebude mít za následek chyby sestavení, ale jsou technicky porušení, protože porušují binární kompatibilitu s sestaveními, které závisí na starších verzích sestavení sady Azure Search .NET SDK. Tyto změny nejsou uvedeny níže. Při upgradu na verzi 9 znovu sestavte aplikaci, abyste se vyhnuli problémům s binární kompatibilitou.

### <a name="immutable-properties"></a>Neměnné vlastnosti

Veřejné vlastnosti několika tříd modelu jsou nyní neměnné. Pokud potřebujete vytvořit vlastní instance těchto tříd pro testování, můžete použít nové parametrizované konstruktory:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Změny pole

Třída `Field` se nyní změnila, protože může také představovat složitá pole.

Následující `bool` vlastnosti jsou nyní nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Důvodem je, že `null` tyto vlastnosti musí být nyní v případě složitých polí. Pokud máte kód, který čte tyto vlastnosti, `null`musí být připraven ke zpracování . Všimněte si, `Field` že všechny ostatní vlastnosti byly vždy a nadále `null` být null, a některé z nich bude také v případě složitých polí - konkrétně následující:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Byl proveden bezparametrický `Field` konstruktor . `internal` Od této chvíle `Field` každý vyžaduje explicitní název a datový typ v době výstavby.

### <a name="simplified-batch-and-results-types"></a>Zjednodušené typy dávek a výsledků

Ve verzi 7.0-preview a starší, různé třídy, které zapouzdřují skupiny dokumentů byly strukturovány do hierarchie paralelní třídy:

  -  `DocumentSearchResult`a `DocumentSearchResult<T>` zděděné z`DocumentSearchResultBase`
  -  `DocumentSuggestResult`a `DocumentSuggestResult<T>` zděděné z`DocumentSuggestResultBase`
  -  `IndexAction`a `IndexAction<T>` zděděné z`IndexActionBase`
  -  `IndexBatch`a `IndexBatch<T>` zděděné z`IndexBatchBase`
  -  `SearchResult`a `SearchResult<T>` zděděné z`SearchResultBase`
  -  `SuggestResult`a `SuggestResult<T>` zděděné z`SuggestResultBase`

Odvozené typy bez parametru obecného typu byly určeny k použití ve scénářích `Document` "dynamicky typed" a předpokládanépoužití typu.

Počínaje verzí 8.0-preview byly odebrány základní třídy a neobecné odvozené třídy. Pro dynamicky zadávaných `IndexBatch<Document>`scénářů můžete použít , `DocumentSearchResult<Document>`a tak dále.
 
### <a name="removed-extensibleenum"></a>Odstraněno ExtensibleEnum

Základní `ExtensibleEnum` třída byla odebrána. Všechny třídy, které jsou odvozeny z `AnalyzerName`něj `DataType`jsou `DataSourceType` nyní struktury, například , a například. Jejich `Create` metody byly také odstraněny. Můžete jednoduše odebrat `Create` volání, protože tyto typy jsou implicitně převoditelné z řetězců. Pokud to má za následek chyby kompilátoru, můžete explicitně vyvolat operátor převodu prostřednictvím přetypování na disambiguate typy. Kód můžete například změnit takto:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

měli změnit na:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Vlastnosti, které držely volitelné hodnoty těchto typů, jsou nyní explicitně zadány jako nullable, takže jsou i nadále volitelné.

### <a name="removed-facetresults-and-hithighlights"></a>Odstraněno FacetResults a HitHighlights

`FacetResults` Třídy `HitHighlights` a byly odebrány. Omezující fazeta výsledky `IDictionary<string, IList<FacetResult>>` jsou nyní `IDictionary<string, IList<string>>`zadány jako a hit zdůrazňuje jako . Rychlý způsob, jak vyřešit chyby sestavení `using` zavedené touto změnou, je přidat aliasy v horní části každého souboru, který používá odebrané typy. Například:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Změnit na Synonymmap 

Konstruktor `SynonymMap` již nemá `enum` parametr `SynonymMapFormat`pro . Tento výčet měl pouze jednu hodnotu, a proto byl redundantní. Pokud se v důsledku toho zobrazí chyby sestavení, `SynonymMapFormat` jednoduše odeberte odkazy na parametr.

### <a name="miscellaneous-model-class-changes"></a>Různé změny třídy modelů

Vlastnost `AutocompleteMode` již `AutocompleteParameters` nelze zrušit. Pokud máte kód, který přiřazuje tuto vlastnost , `null`můžete ji jednoduše odebrat a vlastnost bude automaticky inicializována na výchozí hodnotu.

Pořadí parametrů konstruktoru `IndexAction` se nyní změnilo, když je tento konstruktor automaticky generován. Namísto použití konstruktoru doporučujeme použít `IndexAction.Upload` `IndexAction.Merge`metody výroby , a tak dále.

### <a name="removed-preview-features"></a>Odebrané funkce náhledu

Pokud upgradujete z verze 8.0 preview na verzi 9, uvědomte si, že šifrování pomocí klíčů spravovaných zákazníkem bylo odebráno, protože tato funkce je stále ve verzi Preview. Konkrétně vlastnosti `EncryptionKey` `Index` a `SynonymMap` byly odebrány.

Pokud vaše aplikace má pevné závislosti na této funkci, nebude možné upgradovat na verzi 9 Azure Search .NET SDK. Můžete pokračovat v používání verze 8.0-preview. Mějte však na paměti, že **nedoporučujeme používat sady Preview SDK v produkčních aplikacích**. Funkce náhledu jsou pouze pro vyhodnocení a mohou se změnit.

> [!NOTE]
> Pokud jste vytvořili šifrované indexy nebo mapy synonym pomocí verze 8.0-preview sady SDK, budete je stále moci používat a upravovat jejich definice pomocí verze 9 sady SDK bez negativního vlivu na jejich stav šifrování. Verze 9 sady SDK neodešle `encryptionKey` vlastnost do rozhraní REST API a v důsledku toho rozhraní REST API nezmění stav šifrování prostředku. 

### <a name="behavioral-change-in-data-retrieval"></a>Změna chování při načítání dat

Pokud používáte "dynamicky `Search`zadávaný" , `Suggest`nebo `Get` API, `Document`které vracejí instance typu , uvědomte si, že `object[]` nyní `string[]`dekontizovat prázdná pole JSON namísto .

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, podívejte [se na .NET Návod](search-howto-dotnet-sdk.md).

Uvítáme vaši zpětnou vazbu k sdk. Pokud narazíte na problémy, neváhejte a požádejte nás o pomoc na [Stack Přetečení](https://stackoverflow.com/questions/tagged/azure-search). Pokud najdete chybu, můžete podat problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Nezapomeňte předponu název problému s "[Azure Search]".

Děkujeme vám, že používáte Azure Search!
