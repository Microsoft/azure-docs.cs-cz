---
title: Přidání návrhů a automatického dokončování do vyhledávacího pole – Azure Search
description: Povolte akce dotazů typeahead v Azure Search tím, že vytvoříte moduly pro návrhy a vydáte žádosti, které vyplní vyhledávací pole s dokončenými podmínkami nebo frázemi.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183289"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Přidání návrhů nebo automatického dokončování do aplikace Azure Search

V tomto článku se dozvíte, jak pomocí [návrhů](https://docs.microsoft.com/rest/api/searchservice/suggestions) a [automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vytvořit výkonné vyhledávací pole, které podporuje chování vyhledávání podle zadání.

+ *Návrhy* jsou navrhované výsledky vygenerované při psaní, kde každý z nich představuje jeden výsledek z indexu, který odpovídá tomu, co jste doposud zadali. 

+ *Automatické dokončování* "dokončí" slovo nebo frázi, kterou uživatel právě zapisuje. Místo vrácení výsledků dokončí dotaz, který pak můžete spustit pro vrácení výsledků. Stejně jako u návrhů je dokončené slovo nebo fráze v dotazu při porovnávání v indexu predikátem. Služba nebude nabízet dotazy, které vracejí žádné výsledky v indexu.

Můžete si stáhnout a spustit vzorový kód v **DotNetHowToAutocomplete** a vyhodnotit tyto funkce. Vzorový kód cílí předem sestavený index vyplněný [NYCJobs ukázkovými daty](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Index NYCJobs obsahuje [konstrukt navrhnout](index-add-suggesters.md), který je požadavkem na použití návrhů nebo automatického dokončování. Můžete použít připravený index hostovaný ve službě izolovaného prostoru (sandbox) nebo [naplnit vlastní index](#configure-app) pomocí zavaděče dat v ukázkovém řešení NYCJobs. 

Ukázka **DotNetHowToAutocomplete** předvádí návrhy i automatické dokončování, a C# to jak v jazyce JavaScript, tak ve verzích jazyka JavaScript. C#Vývojáři mohou procházet aplikaci založenou na ASP.NET MVC, která používá [sadu Azure Search .NET SDK](https://aka.ms/search-sdk). Logika pro automatické dokončování a navrhovaná volání dotazů se dá najít v souboru HomeController.cs. Vývojáři JavaScriptu budou v IndexJavaScript. cshtml najít ekvivalentní logiku dotazu, která zahrnuje Přímá volání [REST API Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Pro obě jazykové verze je prostředí front-endu koncového uživatele založené na [uživatelském rozhraní jQuery](https://jqueryui.com/autocomplete/) a knihovnách [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) . Tyto knihovny používáme k sestavení vyhledávacího pole podporujícího návrhy i automatické dokončování. Vstupy shromážděné ve vyhledávacím poli jsou spárovány s návrhy a akcemi automatického dokončování, jako jsou definovány v HomeController.cs nebo IndexJavaScript. cshtml.

Toto cvičení vás provede následujícími úlohami:

> [!div class="checklist"]
> * Implementujte vstupní pole hledání v JavaScriptu a vydejte žádosti o navrhované shody nebo výrazy AutoComplete.
> * V C#portálu definujte návrhy a akce automatického dokončování v HomeController.cs
> * V jazyce JavaScript volejte rozhraní REST API přímo pro zajištění stejné funkce

## <a name="prerequisites"></a>Požadavky

Služba Azure Search je pro toto cvičení volitelná, protože řešení používá živou službu izolovaného prostoru (sandbox), která hostuje připravený ukázkový index NYCJobs. Pokud chcete spustit tento příklad na vlastní vyhledávací službě, přečtěte si téma [Konfigurace indexu úloh NYC](#configure-app) pro pokyny.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), libovolná edice. Vzorový kód a pokyny byly testovány na bezplatnou edici Community.

* Stáhněte si [ukázku DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Ukázka je komplexní, pokrývání návrhů, automatického dokončování, omezující navigaci a ukládání do mezipaměti na straně klienta. Úplný popis toho, co ukázka nabízí, najdete v souboru Readme a komentáře.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete **AutocompleteTutorial. sln** v aplikaci Visual Studio. Řešení obsahuje projekt ASP.NET MVC s připojením k ukázkovému indexu úloh NYC.

2. Stisknutím klávesy F5 projekt spusťte a načtěte stránku v libovolném prohlížeči.

V horní části se zobrazí možnost výběru jazyka C# nebo JavaScriptu. C# Možnost volá do HomeController z prohlížeče a k načtení výsledků používá sadu Azure Search .NET SDK. 

Možnost JavaScriptu volá přímo z prohlížeče rozhraní REST API služby Azure Search. Tato možnost obvykle výrazně vylepší výkon, protože kontroler přebírá z toku. Můžete si vybrat možnost, která vyhovuje vašim potřebám a upřednostňovanému jazyku. Na stránce je několik příkladů automatického dokončování, s některými pokyny pro každý z nich. U každého příkladu je uvedený doporučený ukázkový text, který můžete vyzkoušet.  

Zkuste do každého vyhledávacího pole zadat několik písmen a sledujte, co se stane.

## <a name="search-box"></a>Vyhledávací pole

Pro i C# verze JavaScriptu je implementace vyhledávacího pole naprosto stejná. 

Otevřete soubor **index. cshtml** ve složce \Views\Home, abyste zobrazili kód:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

V tomto příkladu je jednoduché vstupní textové pole s třídou pro stylování, na ID, na které se odkazuje pomocí JavaScriptu, a zástupný text.  Magic je ve vloženém JavaScriptu.

Vzor C# jazyka používá jazyk JavaScript v souboru index. cshtml k využití [knihovny automatického dokončování uživatelského rozhraní jQuery](https://jqueryui.com/autocomplete/). Tato knihovna přidává prostředí pro automatické dokončování do vyhledávacího pole tím, že provádí asynchronní volání kontroleru MVC k načtení návrhů. Verze jazyka JavaScript je v IndexJavaScript. cshtml. Obsahuje skript níže pro panel hledání a také REST API volání Azure Search.

Pojďme se podívat na kód jazyka JavaScript prvního příkladu, který volá funkci automatického dokončování uživatelského rozhraní jQuery a předá požadavek na návrhy:

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

Výše uvedený kód běží v prohlížeči při načítání stránky a konfiguruje automatické dokončování uživatelského rozhraní jQuery pro vstupní pole "example1a".  Parametr `minLength: 3` zajišťuje, že se doporučení zobrazí pouze v případě, že vyhledávací pole obsahuje alespoň tři znaky.  Důležitá je hodnota source (zdroj):

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Výše uvedený řádek obsahuje informace o funkci automatického dokončování uživatelského rozhraní jQuery, kde můžete získat seznam položek, které se mají zobrazit v poli hledání. Vzhledem k tomu, že tento projekt je projekt MVC, volá funkci navrhnout v HomeController.cs, která obsahuje logiku pro vracení návrhů dotazů (Další informace o příkazu navrhnout v další části). Tato funkce také předá několik parametrů pro řízení světel, přibližné spárování a termínu. Rozhraní JavaScript API pro automatické dokončování přidá parametr term.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozšíření ukázky o podporu přibližných shod

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. I když to není nutné, významně zlepšuje odolnost typeaheadho prostředí. Vyzkoušíme to a změníme řádek se zdrojem tak, aby se povolily přibližné shody.

Změňte následující řádek:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

K tomuto:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Stisknutím klávesy F5 aplikaci spusťte.

Zkuste napsat něco jako „execative“ a všimněte si, že výsledky vrátí „executive“, i když se nejedná o přesnou shodu se zadanými písmeny.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Automatické dokončování pomocí Azure Searchho automatického dokončování

V tomto případě byl kód uživatelského prostředí hledání na základě návrhů na střed. Další blok kódu ukazuje funkci automatického dokončování uživatelského rozhraní jQuery (řádek 91 v indexu. cshtml), která předá žádost o Azure Search automatické dokončování:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

## <a name="c-example"></a>C#případě

Teď, když jsme zkontrolovali kód JavaScriptu webové stránky, Podívejme se na kód na straně C# serveru, který ve skutečnosti načte navrhované shody pomocí sady Azure Search .NET SDK.

Otevřete soubor **HomeController.cs** v adresáři Controllers. 

První věc, kterou si můžete všimnout, je metoda v horní části třídy s názvem `InitSearch`. Tato metoda vytvoří ověřeného klienta HTTP indexu pro službu Azure Search. Další informace najdete v tématu [použití Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na řádku 41 si všimněte funkce navrhnout. Je založen na [metodě DocumentsOperationsExtensions. navrhuje](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda. Metoda vytvoří [objekt SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), který je poté předán rozhraní API navrhnout. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

Na řádku 69 si všimněte funkce automatického dokončování. Je založen na [metodě DocumentsOperationsExtensions. AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Funkce automatického dokončování přijímá vstup hledaného termínu. Metoda vytvoří [objekt AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

(Volitelné) Přidejte na začátek funkce Suggest zarážku a postupně si kód projděte. Všimněte si odpovědi vrácené sadou SDK a způsobu jejich převodu na výsledek vrácený metodou.

Další příklady na stránce dodržují stejný vzor, aby bylo možné přidat zvýrazňování přístupů a omezující vlastnosti pro podporu ukládání do mezipaměti na straně klienta v případě výsledků automatického dokončování. Projděte si je, abyste pochopili, jak fungují a jak je můžete využít ve vlastním vyhledávání.

## <a name="javascript-example"></a>Příklad JavaScriptu

Implementace jazyka JavaScript funkcí automatického dokončování a návrhů volá REST API s použitím identifikátoru URI jako zdroje k určení indexu a operace. 

Chcete-li zkontrolovat implementaci JavaScriptu, otevřete **IndexJavaScript. cshtml**. Všimněte si, že funkce automatického dokončování uživatelského rozhraní jQuery se používá také pro vyhledávací pole, shromažďují vstupy vyhledávaného termínu a provádějí asynchronní volání Azure Search k získání navrhovaných shod nebo dokončených podmínek. 

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

Pokud tento příklad porovnáte s výše uvedeným příkladem, který volá domovský kontroler, všimnete si několik podobností.  Konfigurace automatického dokončování `minLength` pro `position` a je přesně stejná. 

Významná změna je ve zdroji. Místo volání metody navrhnout v rámci domovského kontroleru se ve funkci JavaScriptu vytvoří požadavek REST a spustí se pomocí jazyka AJAX. Odpověď se pak zpracuje ve funkci success (úspěch) a použije se jako zdroj.

Volání REST používají identifikátory URI k určení, zda je prováděno volání rozhraní API pro [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nebo [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) . Následující identifikátory URI jsou na řádcích 9 a 10 v uvedeném pořadí.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na řádku 148 můžete najít skript, který volá `autocompleteUri`. První volání `suggestUri` je na řádku 39.

> [!Note]
> Volání REST do služby v JavaScriptu se tady nabízí jako Praktická ukázka REST API, ale neměla by se vykládat jako osvědčený postup nebo doporučení. Zahrnutí klíče rozhraní API a koncového bodu do skriptu otevře vaši službu až k odepření útoků na službu komukoli, kdo může tyto hodnoty číst z tohoto skriptu. V rámci bezpečného používání JavaScriptu pro účely učení, například v indexech hostovaných na bezplatné službě, doporučujeme používat jazyk Java C# nebo pro operace indexování a dotazování v produkčním kódu. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurace NYCJobs pro spuštění ve vaší službě

Dokud teď nepoužíváte hostovaný NYCJobs demo index. Pokud chcete úplný přehled všech kódů, včetně indexu, postupujte podle těchto pokynů a vytvořte a načtěte index ve své vlastní vyhledávací službě.

1. [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. V tomto příkladu můžete použít bezplatnou službu. 

   > [!Note]
   > Pokud používáte bezplatnou službu Azure Search, platí pro vás omezení na tři indexy. Nástroj pro načtení dat NYCJobs vytvoří dva indexy. Ujistěte se, že ve své službě máte místo pro příjem nových indexů.

1. Stáhněte si vzorový kód [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) .

1. Ve složce dataload v ukázkovém kódu NYCJobs otevřete položku dataloader **. sln** v aplikaci Visual Studio.

1. Přidejte informace o připojení pro vaši službu Azure Search. Otevřete soubor App.config v projektu DataLoader a změňte nastavení aplikace TargetSearchServiceName a TargetSearchServiceApiKey tak, aby odpovídala vaší službě Azure Search a klíči rozhraní API služby Azure Search. Tyto informace najdete v Azure Portal.

1. Stisknutím klávesy F5 spusťte aplikaci, vytvořte dva indexy a importujte ukázková data NYCJob.

1. Otevřete **AutocompleteTutorial. sln** a upravte soubor Web. config v projektu **AutocompleteTutorial** . Hodnoty SearchServiceName a SearchServiceApiKey změňte na hodnoty, které jsou platné pro vaši vyhledávací službu.

1. Stisknutím klávesy F5 spusťte aplikaci. Ukázková webová aplikace se otevře ve výchozím prohlížeči. Prostředí je stejné jako verze izolovaného prostoru (sandbox), pouze index a data jsou hostovány ve vaší službě.

## <a name="next-steps"></a>Další postup

Tento příklad ukazuje základní kroky pro vytvoření vyhledávacího pole, které podporuje automatické dokončování a návrhy. Zjistili jste, jak můžete vytvořit aplikaci ASP.NET MVC a použít Azure Search .NET SDK nebo REST API k načtení návrhů.

V dalším kroku se snažíte o integraci návrhů a automatického dokončování do vyhledávání. Následující referenční články by vám měly pomáhat.

> [!div class="nextstepaction"]
> [Rozhraní REST API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Rozhraní REST API pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atribut indexu s omezujícími vlastnostmi v rozhraní REST API pro vytváření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)

