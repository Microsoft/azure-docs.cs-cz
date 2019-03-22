---
title: Příklad pro přidání automatické dokončování do vyhledávacího pole – Azure Search
description: Příklady, jak ke zlepšení prostředí koncového uživatele datově orientovaných aplikací pomocí automatického dokončování Azure Search a návrhy rozhraní API.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: b754f00e9bed34717734c4aec81e5489d2c12b63
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200272"
---
# <a name="example-add-autocomplete-to-your-search-box-using-azure-search"></a>Příklad: Přidání automatické dokončování vaší vyhledávací pole s použitím Azure Search

V tomto příkladu se dozvíte víc o použití [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions), [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) a [omezující vlastnosti](search-faceted-navigation.md) v [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/) a [Sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) vytvářet výkonné vyhledávací pole. 

+ *Návrhy* poskytují doporučení skutečných výsledků na základě toho, co uživatel zatím zadal. 
+ *Automatické dokončování*, [novou funkci ve verzi preview](search-api-preview.md) ve službě Azure Search poskytuje podmínky z indexu k dokončení, co je uživatel aktuálně psát. 

Porovnáme několik technik pro zlepšení produktivity uživatelů přenesením bohatost hledání uživateli jako jejich typu.

Tento příklad vás provede aplikace založené na ASP.NET MVC, která používá C# volat [klientských knihoven Azure Search pro .NET](https://aka.ms/search-sdk)a JavaScript přímo volat rozhraní REST API Azure Search. Aplikace pro účely tohoto příkladu, zaměřuje naplní index [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ukázková data. Můžete použít už nakonfigurovaný index v ukázce NYC Jobs nebo naplnit vlastní index pomocí nástroje pro načítání dat v ukázkovém řešení NYCJobs. Ukázka používá [uživatelské rozhraní jQuery](https://jqueryui.com/autocomplete/) a [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) knihoven jazyka JavaScript pro vytváření vyhledávacího pole, která podporuje automatické dokončování. Pomocí těchto součástí spolu s Azure Search, zobrazí se vám několik příkladů poznámek o podporu automatického dokončování s automatickým dokončováním vyhledávacího pole.

Provedete následující úlohy:

> [!div class="checklist"]
> * Stažení a konfigurace řešení
> * Přidání informací o vyhledávací službě do nastavení aplikace
> * Implementace vstupního vyhledávacího pole
> * Přidání podpory pro automatické dokončování seznam, který si vyžádá ze vzdáleného zdroje 
> * Načíst návrhy a pomocí sady .NET SDK a rozhraní REST API pro automatické dokončování
> * Zvýšení výkonu prostřednictvím podpory ukládání do mezipaměti na straně klienta 

## <a name="prerequisites"></a>Požadavky

