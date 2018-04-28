---
title: Upgrade na Azure Search .NET SDK verze 3 | Microsoft Docs
description: Upgrade na Azure Search .NET SDK verze 3
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 161d22e0ff4ec4ab28107919a80ecc48cd027967
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Upgrade na Azure Search .NET SDK verze 3
Pokud používáte verzi 2.0 preview nebo starší z [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace k používání verze 3.

Další obecné návod sady SDK, včetně příkladů, najdete v tématu [jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 3 .NET SDK služby Azure Search obsahuje některé změny z předchozích verzí. Tyto jsou většinou dílčí, takže měnili kód měli vyžadovat jen minimální úsilí. V tématu [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak upravit svůj kód k použití nové verze sady SDK.

> [!NOTE]
> Pokud používáte verzi 1.0.2-preview nebo starší, byste měli upgradovat na verzi 1.1 první a pak upgradovat na verze 3. V tématu [upgrade .NET SDK služby Azure Search verze 1.1](search-dotnet-sdk-migration-version-1.md) pokyny.
>
> Instanci služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete použít verzi, když je už nejnovějšího, ale doporučujeme migraci kód a použít nejnovější verzi. Při použití rozhraní REST API, musíte zadat verze rozhraní API v každé žádosti o prostřednictvím parametr api-version. Když pomocí sady .NET SDK, určuje verzi sady SDK používáte odpovídající verzi rozhraní REST API. Pokud používáte starší SDK, můžete spustit tento kód se žádné změny, i v případě, že je služba upgradovat pro podporu na novější verzi rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co je nového v verze 3
Verze 3 .NET SDK služby Azure Search cílí na nejnovější verzi obecně k dispozici REST API Azure Search, konkrétně 2016-09-01. Díky tomu je možné použít mnoha nových funkcí služby Azure Search z aplikace .NET, včetně následujících:

* [Vlastní analyzátory](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) a [Azure Table Storage](search-howto-indexing-azure-tables.md) podpora indexeru
* Přizpůsobení indexeru prostřednictvím [pole mapování](search-indexer-field-mappings.md)
* Značky etag binárním rozsáhlým podporu pro povolení bezpečné souběžných aktualizací definic index, indexery a zdroje dat
* Podporu pro vytváření indexu pole definice deklarativně architekturu třídě modelu a použitím nové `FieldBuilder` třídy.
* Podpora pro .NET Core a přenosné profil .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Nejdřív aktualizovat vaše NuGet odkaz pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo nástrojem pravým tlačítkem myši na vaše odkazy na projekt a výběrem "Správa NuGet balíčky..." v sadě Visual Studio.

Jakmile NuGet stáhl nové balíčky a jejich závislosti, znovu sestavte projekt. V závislosti na tom, jak je strukturovaná kódu se může znovu sestavit úspěšně. Pokud ano, jste připravení přejít!

Pokud vaše sestavení selže, měli byste vidět chyby sestavení takto:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Dalším krokem je odstranění této chyby sestavení. V tématu [nejnovější změny ve verzi 3](#ListOfChanges) podrobné informace o co způsobí, že chyba a jeho řešení.

Může se zobrazit další sestavení upozornění související s zastaralé metody nebo vlastnosti. Upozornění bude obsahovat pokyny o tom, jak používat místo zastaralé funkce. Například, pokud vaše aplikace používá `IndexingParameters.Base64EncodeKeys` vlastnost, měli byste obdržet upozornění, která uvádí, že `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Jakmile vyřešili všechny chyby sestavení, vám do vaší aplikace využívat výhod nových funkcí, nechcete-li provádět změny. Nové funkce v sadě SDK jsou podrobně popsané na [co je nového ve verzi 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Nejnovější změny ve verze 3
Existuje malý počet nejnovější změny ve verzi 3, která může vyžadovat kód změní kromě nové sestavení aplikace.

### <a name="indexesgetclient-return-type"></a>Návratový typ Indexes.GetClient
`Indexes.GetClient` Má nové návratový typ. metoda. Dříve, vrátí `SearchIndexClient`, ale to byl změněn na `ISearchIndexClient` v verze 2.0-preview a že se změny přenesou do verze 3. To je podpora zákazníků, které chcete model `GetClient` metodu pro testování částí vrácením imitované implementace `ISearchIndexClient`.

#### <a name="example"></a>Příklad:
Pokud váš kód vypadá takto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Můžete ji změnit k tomuto a opravte případné chyby sestavení:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, datový typ a další jsou už implicitně převést na řetězce
Existuje mnoho typů v Azure Search .NET SDK, které pocházejí z `ExtensibleEnum`. Dříve byly všechny tyto typy implicitně převést na typ `string`. Ale byly zjištěny chyby ve `Object.Equals` implementace pro tyto třídy a oprava chyb vyžaduje zakázání této implicitní převod. Explicitní převod na `string` je stále povolený.

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

Můžete ji změnit k tomuto a opravte případné chyby sestavení:

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

Může se zobrazit chyby sestavení související s metody nebo vlastnosti, které byly označeny jako zastaralé ve verzi 2.0 preview a následně odebrat verze 3. Pokud narazíte na takové chyby, zde je způsob jejich řešení:

- Pokud používáte tento konstruktor: `ScoringParameter(string name, string value)`, použijte místo toho tato: `ScoringParameter(string name, IEnumerable<string> values)`
- Pokud jste používali `ScoringParameter.Value` vlastnosti, použijte `ScoringParameter.Values` vlastnost nebo `ToString` metoda místo.
- Pokud jste používali `SearchRequestOptions.RequestId` vlastnosti, použijte `ClientRequestId` vlastnost místo.

### <a name="removed-preview-features"></a>Funkce odebrané preview

Pokud upgradujete z verze 2.0-preview verze 3, mějte na paměti, JSON a CSV analýza podporu pro objekt Blob indexery byl odebrán vzhledem k tomu, že tyto funkce jsou stále ve verzi preview. Konkrétně tyto metody `IndexingParametersExtensions` třídy byly odebrány:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Pokud vaše aplikace obsahuje pevné závislost na těchto funkcích, nebudete moci upgradovat na verzi 3 .NET SDK služby Azure Search. Můžete nadále používat verze 2.0-preview. Ale prosím mějte na paměti, **nedoporučujeme používání náhled sady SDK v produkční aplikace**. Funkce Preview jsou pouze zkušební verze a může změnit.

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití .NET SDK služby Azure Search, přečtěte si [.NET postupy](search-howto-dotnet-sdk.md).

Uvítáme vaše názory týkající se sady SDK. Pokud narazíte na potíže, neváhejte nám požádat o pomoc na [fórum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Pokud narazíte na chyby, můžete soubor problém v [úložiště Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že předpony název vašeho problému s "[Azure Search]".

Děkujeme za používání služby Azure Search.
