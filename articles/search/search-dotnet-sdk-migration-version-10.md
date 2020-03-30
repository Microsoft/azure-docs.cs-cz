---
title: Upgrade na Azure Cognitive Search .NET SDK verze 10
titleSuffix: Azure Cognitive Search
description: Migrace kódu do sady Azure Cognitive Search .NET SDK verze 10 ze starších verzí. Zjistěte, co je nového a jaké změny kódu jsou požadovány.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847527"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade na Azure Cognitive Search .NET SDK verze 10

Pokud používáte verzi 9.0 nebo starší azure [search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 10.

Azure Search se přejmenuje na Azure Cognitive Search ve verzi 10, ale obory názvů a názvy balíčků se nezmění. Předchozí verze sady SDK (9.0 a starší) nadále používají původní název. Další informace o používání sady SDK, včetně příkladů, naleznete v tématu [Jak používat Azure Cognitive Search z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 10 přidává několik funkcí a oprav chyb, čímž se na stejnou `2019-05-06`funkční úroveň jako nejnovější verze REST API verze . V případech, kdy změna přeruší stávající kód, provedeme vás [kroky potřebnými k vyřešení problému](#UpgradeSteps).

> [!NOTE]
> Pokud používáte verzi 8.0-preview nebo starší, měli byste nejprve upgradovat na verzi 9 a pak upgradovat na verzi 10. Pokyny najdete [v tématu Upgrade na Azure Search .NET SDK verze 9.](search-dotnet-sdk-migration-version-9.md)
>
> Instance vyhledávací služby podporuje několik verzí rozhraní REST API, včetně nejnovější. Verzi můžete nadále používat, pokud již není nejnovější, ale doporučujeme migrovat kód a používat nejnovější verzi. Při použití rozhraní REST API, musíte zadat verzi rozhraní API v každém požadavku prostřednictvím parametru api-version. Při použití sady .NET SDK určuje verze sady SDK, kterou používáte, odpovídající verzi rozhraní REST API. Pokud používáte starší sadu SDK, můžete pokračovat v spouštění tohoto kódu bez evidenčních změn, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Co je nového ve verzi 10
Verze 10 sady Azure Cognitive Search .NET SDK cílí na`2019-05-06`nejnovější obecně dostupnou verzi rozhraní REST API ( ) pomocí těchto aktualizací:

* Zavedení dvou nových dovedností - [Podmíněné dovednosti](cognitive-search-skill-conditional.md) a [dovednosti překladu textu](cognitive-search-skill-text-translation.md).
* [Vstupy dovedností Shaper](cognitive-search-skill-shaper.md) byly restrukturalizovány tak, aby vyhovovaly konsolidaci z vnořených kontextů. Další informace naleznete v tomto [příkladu definice JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Přidání dvou nových [funkcí mapování polí](search-indexer-field-mappings.md):
    - [Urlencode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* V určitých případech chyby a upozornění, které se zobrazí ve [stavu spuštění indexeru](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) může mít další podrobnosti, které pomáhají při ladění. `IndexerExecutionResult`byl aktualizován tak, aby odrážel toto chování.
* Jednotlivé dovednosti definované v rámci [sady dovedností](cognitive-search-defining-skillset.md) lze `name` volitelně identifikovat zadáním vlastnosti.
* `ServiceLimits`zobrazuje limity pro `IndexerExecutionInfo` komplexní [typy](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) a zobrazuje relevantní limity indexeru/kvóty.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu

1. Aktualizujte odkaz NuGet pro `Microsoft.Azure.Search` použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Správa balíčků NuGet...". v sadě Visual Studio.

2. Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt. 

3. Pokud se sestavení nezdaří, budete muset opravit každou chybu sestavení. Podrobnosti o řešení jednotlivých potenciálních chyb sestavení najdete v tématu [Nejnovější změny ve verzi 10.](#ListOfChanges)

4. Jakmile opravíte všechny chyby sestavení nebo upozornění, můžete v aplikaci provést změny a využít tak nové funkce, pokud chcete. Nové funkce sady SDK jsou podrobně popsány v části [Co je nového ve verzi 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Nejnovější změny ve verzi 10

Existuje několik narušující změny ve verzi 10, které mohou vyžadovat změny kódu kromě opětovnésestavení aplikace.

> [!NOTE]
> Níže uvedený seznam změn není vyčerpávající. Některé změny pravděpodobně nebude mít za následek chyby sestavení, ale jsou technicky porušení, protože porušují binární kompatibilitu s sestaveními, které závisí na starších verzích sestavení sady Azure Cognitive Search .NET SDK. Významné změny, které spadají do této kategorie jsou také uvedeny spolu s doporučeními. Při upgradu na verzi 10 znovu sestavte aplikaci, abyste se vyhnuli problémům s binární kompatibilitou.

### <a name="custom-web-api-skill-definition"></a>Vlastní definice dovedností webového rozhraní API

Definice dovednosti [vlastní webové rozhraní API](cognitive-search-custom-skill-web-api.md) byla nesprávně zadána ve verzi 9 a starší. 

Model pro `WebApiSkill` `HttpHeaders` zadané jako vlastnost objektu, který _obsahuje_ slovník. Vytvoření skillset s `WebApiSkill` vyrobeno tímto způsobem by mělo za následek výjimku, protože rozhraní REST API by za požadavek špatně vytvořené. Tento problém byl opraven vytvořením `HttpHeaders` **vlastnosti slovníku nejvyšší úrovně** na samotném `WebApiSkill` modelu - což je považováno za platný požadavek z rozhraní REST API.

Například pokud jste se dříve pokusili `WebApiSkill` vytvořit instanci takto:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

změňte jej na následující, aby se zabránilo chybě ověření z rozhraní REST API:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Dovednost Shaper umožňuje konsolidaci vnořeného kontextu

Shaper dovednost nyní může povolit konsolidaci vstupů z vnořených kontextů. Chcete-li povolit `InputFieldMappingEntry` tuto změnu, jsme upravili tak, `Source` aby bylo `SourceContext` možné `Inputs` vytvořit instanci zadáním pouze vlastnost, nebo obě vlastnosti a.

S největší pravděpodobností nebudete muset provádět žádné změny kódu; všimněte si však, že je povolena pouze jedna z těchto dvou kombinací. To znamená:

- Vytvoření `InputFieldMappingEntry` místa, `Source` kde je inicializována pouze je platný je platný.
- Vytvoření `InputFieldMappingEntry` where `SourceContext` only `Inputs` a are initialized je platný.
- Všechny ostatní kombinace zahrnující tyto tři vlastnosti jsou neplatné.

Pokud se rozhodnete začít využívat tuto novou funkci, ujistěte se, že všichni vaši klienti jsou aktualizovány tak, aby nejprve používaly verzi 10, než tuto změnu zavedou. V opačném případě existuje možnost, že aktualizace klientem (pomocí starší verze sady SDK) na dovednosti Shaper může mít za následek chyby ověření.

> [!NOTE]
> I když `InputFieldMappingEntry` podkladový model byl upraven tak, aby konsolidace z vnořených kontextů, jeho použití je platný pouze v rámci definice Shaper dovednosti. Použití této funkce v jiných dovednostech, zatímco platí v době kompilace, bude mít za následek chybu ověření za běhu.

## <a name="skills-can-be-identified-by-a-name"></a>Dovednosti lze identifikovat pod názvem

Každá dovednost v rámci sady dovedností má nyní novou vlastnost `Name`, kterou lze inicializovat v kódu, aby se dovednosti pomohly identifikovat. To je volitelné - pokud není zadáno (což je výchozí hodnota, pokud nebyla provedena žádná explicitní změna kódu), je mu přiřazen výchozí název pomocí indexu dovednosti založeného na 1 v sadě dovedností, předponou znakem #. Například v následující definici sady dovedností (většina inicializace přeskočena pro stručnost):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`je přiřazen `#1`název `WebApiSkill` , `#2` `ShaperSkill` je `#3` přiřazen , je přiřazen a tak dále.

Pokud se rozhodnete identifikovat dovednosti podle vlastního názvu, nezapomeňte nejprve aktualizovat všechny instance klientů na verzi 10 sady SDK. V opačném případě existuje možnost, že klient pomocí starší `null` verze `Name` sady SDK může z funkce dovednosti, což způsobí, že klient ustoupí od výchozího schématu pojmenování.

## <a name="details-about-errors-and-warnings"></a>Podrobnosti o chybách a upozorněních

`ItemError`a `ItemWarning` modely, které zapouzdřují podrobnosti o chybách a upozorněních (v uvedeném pořadí), ke kterým dochází během provádění indexeru, byly upraveny tak, aby zahrnovaly tři nové vlastnosti s cílem pomoci při ladění indexeru. Tyto vlastnosti jsou:

- `Name`: Název zdroje, ve kterém chyba pochází. Může například odkazovat na určitou dovednost v připojené sadě dovedností.
- `Details`: Další podrobné podrobnosti o chybě nebo upozornění.
- `DocumentationLink`: Odkaz na průvodce odstraňováním potíží pro konkrétní chybu nebo upozornění.

> [!NOTE]
> Začali jsme strukturovat naše chyby a varování tak, aby obsahovaly tyto užitečné podrobnosti, kdykoli je to možné. Pracujeme na tom, aby chomáč všech chyb a upozornění tyto podrobnosti jsou k dispozici, ale je to nedokončená práce a tyto další podrobnosti nemusí být vždy naplněny.

## <a name="next-steps"></a>Další kroky

- Změny dovednosti Shaper mají největší potenciální dopad na nový nebo existující kód. Jako další krok, ujistěte se, že znovu tento příklad ilustrující vstupní strukturu: [Shaper skill JSON definice příklad](cognitive-search-skill-shaper.md)
- Projděte si [přehled obohacení ai](cognitive-search-concept-intro.md).
- Uvítáme vaši zpětnou vazbu k sdk. Pokud narazíte na problémy, neváhejte a požádejte nás o pomoc na [Stack Přetečení](https://stackoverflow.com/questions/tagged/azure-search). Pokud najdete chybu, můžete podat problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Nezapomeňte předponu název problému s "[Azure Cognitive Search]".