* Sada Visual Studio 2017. Můžete použít bezplatnou sadu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Stáhněte si ukázku [zdrojový kód](https://github.com/azure-samples/search-dotnet-getting-started) pro příklad.

* (Volitelné) Aktivní účet Azure a služba Azure Search. Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Nápovědu ke zřízení služby najdete v tématu [Vytvoření vyhledávací služby](search-create-service-portal.md). Účtu a služby jsou volitelné, protože v tomto příkladu je možné provést pomocí prostředí index NYCJobs již na místě pro jinou ukázku.

* (Volitelné) Stáhněte si vzorový kód [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs), který importuje data NYCJobs do indexu ve vaší službě Azure Search.

> [!Note]
> Pokud používáte bezplatnou službu Azure Search, platí pro vás omezení na tři indexy. Nástroj pro načtení dat NYCJobs vytvoří dva indexy. Ujistěte se, že ve své službě máte místo pro příjem nových indexů.

### <a name="set-up-azure-search-optional"></a>Nastavení služby Azure Search (volitelné)

Postup v této části použijte v případě, že chcete importovat data pro ukázkovou aplikaci NYCJobs do vlastního indexu. Tento krok je volitelný.  Pokud chcete použít uvedený ukázkový index, přeskočte k další části věnované spuštění ukázky.

1. V sadě Visual Studio otevřete soubor řešení DataLoader.sln ze složky DataLoader se vzorovým kódem NYCJobs.

1. Aktualizujte informace o připojení tak, aby odpovídaly vaší službě Azure Search.  Otevřete soubor App.config v projektu DataLoader a změňte nastavení aplikace TargetSearchServiceName a TargetSearchServiceApiKey tak, aby odpovídala vaší službě Azure Search a klíči rozhraní API služby Azure Search.  Tyto hodnoty najdete na webu Azure Portal.

1. Stisknutím klávesy F5 spusťte aplikaci.  Tím se vytvoří 2 indexy a naimportují se ukázková data NYCJobs.

1. V příkladu ukázky kódu otevřete soubor řešení AutocompleteTutorial.sln v sadě Visual Studio.  Otevřete soubor Web.config v projektu AutocompleteTutorial a změňte hodnoty SearchServiceName a SearchServiceApiKey na stejné hodnoty jako v předchozím kroku.

### <a name="running-the-sample"></a>Spuštění ukázky

Nyní jste připraveni ke spuštění příkladu ukázkovou aplikaci.  Otevřete soubor řešení AutocompleteTutorial.sln v sadě Visual Studio spustit v příkladu.  Řešení obsahuje projekt ASP.NET MVC.  Stisknutím klávesy F5 projekt spusťte a načtěte stránku v libovolném prohlížeči.  V horní části se zobrazí možnost výběru jazyka C# nebo JavaScriptu.  C# Možnost zavolá HomeController z prohlížeče a používá Azure Search .NET SDK k načtení výsledků.  Možnost JavaScriptu volá přímo z prohlížeče rozhraní REST API služby Azure Search.  Tato možnost se obvykle mít výrazně vyšší výkon, protože trvá kontroleru mimo tento tok.  Můžete si vybrat možnost, která vyhovuje vašim potřebám a upřednostňovanému jazyku.  Na stránce s pokyny pro každou obsahují několik příkladů automatické dokončování.  U každého příkladu je uvedený doporučený ukázkový text, který můžete vyzkoušet.  Zkuste do každého vyhledávacího pole zadat několik písmen a sledujte, co se stane.

## <a name="how-this-works-in-code"></a>Jak to funguje v kódu

Teď, když jste si prohlédli příklady v prohlížeči, si podrobně projdeme první příklad, ukážeme použité komponenty a vysvětlíme, jak fungují.

### <a name="search-box"></a>Vyhledávací pole

Vyhledávací pole je pro obě volby jazyka naprosto stejné.  Otevřete soubor Index.cshtml ve složce \Views\Home. Samotné vyhledávací pole je jednoduché:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Toto je jednoduchý vstupní textové pole s třídou pro používání stylů pro, ID, které má být odkazovány jazyka JavaScript a zástupný text.  To důležité se odehrává v JavaScriptu.

### <a name="javascript-code-c"></a>Kód JavaScriptu (C#)

Ukázka v jazyce C# prostřednictvím JavaScriptu v souboru Index.cshtml využívá knihovnu pro automatické dokončování jQuery UI Autocomplete.  Tato knihovna možnosti automatického dokončování přidá do vyhledávacího pole tím, že byla zahájena asynchronní volání pro kontroler MVC se načíst doporučení.  Podívejme se na kód JavaScriptu pro první příklad:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Tento kód se spustí v prohlížeči při načtení stránky nakonfigurovat automatické dokončování pro "example1a" vstupního pole.  Parametr `minLength: 3` zajišťuje, že se doporučení zobrazí pouze v případě, že vyhledávací pole obsahuje alespoň tři znaky.  Důležitá je hodnota source (zdroj):

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Tento řádek zjistí automatické dokončování rozhraní API, kde zobrazíte seznam položek, které chcete zobrazit pod vyhledávacím polem.  Vzhledem k tomu, že se jedná o projekt MVC, volá se funkce Suggest v souboru HomeController.cs.  Podrobněji se na to podíváme v další části.  Kromě toho se předává několik parametrů, které určují termín a jestli se má použít zvýraznění nebo přibližné shody.  Automatické dokončování rozhraní API pro JavaScript se přidá parametr termín.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozšíření ukázky o podporu přibližných shod

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově.  Vyzkoušíme to a změníme řádek se zdrojem tak, aby se povolily přibližné shody.

Změňte následující řádek:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

měli změnit na:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Stisknutím klávesy F5 aplikaci spusťte.

Zkuste napsat něco jako „execative“ a všimněte si, že výsledky vrátí „executive“, i když se nejedná o přesnou shodu se zadanými písmeny.

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Teď, když jsme si prošli kód JavaScriptu pro ukázku, se podíváme na kód kontroleru v jazyce C#, který ve skutečnosti načítá doporučení pomocí sady .NET SDK služby Azure Search.

1. Otevřete soubor HomeController.cs v adresáři Controllers. 

1. První, čeho si můžete všimnout, je metoda InitSearch na začátku třídy.  Tato metoda vytvoří ověřeného klienta indexu HTTP pro službu Azure Search.  Pokud chcete další informace o tom, jak to funguje, najdete v následujícím příkladu: [Jak používat Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Přesuňte se k funkci Suggest.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda.  Metoda vytvoří objekt SuggestParameters, který se pak předá do rozhraní API pro návrhy. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.
(Volitelné) Přidejte na začátek funkce Suggest zarážku a postupně si kód projděte.  Všimněte si odpovědi vrácené sadou SDK a jejího převodu na výsledek, který vrací metoda.

Další příklady na stránce postupují stejným způsobem pro přidání položek zvýraznění, našeptávání pro doporučení automatického dokončování a omezující vlastnosti, které zajišťují podporu ukládání do mezipaměti na straně klienta s výsledky automatického dokončování.  Projděte si je, abyste pochopili, jak fungují a jak je můžete využít ve vlastním vyhledávání.

### <a name="javascript-language-example"></a>Příklad v jazyce JavaScript

V příkladu v jazyce JavaScript kód JavaScriptu na stránce IndexJavaScript.cshtml využívá knihovnu jQuery UI Autocomplete.  Jedná se o knihovnu, která provádí většinu rutinních úloh při vytváření pěkného vyhledávacího pole a usnadňuje provádění asynchronních volání do služby Azure Search za účelem načtení doporučení.  Podívejme se na kód JavaScriptu pro první příklad:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Pokud tento kód porovnáte s předchozím příkladem, který volal kontroler Home, můžete si všimnout několika podobností.  Konfigurace automatického dokončování pro `minLength` a `position` jsou stejné.  Významná změna je ve zdroji.  Namísto volání metody navrhnout v domácí kontroleru, požadavku REST se vytvářejí ve funkci jazyka JavaScript a pomocí rozhraní Ajax.  Odpověď se pak zpracuje ve funkci success (úspěch) a použije se jako zdroj.

## <a name="takeaways"></a>Shrnutí

Tento příklad ukazuje základní kroky pro vytváření vyhledávacího pole, která podporuje automatické dokončování a návrhy.  Jste viděli, jak může vytvářet aplikace ASP.NET MVC a používat Azure Search .NET SDK nebo rozhraní REST API k načíst návrhy.

## <a name="next-steps"></a>Další postup

Integrace návrhů a automatické dokončování do prostředí pro vyhledávání.  Zvažte, jak pomocí sady .NET SDK nebo rozhraní REST API přímo vám může pomoct využití možností Azure Search uživatelům psaní tak, aby byly zvýšit produktivitu práce.

> [!div class="nextstepaction"]
> [Rozhraní REST API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Rozhraní REST API pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atribut indexu s omezujícími vlastnostmi v rozhraní REST API pro vytváření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)

