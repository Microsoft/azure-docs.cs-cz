---
title: Upgrade na Azure Search .NET SDK verze 3
titleSuffix: Azure Cognitive Search
description: Migrace kódu do azure search .NET SDK verze 3 ze starších verzí. Zjistěte, co je nového a jaké změny kódu jsou povinné.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792984"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Upgrade na Azure Search .NET SDK verze 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Pokud používáte verzi 2.0-preview nebo starší azure [search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 3.

Obecnější návod sady SDK včetně příkladů naleznete v tématu [Jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 3 sady Azure Search .NET SDK obsahuje některé změny z předchozích verzí. Jedná se většinou o menší, takže změna kódu by měla vyžadovat pouze minimální úsilí. Pokyny, jak změnit kód tak, aby používal novou verzi sady SDK, najdete v tématu [Kroky pro upgrade.](#UpgradeSteps)

> [!NOTE]
> Pokud používáte verzi 1.0.2-preview nebo starší, měli byste nejprve upgradovat na verzi 1.1 a potom upgradovat na verzi 3. Pokyny najdete [v tématu Upgrade na Azure Search .NET SDK verze 1.1.](search-dotnet-sdk-migration-version-1.md)
>
> Vaše instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Verzi můžete nadále používat, pokud již není nejnovější, ale doporučujeme migrovat kód a používat nejnovější verzi. Při použití rozhraní REST API, musíte zadat verzi rozhraní API v každém požadavku prostřednictvím parametru api-version. Při použití sady .NET SDK určuje verze sady SDK, kterou používáte, odpovídající verzi rozhraní REST API. Pokud používáte starší sadu SDK, můžete pokračovat v spouštění tohoto kódu bez evidenčních změn, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co je nového ve verzi 3
Verze 3 sady Azure Search .NET SDK se zaměřuje na nejnovější obecně dostupnou verzi rozhraní AZURE Search REST API, konkrétně 2016-09-01. To umožňuje používat mnoho nových funkcí Azure Search z aplikace .NET, včetně následujících:

* [Vlastní analyzátory](https://aka.ms/customanalyzers)
* [Podpora azureblob storage](search-howto-indexing-azure-blob-storage.md) a indexeru [table storage Azure](search-howto-indexing-azure-tables.md)
* Přizpůsobení indexeru pomocí [mapování polí](search-indexer-field-mappings.md)
* Podpora eTags pro umožnění bezpečné souběžné aktualizace definic indexů, indexerů a zdrojů dat
* Podpora pro vytváření definic indexových polí deklarativně zdobením třídy modelu a použitím nové `FieldBuilder` třídy.
* Podpora pro .NET Core a .NET portable profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Nejprve aktualizujte odkaz `Microsoft.Azure.Search` NuGet pro použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Spravovat balíčky NuGet..." v sadě Visual Studio.

Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt. V závislosti na tom, jak je váš kód strukturován, může být úspěšně znovu sestavit. Pokud ano, jste připraveni jít!

Pokud se sestavení nezdaří, měla by se zobrazit chyba sestavení, jako je následující:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Dalším krokem je oprava této chyby sestavení. Podrobnosti o tom, co chybu způsobuje a jak ji opravit, najdete v tématu [Nejnovější změny ve verzi 3.](#ListOfChanges)

Může se zobrazit další sestavení upozornění týkající se zastaralé metody nebo vlastnosti. Upozornění budou obsahovat pokyny k použití namísto zastaralé funkce. Například pokud vaše aplikace `IndexingParameters.Base64EncodeKeys` používá vlastnost, měli byste dostat upozornění, že`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Jakmile opravíte všechny chyby sestavení, můžete v aplikaci provést změny a využít tak nové funkce, pokud chcete. Nové funkce sady SDK jsou podrobně popsány v části [Co je nového ve verzi 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Nejnovější změny ve verzi 3
Existuje malý počet narušující změny ve verzi 3, které mohou vyžadovat změny kódu kromě opětovnésestavení aplikace.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient návratový typ
Metoda `Indexes.GetClient` má nový návratový typ. Dříve se `SearchIndexClient`vrátil , ale `ISearchIndexClient` to to bylo změněno na verzi 2.0-preview a tato změna přenáší na verzi 3. Toto je podporovat zákazníky, `GetClient` kteří chtějí zesměšňovat `ISearchIndexClient`metodu pro testy částí vrácením mock implementace .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Můžete jej změnit na to to opravit všechny chyby sestavení:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType a další již nejsou implicitně převoditelné na řetězce
Existuje mnoho typů ve službě Azure Search .NET `ExtensibleEnum`SDK, které jsou odvozeny z . Dříve tyto typy byly všechny `string`implicitně převoditelné typu . Chyba však byla zjištěna v `Object.Equals` implementaci pro tyto třídy a oprava chyby vyžaduje zakázání tohoto implicitního převodu. Explicitní převod `string` na je stále povolen.

#### <a name="example"></a>Příklad
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

Můžete jej změnit na to to opravit všechny chyby sestavení:

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

Chyby sestavení související s metodami nebo vlastnostmi, které byly ve verzi 2.0-preview označeny jako zastaralé a následně odebrány ve verzi 3. Pokud narazíte na takové chyby, zde je návod, jak je vyřešit:

- Pokud jste používali tento `ScoringParameter(string name, string value)`konstruktor: , použijte tento:`ScoringParameter(string name, IEnumerable<string> values)`
- Pokud jste používali `ScoringParameter.Value` vlastnost, `ScoringParameter.Values` použijte vlastnost `ToString` nebo metodu.
- Pokud jste používali `SearchRequestOptions.RequestId` vlastnost, `ClientRequestId` použijte místo toho vlastnost.

### <a name="removed-preview-features"></a>Odebrané funkce náhledu

Pokud upgradujete z verze 2.0-preview na verzi 3, uvědomte si, že podpora analýzy JSON a CSV pro indexery objektů Blob byla odebrána, protože tyto funkce jsou stále ve verzi preview. Konkrétně byly odebrány `IndexingParametersExtensions` následující metody třídy:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace má pevné závislosti na těchto funkcích, nebude možné upgradovat na verzi 3 Azure Search .NET SDK. Můžete pokračovat v používání verze 2.0-preview. Mějte však na paměti, že **nedoporučujeme používat sady Preview SDK v produkčních aplikacích**. Funkce náhledu jsou pouze pro vyhodnocení a mohou se změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o používání sady Azure Search .NET SDK, podívejte [se na .NET Návod](search-howto-dotnet-sdk.md).

Uvítáme vaši zpětnou vazbu k sdk. Pokud narazíte na problémy, neváhejte a požádejte nás o pomoc na [Stack Přetečení](https://stackoverflow.com/questions/tagged/azure-search). Pokud najdete chybu, můžete podat problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Nezapomeňte předponu název problému s "[Azure Search]".

Děkujeme vám, že používáte Azure Search!
