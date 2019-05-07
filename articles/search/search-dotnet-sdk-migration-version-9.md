---
title: Upgrade na Azure Search .NET SDK verze 9 – Azure Search
description: Migrace ze starší verze kódu do Azure Search .NET SDK verze 9. Zjistěte, co je nového a jaké změny kódu jsou povinné.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f540bc304920073bcd823adcf6c9dd47cb2cf93b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159747"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-9"></a>Upgrade na Azure Search .NET SDK verze 9

Pokud používáte verzi 7.0 preview nebo starší aplikace [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace pro používání verze 9.

> [!NOTE]
> Pokud budete chtít slouží k vyhodnocení funkce, které ještě nejsou obecně dostupná verze 8.0-preview, můžete také podle pokynů v tomto článku k upgradu z předchozí verze 8.0 ve verzi Preview.

Další obecné návod sady SDK, včetně příkladů najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 9 služby Azure Search .NET SDK obsahuje mnoho změn z předchozích verzí. Některé z nich jsou rozbíjející změny, ale vyžadují by měl pouze relativně malé změny kódu. Zobrazit [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak změnit váš kód k použití nové verze sady SDK.

> [!NOTE]
> Pokud používáte verzi 4.0 ve verzi preview nebo starší, byste nejprve upgradovat na verze 5 a poté provedli upgrade na verzi 9. Zobrazit [upgrade na Azure Search .NET SDK verze 5](search-dotnet-sdk-migration-version-5.md) pokyny.
>
> Instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete nadále používat verzi, pokud již není nejnovější, ale doporučujeme migrovat kód Refaktorovat pro použití na nejnovější verzi. Při použití rozhraní REST API, je nutné zadat verzi rozhraní API v každé žádosti prostřednictvím parametru verze api-version. Při použití sady .NET SDK, určuje verzi sady SDK, které používáte odpovídající verzi rozhraní REST API. Pokud používáte starší sada SDK, můžete nadále spouštět tento kód beze změny i v případě, že služba se upgraduje na novější verzi rozhraní API podporují.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co je nového ve verzi 9
Azure Search .NET SDK verze 9, zaměřuje na nejnovější verzi obecně dostupnou verzi REST API služby Azure Search, konkrétně 2019-05-06. To umožňuje používat nové funkce služby Azure Search z aplikace .NET, včetně následujících:

* [Kognitivní vyhledávání](cognitive-search-concept-intro.md) je funkce AI ve službě Azure Search používá k extrakci textu z bitové kopie, objekty BLOB a jiných zdrojů nestrukturovaných dat – rozšíření obsahu provést další prohledávatelná v indexu Azure Search.
* Podpora pro [komplexní typy](search-howto-complex-data-types.md) umožňuje modelovat téměř jakékoli vnořené struktury JSON do indexu Azure Search.
* [Automatické dokončování](search-autocomplete-tutorial.md) poskytuje alternativu k **navrhnout** rozhraní API pro implementaci chování vyhledávání jako vám type. Automatické dokončování "dokončení" slovo nebo frázi, která je aktuálně zadání uživatele.
* [Režim parsování JsonLines](search-howto-index-json-blobs.md), součást sady objektů Blob v Azure, indexování, vytvoří jeden hledání v dokumentech na entitu JSON, který je oddělen znakem nového řádku.

### <a name="new-preview-features-in-version-80-preview"></a>Nové funkce ve verzi preview ve verzi 8.0 – preview
Verze 8.0 – ve verzi preview sady .NET SDK služby Azure Search cílí na rozhraní API verze 2017-11-11-Preview. Tato verze zahrnuje stejné funkce verze 9, a navíc:

* [Klíče spravované zákazníkem šifrování](search-security-manage-encryption-keys.md) straně služby šifrování neaktivních je nová funkce ve verzi preview. Kromě integrované šifrování neaktivních spravovaný microsoftem můžete použít další úroveň šifrování, kde jste jediným vlastníkem klíčů.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejprve aktualizujte referenci NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo tím, že pravým tlačítkem myši na odkazy projektu a vyberte "Spravovat NuGet balíčky..." v sadě Visual Studio.

Po stažení nové balíčky a jejich závislostí NuGet znovu sestavte projekt. V závislosti na tom, jak váš kód strukturovaná ji může znovu sestavit úspěšně. Pokud ano, jste připraveni začít!

Pokud se sestavení nezdaří, je potřeba opravit všechny chyby sestavení. Zobrazit [nejnovější změny ve verzi 9](#ListOfChanges) pro sestavení podrobnosti o tom, jak vyřešit každý potenciální chyby.

Může se zobrazit další upozornění související s zastaralé metody nebo vlastnosti. Upozornění bude obsahovat pokyny, jak použít namísto zastaralé funkce. Například, pokud vaše aplikace používá `DataSourceType.DocumentDb` vlastnost, měli byste obdržet upozornění s textem "Tento člen je zastaralý. Pomocí služby cosmos DB místo".

Po vyřešili jsme upozornění ani chyby sestavení, můžete aplikace výhod nových funkcí, pokud chcete měnit. Nové funkce v sadě Windows SDK jsou podrobně popsány v [co je nového ve verzi 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Rozbíjející změny ve verzi 9

Existuje několik rozbíjející změny ve verzi 9, které mohou vyžadovat změny kódu kromě znovu sestavit aplikaci.

> [!NOTE]
> Seznam změn níže není vyčerpávající. Některé změny pravděpodobně nebudou vést k chybám sestavení, ale jsou technicky zásadní, protože jejich porušit binární kompatibilitu s sestavení, které jsou závislé na dřívější verze sestavení .NET SDK služby Azure Search. Tyto změny nejsou uvedené níže. Při upgradu na verzi 9, abyste předešli problémům s žádné binární kompatibilitu sestavte aplikaci.

### <a name="making-properties-immutable"></a>Provádění neměnné vlastnosti

Veřejné vlastnosti několik tříd modelu jsou nyní neměnné. Pokud je potřeba vytvořit vlastní instance těchto tříd pro testování, můžete použít nový konstruktor s parametry:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Změny pole

`Field` Změnila třída teď může také představovat komplexní pole.

Následující `bool` vlastnosti jsou nyní s povolenou hodnotou NULL:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Důvodem je, že tyto vlastnosti musí být nyní `null` v případě složitých pole. Pokud máte kód, který čte tyto vlastnosti, musí být připraveni `null`. Všimněte si, že všechny ostatní vlastnosti `Field` byly vždy a nadále s možnou hodnotou Null, a některé z nich bude také `null` v případě složitých pole--především následující:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Konstruktor bez parametrů z `Field` provedl `internal`. Od této chvíle každý `Field` vyžaduje explicitní název a datový typ v době konstrukce.

### <a name="simplification-of-batch-and-results-types"></a>Zjednodušení typů služby batch a výsledky

Ve verzi 7.0 preview a starší byly strukturované třídách, které provádí zapouzdření skupiny dokumentů do hierarchií paralelní třídy:

  -  `DocumentSearchResult` a `DocumentSearchResult<T>` zděděno od `DocumentSearchResultBase`
  -  `DocumentSuggestResult` a `DocumentSuggestResult<T>` zděděno od `DocumentSuggestResultBase`
  -  `IndexAction` a `IndexAction<T>` zděděno od `IndexActionBase`
  -  `IndexBatch` a `IndexBatch<T>` zděděno od `IndexBatchBase`
  -  `SearchResult` a `SearchResult<T>` zděděno od `SearchResultBase`
  -  `SuggestResult` a `SuggestResult<T>` zděděno od `SuggestResultBase`

Odvozené typy bez parametru obecného typu se by se použít ve scénářích "dynamicky typované" a předpokládá, že využití `Document` typu.

Od verze 8.0-preview, základní třídy a odvozené třídy neobecnou všechny byly odebrány. Pro dynamicky typované scénáře, můžete použít `IndexBatch<Document>`, `DocumentSearchResult<Document>`, a tak dále.
 
### <a name="removal-of-extensibleenum"></a>Odebrání ExtensibleEnum

`ExtensibleEnum` Základní třída odebrala. Všechny třídy, které z něj odvozenou jsou nyní struktury, jako je například `AnalyzerName`, `DataType`, a `DataSourceType` třeba. Jejich `Create` metody také byly odebrány. Můžete pouze odebrat volání `Create` vzhledem k tomu, že tyto typy jsou implicitně převést z řetězce. Pokud, která má za následek chyby kompilátoru, můžete vyvolat explicitní operátor převodu prostřednictvím přetypování k rozlišení typů. Například můžete změnit kód následujícím způsobem:

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

Vlastnosti, které se nachází volitelné hodnoty těchto typů jsou nyní explicitně typu s možnou hodnotou Null, budou pokračovat v práci na nepovinný.

### <a name="removal-of-facetresults-and-hithighlights"></a>Odebrání FacetResults a HitHighlights

`FacetResults` a `HitHighlights` třídy byly odebrány. Omezující vlastnost výsledky jsou nyní zadán jako `IDictionary<string, IList<FacetResult>>` a stiskněte klávesu zvýrazní jako `IDictionary<string, IList<string>>`. Rychlý způsob, jak vyřešit chyby sestavení zavedených v této změny je přidání `using` aliasy v horní části každého souboru, který používá odebrané typy. Příklad:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Změnit na SynonymMap 

`SynonymMap` Již nemá konstruktor `enum` parametr pro `SynonymMapFormat`. Tento výčet pouze má jednu hodnotu a byl proto redundantní. Pokud se zobrazí v důsledku této chyby sestavení, jednoduše odebrat odkazy `SynonymMapFormat` parametru.

### <a name="miscellaneous-model-class-changes"></a>Model pro různé třídy změny

`AutocompleteMode` Vlastnost `AutocompleteParameters` už nejsou s možnou hodnotou Null. Pokud máte kód, který se přiřadí tuto vlastnost na `null`, můžete ho jednoduše odebrat, a vlastnost bude automaticky inicializován na výchozí hodnotu.

Pořadí parametrů pro `IndexAction` konstruktoru došlo ke změně teď, když tento konstruktor se generuje automaticky. Namísto použití konstruktoru, doporučujeme použít metody pro vytváření objektů `IndexAction.Upload`, `IndexAction.Merge`, a tak dále.

### <a name="removed-preview-features"></a>Funkce odebrané ve verzi preview

Pokud provádíte upgrade z verze 8.0-preview do verze 9, mějte na paměti Toto šifrování pomocí klíčů spravovaných zákazníkem byla odebrána, protože tato funkce je stále ve verzi preview. Konkrétně `EncryptionKey` vlastnosti `Index` a `SynonymMap` byly odebrány.

Pokud vaše aplikace má pevný závislost na tuto funkci, nebude možné upgradovat na verzi 9 Azure Search .NET SDK. Můžete nadále používat verze 8.0-preview. Však prosím mějte na paměti, která **nedoporučujeme používání verze preview sady SDK v aplikacích v produkčním prostředí**. Funkce ve verzi Preview jsou určené pro zkušební pouze a může změnit.

> [!NOTE]
> Pokud jste vytvořili šifrované indexy nebo synonymum mapy pomocí verze 8.0 předběžnou verzi sady SDK, které budou mít pořád povolený jejich použití a upravení jejich definicí pomocí sady SDK verze 9 nemá žádný vliv na jejich stav šifrování. Verze 9 sady SDK nebude odesílat `encryptionKey` vlastnost k rozhraní REST API a jako výsledek rozhraní REST API se nezmění stav šifrování prostředku. 

### <a name="behavioral-change-in-data-retrieval"></a>Změna chování načítání dat

Pokud používáte "dynamicky zadávaných" `Search`, `Suggest`, nebo `Get` rozhraní API, která vrátí instance typu `Document`, mějte na paměti, že nyní deserializovat prázdné pole JSON na `object[]` místo `string[]`.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití Azure Search .NET SDK, přečtěte si [.NET postupy](search-howto-dotnet-sdk.md).

Vítáme váš názor na sadě SDK. Pokud narazíte na problémy, neváhejte nás požádat o pomoc na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Pokud zjistíte chybu, můžete založit problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že jako předpona váš problém název s "[Azure Search]".

Děkujeme vám za použití Azure Search.
