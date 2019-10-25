---
title: Upgrade na Azure Search .NET SDK verze 3
titleSuffix: Azure Cognitive Search
description: Migruje kód do sady Azure Search .NET SDK verze 3 ze starších verzí. Podívejte se, co je nového a kdy se vyžadují změny kódu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792984"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Upgrade na Azure Search .NET SDK verze 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Pokud používáte verzi 2,0-Preview nebo starší [sadu Azure Search .NET SDK](https://aka.ms/search-sdk), Tento článek vám pomůže při upgradu aplikace na použití verze 3.

Obecnější návod k sadě SDK, včetně příkladů, najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 3 sady Azure Search .NET SDK obsahuje některé změny z dřívějších verzí. Jsou to většinou méně významné, takže změna kódu by měla vyžadovat jenom minimální úsilí. Pokyny ke změně kódu pro použití nové verze sady SDK najdete v tématu [Postup upgradu](#UpgradeSteps) .

> [!NOTE]
> Pokud používáte verzi 1.0.2-Preview nebo starší, měli byste nejdřív upgradovat na verzi 1,1 a potom upgradovat na verzi 3. Pokyny najdete v tématu [upgrade na sadu Azure Search .NET SDK verze 1,1](search-dotnet-sdk-migration-version-1.md) .
>
> Vaše instance služby Azure Search podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si kód migrovali na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co je nového ve verzi 3
Verze 3 sady Azure Search .NET SDK cílí na nejnovější všeobecně dostupnou verzi Azure Search REST API, konkrétně 2016-09-01. Díky tomu je možné využít mnoho nových funkcí Azure Search z aplikace .NET, včetně následujících:

* [Vlastní analyzátory](https://aka.ms/customanalyzers)
* Podpora [azure BLOB Storage](search-howto-indexing-azure-blob-storage.md) a [Azure Table Storage](search-howto-indexing-azure-tables.md) indexeru
* Přizpůsobení indexeru prostřednictvím [mapování polí](search-indexer-field-mappings.md)
* Podpora značek ETag umožňuje bezpečný souběžnou aktualizaci definic indexů, indexerů a zdrojů dat.
* Podpora pro sestavování definic polí indexu deklarativně pomocí upravení třídy modelu a použití nové třídy `FieldBuilder`.
* Podpora pro přenosný profil .NET Core a .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejdřív aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. V závislosti na tom, jak je kód strukturovaný, se může úspěšně znovu sestavit. Pokud ano, jste připraveni!

Pokud se sestavení nepovede, měla by se zobrazit chyba sestavení podobný následujícímu:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Dalším krokem je opravit tuto chybu sestavení. Podrobnosti o tom, co způsobuje chybu a jak ji opravit, najdete v tématu zásadní [změny ve verzi 3](#ListOfChanges) .

Může se zobrazit další upozornění sestavení související s zastaralými metodami nebo vlastnostmi. Upozornění budou obsahovat pokyny k používání funkce místo zastaralé funkce. Například pokud vaše aplikace používá vlastnost `IndexingParameters.Base64EncodeKeys`, mělo by se zobrazit upozornění, které říká `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Jakmile opravíte jakékoli chyby sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete. Nové funkce v sadě SDK jsou podrobně popsané v části [co je nového ve verzi 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Přerušující změny ve verzi 3
Ve verzi 3 Existuje malý počet zásadních změn, které mohou vyžadovat změny kódu kromě opakovaného sestavování aplikace.

### <a name="indexesgetclient-return-type"></a>Indexs. GetClient – návratový typ
Metoda `Indexes.GetClient` má nový návratový typ. Dříve vrátila `SearchIndexClient`, ale ta se změnila na `ISearchIndexClient` ve verzi 2,0-Preview a tato změna se přenáší na verzi 3. To je podpora pro zákazníky, kteří chtějí napodobovat `GetClient` metodu pro testování částí, a to vrácením makety implementace `ISearchIndexClient`.

#### <a name="example"></a>Příklad:
Pokud váš kód vypadá takto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Deanalýza, datový typ a další už nejsou implicitně převoditelné na řetězce.
V sadě Azure Search .NET SDK existuje mnoho typů, které jsou odvozeny od `ExtensibleEnum`. Dříve tyto typy byly implicitně převoditelné na typ `string`. Při implementaci `Object.Equals` pro tyto třídy se ale zjistila chyba a opravuje se chyba, která vyžaduje zakázání tohoto implicitního převodu. Explicitní převod na `string` je stále povolený.

#### <a name="example"></a>Příklad:
Pokud váš kód vypadá takto:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Odebrané zastaralé členy

Můžou se zobrazit chyby sestavení související s metodami nebo vlastnostmi, které byly označené jako zastaralé ve verzi 2,0-Preview a následně odebrány ve verzi 3. Pokud dojde k takovým chybám, můžete je vyřešit:

- Pokud jste tento konstruktor používali: `ScoringParameter(string name, string value)`, použijte tento příkaz: `ScoringParameter(string name, IEnumerable<string> values)`
- Pokud jste používali vlastnost `ScoringParameter.Value`, použijte místo toho vlastnost `ScoringParameter.Values` nebo metodu `ToString`.
- Pokud jste používali vlastnost `SearchRequestOptions.RequestId`, použijte místo toho vlastnost `ClientRequestId`.

### <a name="removed-preview-features"></a>Odebrané funkce verze Preview

Pokud upgradujete z verze 2,0-Preview na verzi 3, počítejte s tím, že podpora analýzy JSON a CSV pro indexery objektů BLOB se odebrala, protože tyto funkce jsou stále ve verzi Preview. Byly odebrány konkrétně následující metody třídy `IndexingParametersExtensions`:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud je vaše aplikace pevně závislá na těchto funkcích, nebudete moct upgradovat na verzi 3 sady Azure Search .NET SDK. Můžete dál používat verzi 2,0-Preview. Mějte ale na paměti, že **v produkčních aplikacích nedoporučujeme používat sady SDK pro verze Preview**. Funkce ve verzi Preview jsou jenom pro vyhodnocení a můžou se změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, přečtěte si [článek postupy pro .NET](search-howto-dotnet-sdk.md).

Vaše názory na sadu SDK jsme uvítá. Pokud narazíte na problémy, řekněte nám, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude předponou "[Azure Search]".

Děkujeme, že používáte Azure Search!
