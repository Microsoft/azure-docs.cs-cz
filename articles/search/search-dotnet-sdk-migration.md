---
title: Upgrade na Azure Search .NET SDK verze 3 – Azure Search
description: Migrace ze starší verze kódu do Azure Search .NET SDK verze 3. Zjistěte, co je nového a jaké změny kódu jsou povinné.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4acf609ca1f81e69babfa1a319b43e20e84a8395
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317249"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Upgrade na Azure Search .NET SDK verze 3
Pokud používáte verzi 2.0 preview nebo starší aplikace [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace použije verzi 3.

Další obecné návod sady SDK, včetně příkladů najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Azure Search .NET SDK verze 3 obsahuje některé změny z předchozích verzí. Toto jsou většinou dílčí, takže změna kódu by měla trvat pouze minimálním úsilím. Zobrazit [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak změnit váš kód k použití nové verze sady SDK.

> [!NOTE]
> Pokud používáte verzi 1.0.2-preview nebo starší, měli byste upgradovat na verzi 1.1 první a poté proveďte upgrade na verzi 3. Zobrazit [upgrade na Azure Search .NET SDK verze 1.1](search-dotnet-sdk-migration-version-1.md) pokyny.
>
> Instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete nadále používat verzi, pokud již není nejnovější, ale doporučujeme migrovat kód Refaktorovat pro použití na nejnovější verzi. Při použití rozhraní REST API, je nutné zadat verzi rozhraní API v každé žádosti prostřednictvím parametru verze api-version. Při použití sady .NET SDK, určuje verzi sady SDK, které používáte odpovídající verzi rozhraní REST API. Pokud používáte starší sada SDK, můžete nadále spouštět tento kód beze změny i v případě, že služba se upgraduje na novější verzi rozhraní API podporují.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co je nového ve verzi 3
Azure Search .NET SDK verze 3, zaměřuje na nejnovější verzi obecně dostupnou verzi REST API služby Azure Search, konkrétně 2016-09-01. Díky tomu je možné použít spoustu nových funkcí služby Azure Search z aplikace .NET, včetně následujících:

* [Vlastní analyzátory](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) a [Azure Table Storage](search-howto-indexing-azure-tables.md) nepodporuje indexery
* Přizpůsobení indexeru prostřednictvím [mapování polí](search-indexer-field-mappings.md)
* Podpora značek etag pro povolení bezpečné souběžných aktualizací definic indexu, indexery a zdroje dat
* Podpora pro vytváření definic pole indexu deklarativně upravení třídě modelu a pomocí nového `FieldBuilder` třídy.
* Podpora pro .NET Core a .NET Portable profilu 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejprve aktualizujte referenci NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo tím, že pravým tlačítkem myši na odkazy projektu a vyberte "Spravovat NuGet balíčky..." v sadě Visual Studio.

Po stažení nové balíčky a jejich závislostí NuGet znovu sestavte projekt. V závislosti na tom, jak váš kód strukturovaná ji může znovu sestavit úspěšně. Pokud ano, jste připraveni začít!

Pokud se sestavení nepovede, měli byste vidět chybu sestavení vypadat asi takto:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Chcete-li vyřešit tuto chybu sestavení je dalším krokem. Zobrazit [Rozbíjející změny ve verzi 3](#ListOfChanges) podrobné informace o co způsobuje chybu a jak ho opravit.

Může se zobrazit další upozornění související s zastaralé metody nebo vlastnosti. Upozornění bude obsahovat pokyny, jak použít namísto zastaralé funkce. Například, pokud vaše aplikace používá `IndexingParameters.Base64EncodeKeys` vlastnost, měli byste obdržet upozornění, že `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Po jste opravě případných chyb sestavení, můžete aplikace výhod nových funkcí, pokud chcete změnit. Nové funkce v sadě Windows SDK jsou podrobně popsány v [co je nového ve verzi 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Rozbíjející změny ve verzi 3
Existuje malý počet nejnovější změny ve verzi 3, které můžou vyžadovat kód změní kromě nové sestavení aplikace.

### <a name="indexesgetclient-return-type"></a>Návratový typ Indexes.GetClient
`Indexes.GetClient` Má nové návratový typ. metoda. Dříve byl vrácen `SearchIndexClient`, ale to byl změněn na `ISearchIndexClient` ve verzi 2.0-preview a že se změny přenesou na verzi 3. Toto je podporu pro zákazníky, které chcete napodobení `GetClient` metoda pro testování částí vrácením imitované implementace `ISearchIndexClient`.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Můžete ho změnit na to opravit jakékoli chyby sestavení:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, datový typ a ostatní už nejsou implicitně převeditelné na řetězce
Existuje mnoho typů v Azure Search .NET SDK, které jsou odvozeny z `ExtensibleEnum`. Dříve byly všechny tyto typy implicitně převést na typ `string`. Však byla zjištěna chyba v `Object.Equals` implementace pro tyto třídy a opravu chyb vyžaduje zakázání této implicitní převod. Explicitní převod na `string` stále povolený.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

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

Můžete ho změnit na to opravit jakékoli chyby sestavení:

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

### <a name="removed-obsolete-members"></a>Odebrat zastaralé členy

Může se zobrazit chyby sestavení související s metodami nebo vlastnostmi, které byly označeny jako zastaralé ve verzi 2.0 ve verzi preview a následně odebrané ve verzi 3. Pokud narazíte na takové chyby, tady je způsob jejich řešení:

- Pokud jste používali tento konstruktor: `ScoringParameter(string name, string value)`, místo toho použijte tohle: `ScoringParameter(string name, IEnumerable<string> values)`
- Pokud jste používali `ScoringParameter.Value` vlastnosti, použijte `ScoringParameter.Values` vlastnost nebo `ToString` metoda místo.
- Pokud jste používali `SearchRequestOptions.RequestId` vlastnosti, použijte `ClientRequestId` vlastnost místo.

### <a name="removed-preview-features"></a>Funkce odebrané ve verzi preview

Pokud provádíte upgrade z verze 2.0-preview na verzi 3, mějte na paměti, že JSON a parsování podporu pro indexování objektů Blob CSV má se odebrat, protože tyto funkce jsou stále ve verzi preview. Konkrétně tyto metody `IndexingParametersExtensions` jsme odebrali třídu:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace má pevný závislost na těchto funkcích, nebude možné upgradovat na verzi 3 Azure Search .NET SDK. Můžete nadále používat verzi 2.0-preview. Však prosím mějte na paměti, která **nedoporučujeme používání verze preview sady SDK v aplikacích v produkčním prostředí**. Funkce ve verzi Preview jsou určené pro zkušební pouze a může změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití Azure Search .NET SDK, přečtěte si [.NET postupy](search-howto-dotnet-sdk.md).

Vítáme váš názor na sadě SDK. Pokud narazíte na problémy, neváhejte nás požádat o pomoc na [fórum Azure Search na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud zjistíte chybu, můžete založit problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že jako předpona váš problém název s "[Azure Search]".

Děkujeme vám za použití Azure Search.
