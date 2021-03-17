---
title: Upgrade na .NET SDK verze 10
titleSuffix: Azure Cognitive Search
description: Migruje kód do Azure Kognitivní hledání .NET SDK verze 10 ze starších verzí. Zjistěte, co je nového a kdy se vyžadují změny kódu.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002806"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade na Azure Kognitivní hledání .NET SDK verze 10

Pokud používáte verzi [.NET SDK](/dotnet/api/overview/azure/search)verze 9,0 nebo starší, Tento článek vám pomůže při upgradu aplikace na použití verze 10.

Azure Search se přejmenuje na Azure Kognitivní hledání ve verzi 10, ale obory názvů a názvy balíčků se nemění. Předchozí verze sady SDK (9,0 a starší) nadále používají dřívější název. Další informace o používání sady SDK, včetně příkladů, najdete v tématu [Jak používat Azure kognitivní hledání z aplikace .NET](search-howto-dotnet-sdk.md).

Verze 10 přináší několik funkcí a oprav chyb a přináší tak stejnou úroveň funkčnosti jako verze REST API `2019-05-06` . V případech, kdy změna přeruší existující kód, Vás provedeme [kroky potřebnými k vyřešení tohoto problému](#UpgradeSteps).

> [!NOTE]
> Pokud používáte verzi 8,0-Preview nebo starší, měli byste nejdřív upgradovat na verzi 9 a pak upgradovat na verzi 10. Pokyny najdete v tématu [upgrade na sadu Azure Search .NET SDK verze 9](search-dotnet-sdk-migration-version-9.md) .
>
> Vaše instance služby Search Service podporuje několik REST API verzí, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si kód migrovali na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Co je nového ve verzi 10
REST API cílů Azure Kognitivní hledání .NET SDK je verze 10 pro `2019-05-06` tyto aktualizace:

* Seznámení se dvěma novými dovednostmi – [podmíněná](cognitive-search-skill-conditional.md) dovednost a [dovednost překladu textu](cognitive-search-skill-text-translation.md)
* [Shapery odbornosti](cognitive-search-skill-shaper.md) byly restrukturované, aby vyhovovaly konsolidaci z vnořených kontextů. Další informace najdete v tomto [příkladu definice JSON](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts).
* Přidání dvou nových [funkcí mapování polí](search-indexer-field-mappings.md):
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* V některých případech můžou chyby a upozornění, která se zobrazují ve [stavu provádění indexeru](/rest/api/searchservice/get-indexer-status) , získat další podrobnosti, které vám pomůžou při ladění. `IndexerExecutionResult` byla aktualizována, aby odrážela toto chování.
* Jednotlivé dovednosti definované v rámci [dovednosti](cognitive-search-defining-skillset.md) lze volitelně identifikovat zadáním `name` Vlastnosti.
* `ServiceLimits` Zobrazuje omezení pro [komplexní typy](./search-howto-complex-data-types.md) a `IndexerExecutionInfo` zobrazuje relevantní omezení a kvóty indexerů.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu

1. Aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Search` použití buď konzoly Správce balíčků NuGet, nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

2. Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. 

3. Pokud se sestavení nezdaří, bude nutné opravit každou chybu sestavení. Podrobnosti o tom, jak vyřešit každou možnou chybu sestavení, naleznete v tématu zásadní [změny ve verzi 10](#ListOfChanges) .

4. Jakmile budete chtít opravit chyby nebo upozornění sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete. Nové funkce v sadě SDK jsou podrobně popsané v části [co je nového ve verzi 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Přerušující změny ve verzi 10

K dispozici je několik zásadních změn ve verzi 10, které mohou vyžadovat změny kódu navíc k opakovanému sestavování aplikace.

> [!NOTE]
> Níže uvedený seznam změn není vyčerpávající. Některé změny by nejspíš nevedly k chybám sestavení, ale budou se technicky rušit, protože přerušují binární kompatibilitu se sestaveními, která jsou závislá na dřívějších verzích sestavení Azure Kognitivní hledání .NET SDK. Důležité změny, které spadají do této kategorie, jsou uvedeny také spolu s doporučeními. Sestavte prosím aplikaci znovu při upgradu na verzi 10, abyste se vyhnuli problémům s binární kompatibilitou.

### <a name="custom-web-api-skill-definition"></a>Definice dovednosti vlastního webového rozhraní API

Definice [vlastního dovednosti webového rozhraní API](cognitive-search-custom-skill-web-api.md) byla nesprávně zadaná ve verzi 9 a starší. 

Model `WebApiSkill` určený `HttpHeaders` jako vlastnost objektu, která _obsahuje_ slovník. Vytvoření dovednosti s `WebApiSkill` vytvořeným tímto způsobem by způsobilo výjimku, protože REST API by poznamenala chybně vytvořený požadavek. Tento problém byl opraven nastavením `HttpHeaders` **vlastnosti slovníku nejvyšší úrovně** v `WebApiSkill` modelu samotného, což je považováno za platný požadavek z REST API.

Například pokud jste se dříve pokusili vytvořit instanci a `WebApiSkill` následujícím způsobem:

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

změňte na následující, aby nedošlo k chybě ověřování REST API:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Shaper dovednost umožňuje konsolidaci vnořeného kontextu.

Shaper dovednost teď může z vnořených kontextů umožňovat vstupní konsolidaci. Pro povolení této změny jsme změnili `InputFieldMappingEntry` tak, aby mohla být vytvořena instance zadáním pouze `Source` vlastnosti, nebo `SourceContext` `Inputs` vlastností a.

Pravděpodobně nebudete muset dělat žádné změny kódu; Upozorňujeme však, že je povolena pouze jedna z těchto dvou kombinací. To znamená, že:

- Vytváření je `InputFieldMappingEntry` platné pouze v případě, že je `Source` inicializována pouze inicializace.
- Vytváření `InputFieldMappingEntry` je platné pouze v případě `SourceContext` , `Inputs` že jsou inicializovány.
- Všechny ostatní kombinace týkající se těchto tří vlastností jsou neplatné.

Pokud se rozhodnete začít používat tuto novou funkci, ujistěte se, že všichni klienti jsou aktualizováni tak, aby používali verzi 10 jako první, a teprve potom provedete tuto změnu. V opačném případě existuje možnost, že aktualizace klienta (pomocí starší verze sady SDK) k Shaper dovednosti může vést k chybám ověření.

> [!NOTE]
> I když je původní `InputFieldMappingEntry` model upraven tak, aby povoloval konsolidaci z vnořených kontextů, použití je platné pouze v rámci definice Shaper dovednosti. Použití této funkce v jiných dovednostech, zatímco platné v době kompilace, bude mít za následek chybu ověřování za běhu.

## <a name="skills-can-be-identified-by-a-name"></a>Dovednosti můžete identifikovat podle názvu.

Každá dovednost v rámci dovednosti má nyní novou vlastnost `Name` , která může být inicializována v kódu pro usnadnění identifikace dovednosti. Tato možnost je volitelná – Pokud neurčíte (což je výchozí nastavení, pokud se nezměnila žádná explicitní Změna kódu), je mu přiřazen výchozí název pomocí 1 indexu dovedností v dovednosti s předponou #. Například v následující definici dovednosti (většina inicializací byla vynechána pro zkrácení):

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

`SentimentSkill` má přiřazený název `#1` , je přiřazen `WebApiSkill` `#2` `ShaperSkill` `#3` a tak dále.

Pokud se rozhodnete identifikovat dovednosti podle vlastního názvu, nezapomeňte nejdřív aktualizovat všechny instance klientů na verzi 10 sady SDK. V opačném případě existuje možnost, že klient nástroje, který používá starší verzi sady SDK `null` , může mít `Name` vlastnost dovednosti, což způsobí, že se klient vrátí k výchozímu schématu pojmenování.

## <a name="details-about-errors-and-warnings"></a>Podrobnosti o chybách a upozorněních

`ItemError` a `ItemWarning` modely, které zapouzdřují podrobnosti chyb a varování (v uvedeném pořadí), ke kterým došlo během provádění indexeru, se změnily tak, aby zahrnovaly tři nové vlastnosti s cílem pomoci při ladění indexeru. Tyto vlastnosti jsou:

- `Name`: Název zdroje, ze kterého došlo k chybě. Například může odkazovat na konkrétní dovednosti v připojené dovednosti.
- `Details`: Další podrobné informace o chybě nebo upozornění.
- `DocumentationLink`: Odkaz na Průvodce odstraňováním potíží se specifickou chybou nebo upozorněním.

> [!NOTE]
> Začali jsme strukturovat naše chyby a upozornění, aby zahrnovaly tyto užitečné podrobnosti, kdykoli je to možné. Pracujeme na tom, abychom zajistili, že všechny chyby a upozornění jsou k dispozici, ale jedná se o probíhající práci a tyto další podrobnosti nemusí být vždy naplněny.

## <a name="next-steps"></a>Další kroky

- Změny Shaper dovednosti mají největší možný dopad na nový nebo existující kód. V dalším kroku se ujistěte, že tento příklad ilustruje vstupní strukturu: [příklad definice JSON Shaper dovednost](cognitive-search-skill-shaper.md)
- Projděte si [Přehled rozšíření AI](cognitive-search-concept-intro.md).
- Vaše názory na sadu SDK jsme uvítá. Pokud narazíte na problémy, řekněte nám, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude předponou [Azure Kognitivní hledání].