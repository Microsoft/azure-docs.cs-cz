---
title: Přidat návrhy a automatické dokončování ve vyhledávacím poli – Azure Search
description: Povolení akcí typeahead dotazu ve službě Azure Search vytvořením moduly pro návrhy a formulování požadavků, které zadejte ve vyhledávacím poli s dokončené podmínky nebo fráze.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 9fb3cdd4b4b809e45180cd95b8fe930cce86826e
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498804"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Příklad: Přidat nějaké návrhy nebo automatické dokončování pro vaši aplikaci Azure Search

V tomto příkladu, další informace o použití [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) a [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vytvářet výkonné vyhledávací pole, která podporuje chování vyhledávání jako vám type.

+ *Návrhy* je seznam navrhovaných výsledky vygenerované při psaní, kde každý návrh je jeden výsledek z indexu, který odpovídá, co jste jste zatím zadali. 

+ *Automatické dokončování*, [funkce ve verzi preview](search-api-preview.md), "dokončení" slovo nebo frázi, která je aktuálně zadání uživatele. Stejně jako u návrhy, dokončené slovo nebo frázi, záleží na shodu v indexu. 

Můžete stáhnout a spustit ukázkový kód **DotNetHowToAutocomplete** k vyhodnocení, tyto funkce. Ukázkový kód, zaměřuje předem připravených index vyplní [NYCJobs ukázkových dat](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). NYCJobs index obsahuje [modulu pro návrhy konstrukce](index-add-suggesters.md), což je požadavek pro použití návrhy nebo automatické dokončování. Můžete použít připravený index hostovaný ve službě izolovaného prostoru, nebo [naplňte jimi index vlastní](#configure-app) pomocí zavaděče dat v ukázkovém řešení NYCJobs. 

**DotNetHowToAutocomplete** ukázce návrhy a automatické dokončování, v obou C# a verze jazyka JavaScript. C#Vývojáři krokovat aplikaci založené na ASP.NET MVC, která se používá [Azure Search .NET SDK](https://aka.ms/search-sdk). Logika pro automatické dokončování a volání navrhovaných dotazů najdete v souboru HomeController.cs. Vývojáře v JavaScriptu najdete ekvivalentní dotazu logiky ve IndexJavaScript.cshtml, která zahrnuje přímá volání [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Pro obě verze jazyka je na základě front-endu uživatelské prostředí [uživatelské rozhraní jQuery](https://jqueryui.com/autocomplete/) a [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) knihovny. Používáme tyto knihovny k sestavení do vyhledávacího pole podpora návrhy a automatické dokončování. Vstupy shromažďují do vyhledávacího pole jsou párovány s návrhy a automatické dokončování ve více akcí, jako jsou ty, jak jsou definovány v HomeController.cs nebo IndexJavaScript.cshtml.

V tomto cvičení vás provede následující úlohy:

> [!div class="checklist"]
> * Implementovat vstupní pole pro hledání jazyka JavaScript a vydávání požadavků na navržené shody nebo autocompleted podmínky
> * V C#, definujte v HomeController.cs návrhy a akce automatického dokončování
> * V jazyce JavaScript volejte rozhraní REST API přímo do poskytují stejné funkce

## <a name="prerequisites"></a>Požadavky

Služba Azure Search je volitelné pro toto cvičení, protože toto řešení využívá živé sandboxu služby hostování připravenou ukázku index NYCJobs. Pokud chcete spustit tento příklad na služby search, přečtěte si téma [konfigurace NYC Jobs index](#configure-app) pokyny.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), všechny edice. Ukázky kódu a instrukce byly testovány v bezplatná edice Community.

* Stáhněte si [DotNetHowToAutoComplete ukázka](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Vzorek je komplexní, mezi které patří návrhy, automatické dokončování, Fasetové navigace a ukládání do mezipaměti na straně klienta. Měli byste zkontrolovat soubor readme a komentáře pro úplný popis co nabízí vzorku.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřít **AutocompleteTutorial.sln** v sadě Visual Studio. Řešení obsahuje projekt ASP.NET MVC s připojením k indexu NYC Jobs ukázku.

2. Stisknutím klávesy F5 projekt spusťte a načtěte stránku v libovolném prohlížeči.

V horní části se zobrazí možnost výběru jazyka C# nebo JavaScriptu. C# Možnost zavolá HomeController z prohlížeče a používá Azure Search .NET SDK k načtení výsledků. 

Možnost JavaScriptu volá přímo z prohlížeče rozhraní REST API služby Azure Search. Tato možnost se obvykle mít výrazně vyšší výkon, protože trvá kontroleru mimo tento tok. Můžete si vybrat možnost, která vyhovuje vašim potřebám a upřednostňovanému jazyku. Na stránce s pokyny pro každou obsahují několik příkladů automatické dokončování. U každého příkladu je uvedený doporučený ukázkový text, který můžete vyzkoušet.  

Zkuste do každého vyhledávacího pole zadat několik písmen a sledujte, co se stane.

## <a name="search-box"></a>Vyhledávací pole

U obou C# a verze jazyka JavaScript, pole implementace hledání je přesně tatáž. 

Otevřít **Index.cshtml** souboru v rámci složky \Views\Home zobrazíte kód:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Toto je jednoduchý vstupní textové pole s třídou pro používání stylů pro, ID, které má být odkazovány jazyka JavaScript a zástupný text.  Všechno je v vložený JavaScript.

C# Jazyk Ukázka používá jazyk JavaScript v Index.cshtml využívat [knihovny automatické dokončování uživatelské rozhraní jQuery](https://jqueryui.com/autocomplete/). Tato knihovna možnosti automatického dokončování přidá do vyhledávacího pole tak, že byla zahájena asynchronní volání pro kontroler MVC načíst návrhy. Verze jazyka JavaScript, je v IndexJavaScript.cshtml. Obsahuje níže uvedený skript pro panel vyhledávání, jakož i volání rozhraní REST API do služby Azure Search.

Podívejme se na kód jazyka JavaScript v prvním příkladu, který volá jQuery funkci automatického dokončování uživatelského rozhraní a předává jí žádost pro návrhy:

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

Výše uvedený kód běží v prohlížeči při načtení stránky konfigurace uživatelské rozhraní jQuery automatické dokončování "example1a" vstupního pole.  Parametr `minLength: 3` zajišťuje, že se doporučení zobrazí pouze v případě, že vyhledávací pole obsahuje alespoň tři znaky.  Důležitá je hodnota source (zdroj):

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Řádek výše zjistí funkce automatického dokončování uživatelské rozhraní jQuery, kde zobrazíte seznam položek, které chcete zobrazit pod vyhledávacím polem. Protože se jedná o projekt MVC, volá funkci navrhnout v HomeController.cs, který obsahuje logiku pro vrácení návrhy dotazů (Další informace o navrhnout v další části). Tato funkce také předá několik parametrů stručný přehled ovládacího prvku, přibližné shody a délku smlouvy. Automatické dokončování rozhraní API pro JavaScript se přidá parametr termín.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozšíření ukázky o podporu přibližných shod

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. Přestože se nevyžaduje, výrazně zvyšuje odolnost typeahead prostředí. Vyzkoušíme to a změníme řádek se zdrojem tak, aby se povolily přibližné shody.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery automatické dokončování se opírá o Azure Search automatického dokončování

Zatím se na návrhy střed hledání kód uživatelského rozhraní. Další blok kódu demonstruje funkce automatického dokončování uživatelské rozhraní jQuery (řádku 91 v index.cshtml), předávání v požadavku pro automatické dokončování Azure Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-version"></a>C#verze

Teď, když jsme si prohlédli kód jazyka JavaScript pro webové stránky, Podívejme se C# kód kontroleru na straně serveru, který ve skutečnosti získá navrhované shody pomocí .NET SDK služby Azure Search.

Otevřít **HomeController.cs** soubor v adresáři řadiče. 

První věc, kterou jste si všimnout, je metoda v horní části třídy, nazvané `InitSearch`. Tato metoda vytvoří ověřeného klienta indexu HTTP pro službu Azure Search. Další informace najdete v tématu [použití Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na řádku 41 Všimněte si, že funkce Navrhnout. Je založen na [DocumentsOperationsExtensions.Suggest metoda](https://docs.microsoft.com/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda. Metoda vytvoří [SuggestParameters objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), který je pak předán návrh rozhraní API. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

Na řádek 69 Všimněte si, že funkce automatického dokončování. Je založen na [DocumentsOperationsExtensions.Autocomplete metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funkce automatického dokončování přijímá vstup termín vyhledávání. Metoda vytvoří [AutoCompleteParameters objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

(Volitelné) Přidejte na začátek funkce Suggest zarážku a postupně si kód projděte. Všimněte si, že odpovědi vrácené sady SDK a jak je převést na výsledek vrácený metodou.

Další příklady na stránce postupujte podle stejného vzoru přidání zvýrazňování a omezující vlastnosti pro podporu ukládání do mezipaměti na straně klienta s výsledky automatického dokončování. Projděte si je, abyste pochopili, jak fungují a jak je můžete využít ve vlastním vyhledávání.

## <a name="javascript-version-with-rest"></a>Verze jazyka JavaScript s využitím REST

Pro implementaci jazyka JavaScript, otevřete **IndexJavaScript.cshtml**. Všimněte si, že funkce automatického dokončování uživatelské rozhraní jQuery slouží také pro vyhledávací pole, shromažďování vstupů termín vyhledávání a asynchronní volání do služby Azure Search se načíst navrhované shody nebo dokončení podmínky. 

Podívejme se na kód JavaScriptu pro první příklad:

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

Pokud tento kód porovnáte s předchozím příkladem, který volal kontroler Home, můžete si všimnout několika podobností.  Konfigurace automatického dokončování pro `minLength` a `position` jsou stejné. 

Významná změna je ve zdroji. Namísto volání metody navrhnout v domácí kontroleru, požadavku REST se vytvářejí ve funkci jazyka JavaScript a pomocí rozhraní Ajax. Odpověď se pak zpracuje ve funkci success (úspěch) a použije se jako zdroj.

Volání REST pomocí identifikátorů URI můžete určit, jestli [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nebo [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) provádí volání rozhraní API. Následující identifikátory URI jsou na řádcích 9 a 10, v uvedeném pořadí.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na řádku 148 najdete skript, který volá `autocompleteUri`. První volání `suggestUri` je na řádku 39.

> [!Note]
> Volání REST pro službu v jazyce JavaScript se nabízí jako praktické předvedení rozhraní REST API, ale neměly by být vykládány jako nejlepší praxe nebo doporučení. Zahrnutí klíč rozhraní API a koncového bodu ve skriptu se otevře vaše služby až odmítnutí útoků služby každému, kdo může číst tyto hodnoty mimo tento skript. Při jeho bezpečné pomocí jazyka JavaScript pro výukové účely, třeba na indexy, které jsou hostované na bezplatné služby, doporučujeme pomocí Javy nebo C# pro operace indexování a dotazování v produkčním kódu. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurace NYCJobs ke spuštění ve službě service

Až dosud jste dosud používali hostované index NYCJobs ukázku. Pokud chcete, aby úplný přehled o veškerý kód, včetně index, postupujte podle těchto pokynů k vytvoření a načtení indexu ve vyhledávací službě.

1. [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto příkladu. 

   > [!Note]
   > Pokud používáte bezplatnou službu Azure Search, platí pro vás omezení na tři indexy. Nástroj pro načtení dat NYCJobs vytvoří dva indexy. Ujistěte se, že ve své službě máte místo pro příjem nových indexů.

1. Stáhněte si [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) ukázkový kód.

1. Ve složce DataLoader NYCJobs ukázkového kódu otevřete **DataLoader.sln** v sadě Visual Studio.

1. Přidejte informace o připojení pro vaši službu Azure Search. Otevřete soubor App.config v projektu DataLoader a změňte nastavení aplikace TargetSearchServiceName a TargetSearchServiceApiKey tak, aby odpovídala vaší službě Azure Search a klíči rozhraní API služby Azure Search. Tyto hodnoty najdete na webu Azure Portal.

1. Stiskněte klávesu F5 ke spuštění aplikace, vytváření dvou indexů a import NYCJob ukázková data.

1. Otevřít **AutocompleteTutorial.sln** a upravte soubor Web.config v **AutocompleteTutorial** projektu. Změňte hodnoty SearchServiceName a SearchServiceApiKey na hodnoty, které jsou platné pro vaši vyhledávací službu.

1. Stisknutím klávesy F5 spusťte aplikaci. Ukázková webová aplikace se otevře ve výchozím prohlížeči. Rozhraní je stejný jako verze v izolovaném prostoru, index a data jsou hostované ve službě service.

## <a name="next-steps"></a>Další postup

Tento příklad ukazuje základní kroky pro vytváření vyhledávacího pole, která podporuje automatické dokončování a návrhy. Jste viděli, jak může vytvářet aplikace ASP.NET MVC a používat Azure Search .NET SDK nebo rozhraní REST API k načíst návrhy.

V dalším kroku při integraci návrhy a automatické dokončování do prostředí pro vyhledávání. V následujících článcích odkazu by měly pomoci.

> [!div class="nextstepaction"]
> [Rozhraní REST API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Rozhraní REST API pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atribut indexu s omezujícími vlastnostmi v rozhraní REST API pro vytváření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)

