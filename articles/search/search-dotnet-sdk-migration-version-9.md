---
title: Upgrade na Azure Search .NET SDK verze 9
titleSuffix: Azure Cognitive Search
description: Migruje kód do sady Azure Search .NET SDK verze 9 ze starších verzí. Zjistěte, co je nového a kdy se vyžadují změny kódu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 76cb2f94c949217c36fdccb8c64a53555bab227c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606330"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Upgrade na Azure Search .NET SDK verze 9

Pokud používáte verzi 7,0-Preview nebo starší [sadu Azure Search .NET SDK](/dotnet/api/overview/azure/search), Tento článek vám pomůže při upgradu aplikace na použití verze 9.

> [!NOTE]
> Pokud chcete použít verzi 8,0-Preview k vyhodnocení funkcí, které nejsou všeobecně dostupné, můžete postupovat podle pokynů v tomto článku a upgradovat na 8,0-Preview z předchozích verzí.

Obecnější návod k sadě SDK, včetně příkladů, najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 9 sady Azure Search .NET SDK obsahuje mnoho změn z dřívějších verzí. Některé z nich jsou zásadními změnami, ale měly by vyžadovat pouze poměrně drobné změny kódu. Pokyny ke změně kódu pro použití nové verze sady SDK najdete v tématu [Postup upgradu](#UpgradeSteps) .

> [!NOTE]
> Pokud používáte verzi 4,0-Preview nebo starší, měli byste nejdřív upgradovat na verzi 5 a pak upgradovat na verzi 9. Pokyny najdete v tématu [upgrade na sadu Azure Search .NET SDK verze 5](search-dotnet-sdk-migration-version-5.md) .
>
> Vaše instance služby Azure Search podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si kód migrovali na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co je nového ve verzi 9
Verze 9 Azure Search .NET SDK cílí na verzi 2019-05-06 Azure Search REST API s následujícími funkcemi:

* [Obohacení AI](cognitive-search-concept-intro.md) je schopnost extrahovat text z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat – vylepšuje obsah, aby bylo lépe prohledávatelné Azure Search indexu.
* Podpora [komplexních typů](search-howto-complex-data-types.md) umožňuje modelovat skoro všechny vnořené struktury JSON v indexu Azure Search.
* [Automatické dokončování](search-add-autocomplete-suggestions.md) nabízí alternativu k rozhraní API pro **navrhování** pro implementaci chování hledání podle zadání. Automatické dokončování "dokončí" slovo nebo frázi, kterou uživatel právě zapisuje.
* [Režim analýzy JsonLines](search-howto-index-json-blobs.md), část indexování objektů BLOB v Azure, vytvoří jeden vyhledávací dokument pro každou entitu JSON, která je oddělená novým řádkem.

### <a name="new-preview-features-in-version-80-preview"></a>Nové funkce ve verzi Preview verze 8,0-Preview
Verze 8,0-Preview rozhraní API cíle Azure Search .NET SDK verze 2017-11-11-Preview. Tato verze zahrnuje všechny stejné funkce verze 9 a navíc:

* [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro šifrování na straně služby – REST je nová funkce verze Preview. Kromě integrovaného šifrování v klidovém formátu, které spravuje Microsoft, můžete použít další vrstvu šifrování, kde jste jediným vlastníkem klíčů.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejdřív aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Search` použití buď konzoly Správce balíčků NuGet, nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. V závislosti na tom, jak je kód strukturovaný, se může úspěšně znovu sestavit. Pokud ano, jste připraveni!

Pokud se sestavení nezdaří, bude nutné opravit každou chybu sestavení. Podrobnosti o tom, jak vyřešit každou možnou chybu sestavení, naleznete [v tématu průlomové změny ve verzi 9](#ListOfChanges) .

Může se zobrazit další upozornění sestavení související s zastaralými metodami nebo vlastnostmi. Upozornění budou obsahovat pokyny k používání funkce místo zastaralé funkce. Například pokud vaše aplikace používá `DataSourceType.DocumentDb` vlastnost, mělo by se zobrazit upozornění, že "Tento člen je zastaralý. Místo toho použijte CosmosDb.

Jakmile budete chtít opravit chyby nebo upozornění sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete. Nové funkce v sadě SDK jsou podrobně popsané v části [co je nového ve verzi 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Přerušující změny ve verzi 9

K dispozici je několik zásadních změn ve verzi 9, které mohou vyžadovat změny kódu navíc k opakovanému sestavování aplikace.

> [!NOTE]
> Níže uvedený seznam změn není vyčerpávající. Některé změny by nejspíš nevedly k chybám sestavení, ale budou se technicky rušit, protože přerušují binární kompatibilitu se sestaveními, která jsou závislá na dřívějších verzích Azure Search .NET SDK. Tyto změny nejsou uvedeny níže. Při upgradu na verzi 9 prosím sestavte aplikaci znovu, aby nedocházelo k problémům s binárními kompatibilitou.

### <a name="immutable-properties"></a>Neměnné vlastnosti

Veřejné vlastnosti několika tříd modelu jsou nyní neměnný. Pokud potřebujete vytvořit vlastní instance těchto tříd pro testování, můžete použít nové parametrizované konstruktory:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Změny v poli

`Field`Třída se změnila nyní, která může také představovat složitá pole.

Následující `bool` vlastnosti jsou nyní Nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Důvodem je, že tyto vlastnosti se teď musí nacházet `null` v případě složitých polí. Pokud máte kód, který tyto vlastnosti čte, musí být připraven zpracovat `null` . Všimněte si, že všechny ostatní vlastnosti se `Field` vždycky nastavily a budou mít i nadále hodnotu null a některé z nich budou také `null` v případě komplexních polí – konkrétně následující:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Konstruktor bez parametrů `Field` byl proveden `internal` . Od `Field` této chvíle vyžaduje explicitní název a datový typ v době konstrukce.

### <a name="simplified-batch-and-results-types"></a>Zjednodušené typy dávek a výsledků

Ve verzi 7,0-Preview a dříve byly různé třídy, které zapouzdřují skupiny dokumentů, strukturované do paralelních hierarchií tříd:

  -  `DocumentSearchResult` a `DocumentSearchResult<T>` Zděděno od `DocumentSearchResultBase`
  -  `DocumentSuggestResult` a `DocumentSuggestResult<T>` Zděděno od `DocumentSuggestResultBase`
  -  `IndexAction` a `IndexAction<T>` Zděděno od `IndexActionBase`
  -  `IndexBatch` a `IndexBatch<T>` Zděděno od `IndexBatchBase`
  -  `SearchResult` a `SearchResult<T>` Zděděno od `SearchResultBase`
  -  `SuggestResult` a `SuggestResult<T>` Zděděno od `SuggestResultBase`

Odvozené typy bez parametru obecného typu by se měly používat ve scénářích s dynamickým zadáním a s předpokládaným využitím tohoto `Document` typu.

Počínaje verzí 8,0-Preview byly všechny základní třídy a neobecné odvozené třídy odebrány. Pro scénáře s dynamickým typem můžete použít `IndexBatch<Document>` , `DocumentSearchResult<Document>` a tak dále.
 
### <a name="removed-extensibleenum"></a>Odebrané ExtensibleEnum

`ExtensibleEnum`Základní třída byla odebrána. Všechny třídy, které jsou z něj odvozeny, jsou nyní strukturami, například `AnalyzerName` , `DataType` a `DataSourceType` . Jejich `Create` metody byly také odebrány. Můžete pouze odebrat volání, `Create` protože tyto typy jsou implicitně převoditelné z řetězců. Pokud dojde k chybám kompilátoru, můžete explicitně vyvolat operátor převodu prostřednictvím přetypování pro jednoznačné typy. Například můžete změnit kód takto:

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

Vlastnosti, které drží volitelné hodnoty těchto typů, jsou nyní explicitně zadány jako Nullable, aby byly i nadále volitelné.

### <a name="removed-facetresults-and-hithighlights"></a>Odebrané FacetResults a HitHighlights

`FacetResults`Třídy a byly `HitHighlights` odebrány. Výsledky omezující vlastnosti se teď zadávají jako `IDictionary<string, IList<FacetResult>>` a zvýrazní se `IDictionary<string, IList<string>>` . Rychlý způsob, jak vyřešit chyby sestavení zavedené touto změnou, je přidat `using` aliasy v horní části každého souboru, který používá odebrané typy. Například:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Změnit na SynonymMap 

`SynonymMap`Konstruktor již nemá `enum` parametr pro `SynonymMapFormat` . Tento výčet má pouze jednu hodnotu, a proto byl redundantní. Pokud se v důsledku toho zobrazí chyby sestavení, jednoduše odeberte odkazy na `SynonymMapFormat` parametr.

### <a name="miscellaneous-model-class-changes"></a>Různé třídy modelu – změny

`AutocompleteMode`Vlastnost `AutocompleteParameters` již není nastavena na hodnotu null. Pokud máte kód, kterému tuto vlastnost přiřadíte `null` , můžete ji jednoduše odebrat a vlastnost se automaticky inicializuje na výchozí hodnotu.

Pořadí parametrů `IndexAction` konstruktoru bylo nyní změněno, je-li tento konstruktor automaticky generován. Namísto použití konstruktoru doporučujeme použít metody továrny `IndexAction.Upload` , `IndexAction.Merge` a tak dále.

### <a name="removed-preview-features"></a>Odebrané funkce verze Preview

Pokud upgradujete z verze 8,0-Preview na verzi 9, počítejte s tím, že šifrování pomocí klíčů spravovaných zákazníkem bylo odebráno, protože tato funkce je stále ve verzi Preview. Konkrétně byly `EncryptionKey` `Index` odebrány vlastnosti a `SynonymMap` .

Pokud má vaše aplikace pevně závislou funkci, nebudete moct upgradovat na verzi 9 sady Azure Search .NET SDK. Můžete dál používat verzi 8,0-Preview. Mějte ale na paměti, že **v produkčních aplikacích nedoporučujeme používat sady SDK pro verze Preview**. Funkce ve verzi Preview jsou jenom pro vyhodnocení a můžou se změnit.

> [!NOTE]
> Pokud jste vytvořili šifrované indexy nebo mapy synonym pomocí verze 8,0-Preview sady SDK, budete je moct používat a upravovat jejich definice pomocí verze 9 sady SDK, aniž by to ovlivnilo jejich stav šifrování. Verze 9 sady SDK nebude odesílat `encryptionKey` vlastnost do REST API a v důsledku toho REST API stav šifrování prostředku nemění. 

### <a name="behavioral-change-in-data-retrieval"></a>Změna chování při načítání dat

Pokud používáte "dynamicky typované" `Search` , `Suggest` nebo `Get` rozhraní API, které vracejí instance typu `Document` , uvědomte si, že nyní deserializovat prázdná pole JSON `object[]` namísto `string[]` .

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, přečtěte si [článek postupy pro .NET](search-howto-dotnet-sdk.md).

Vaše názory na sadu SDK jsme uvítá. Pokud narazíte na problémy, řekněte nám, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude předponou "[Azure Search]".

Děkujeme, že používáte Azure Search!