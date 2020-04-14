---
title: Přidání automatického dokončování a návrhů do vyhledávacího pole
titleSuffix: Azure Cognitive Search
description: Povolte akce dotazů jako typ vyhledávání v Azure Cognitive Search vytvořením návrhy a formulováním požadavků, které automaticky doplní vyhledávací pole s hotovými výrazy nebo frázemi. Můžete také vrátit navrhované zápasy.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 8b64a583c11e794c30e1de12eb66941874a25462
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262212"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Přidání návrhů nebo automatického dokončování do aplikace Azure Cognitive Search

Tento příklad ukazuje vyhledávací pole, které podporuje chování typu hledání. Existují dvě funkce, které můžete použít společně nebo samostatně:

+ *Návrhy* generují výsledky hledání při psaní, kde každý návrh je jeden výsledek nebo vyhledávací dokument z indexu, který odpovídá tomu, co jste dosud zadali. 

+ *Automatické dokončování* generuje dotazy "dokončení" slovo nebo frázi. Místo vrácení výsledků dokončí dotaz, který pak můžete spustit a vrátit výsledky. Stejně jako u návrhů je dokončené slovo nebo fráze v dotazu založena na shodě v indexu. Služba nebude nabízet dotazy, které vrátí nulové výsledky v indexu.

Ukázkový kód ukazuje návrhy i automatické dokončování v jazyce C# i javascriptové jazykové verzi. 

Vývojáři jazyka C# mohou krokovat ASP.NET aplikace založené na MVC, která používá [azure cognitive search .NET SDK](https://aka.ms/search-sdk). Logiku pro automatické dokončování a doporučená volání dotazu naleznete v souboru HomeController.cs. 

Vývojáři JavaScriptu najdou ekvivalentní logiku dotazu v IndexJavaScript.cshtml, která zahrnuje přímá volání rozhraní [AZURE Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

Pro obě jazykové verze je prostředí front-end uživatele založeno na knihovnách [jQuery UI](https://jqueryui.com/autocomplete/) a [XDSoft.](https://xdsoft.net/jqplugins/autocomplete/) Tyto knihovny používáme k vytvoření vyhledávacího pole podporujícího návrhy i automatické dokončování. Vstupy shromážděné ve vyhledávacím poli jsou spárovány s návrhy a akcemi automatického dokončování, například s těmi, které jsou definovány v HomeController.cs nebo IndexJavaScript.cshtml.

## <a name="prerequisites"></a>Požadavky

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)

Služba Azure Cognitive Search je volitelná pro toto cvičení, protože řešení používá hostovizované služby a NYCJobs demo index. Pokud chcete vytvořit tento index na vlastní vyhledávací služby, naleznete [v tématu vytvoření nyc jobs index](#configure-app) pro pokyny. V opačném případě můžete použít existující služby a index zálohovat klientské aplikace JavaScript.

<!-- The sample is comprehensive, covering suggestions, autocomplete, faceted navigation, and client-side caching. Review the readme and comments for a full description of what the sample offers. -->

## <a name="download-files"></a>Stažení souborů

Ukázkový kód pro vývojáře jazyka C# i JavaScriptu najdete ve [složce DotNetHowToAutoComplete v](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) úložišti GitHub **Azure-Samples/search-dotnet-getting-started.**

Ukázka se zaměřuje na existující službu ukázkového vyhledávání a předem vytvořený index naplněný [ukázkovými daty NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). NycJobs index obsahuje [Konstrukce Sustrumeje](index-add-suggesters.md), což je požadavek pro použití návrhy nebo automatické dokončování.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete v sadě Visual Studio **soubor AutocompleteTutorial.sln.** Řešení obsahuje ASP.NET mvc projektu s připojením k existující vyhledávací služby a indexu.

1. Aktualizujte balíčky NuGet:

   1. V Průzkumníku řešení klepněte pravým tlačítkem myši na položku **DotNetHowToAutoComplete** a vyberte **příkaz Spravovat balíčky NuGet**.  
   1. Vyberte kartu **Aktualizace,** vyberte všechny balíčky a klepněte na **tlačítko Aktualizovat**. Přijměte všechny licenční smlouvy. K aktualizaci všech balíčků může být vyžadováno více než jeden průchod.

1. Stisknutím klávesy F5 spusťte projekt a načtěte stránku do prohlížeče.

V horní části se zobrazí možnost výběru jazyka C# nebo JavaScriptu. Možnost C# volá do HomeController z prohlížeče a používá Azure Cognitive Search .NET SDK k načtení výsledků. 

Možnost JavaScript volá rozhraní AZURE Cognitive Search REST API přímo z prohlížeče. Tato možnost bude mít obvykle výrazně lepší výkon, protože přebírá řadič z toku. Můžete si vybrat možnost, která vyhovuje vašim potřebám a upřednostňovanému jazyku. Existuje několik příkladů automatického dokončování na stránce s některými pokyny pro každý. U každého příkladu je uvedený doporučený ukázkový text, který můžete vyzkoušet.  

![Ukázková úvodní stránka](media/search-autocomplete-tutorial/startup-page.png "Ukázka úvodní stránky v localhost")

Zkuste do každého vyhledávacího pole zadat několik písmen a sledujte, co se stane.

## <a name="query-inputs"></a>Vstupy dotazů

Pro verze jazyka C# i JavaScript je implementace vyhledávacího pole naprosto stejná. 

Chcete-li zobrazit kód, otevřete soubor **Index.cshtml** ve složce \Views\Home:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Tento příklad je jednoduché vstupní textové pole s třídou pro styling, ID, na které odkazuje JavaScript, a zástupný text.  Kouzlo je ve vloženém JavaScriptu.

Ukázka jazyka C# používá JavaScript v Index.cshtml k využití [knihovny automatického dokončování jQuery UI](https://jqueryui.com/autocomplete/). Tato knihovna přidá do vyhledávacího pole možnost automatického dokončování asynchronním voláním řadiče MVC, aby bylo možné načíst návrhy. Jazyková verze JavaScriptu je v IndexJavaScript.cshtml. Obsahuje skript níže pro vyhledávací panel, stejně jako volání rozhraní REST API pro Azure Cognitive Search.

Podívejme se na kód JavaScript u prvního příkladu, který volá funkci automatického dokončování jQuery UI a předá žádost o návrhy:

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

Výše uvedený kód běží v prohlížeči na načtení stránky pro konfiguraci jQuery UI automatické dokončování pro "example1a" vstupní pole.  Parametr `minLength: 3` zajišťuje, že se doporučení zobrazí pouze v případě, že vyhledávací pole obsahuje alespoň tři znaky.  Důležitá je hodnota source (zdroj):

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Výše uvedený řádek říká funkci automatického dokončování jQuery UI, kde získat seznam položek, které se mají zobrazit pod vyhledávacím polem. Vzhledem k tomu, že tento projekt je projekt MVC, volá funkce Suggest v HomeController.cs, která obsahuje logiku pro vrácení návrhů dotazů (více o navržení v další části). Tato funkce také předá několik parametrů pro řízení zvýraznění, přibližné shody a termínu. Automatické dokončování JavaScript API přidá parametr termínu.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozšíření ukázky o podporu přibližných shod

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. I když není vyžadováno, výrazně zlepšuje robustnost prostředí typu ahead. Vyzkoušíme to a změníme řádek se zdrojem tak, aby se povolily přibližné shody.

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

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>jAutomatické dokončování dotazů podpořené automatickým dokončování Azure Cognitive Search

Zatím byl vyhledávací kód Uživatelského ux zaměřen na návrhy. Další blok kódu zobrazuje funkci automatického dokončování jQuery UI (řádek 91 v index.cshtml), která předává žádost o automatické dokončování Azure Cognitive Search:

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

## <a name="c-example"></a>Příklad jazyka C#

Teď, když jsme zkontrolovali kód JavaScriptpro webovou stránku, podívejme se na kód řadiče na straně serveru C#, který ve skutečnosti načte navrhované shody pomocí sady Azure Cognitive Search .NET SDK.

Otevřete soubor **HomeController.cs** v adresáři Řadiče. 

První věc, kterou si můžete všimnout, je `InitSearch`metoda v horní části třídy s názvem . Tato metoda vytvoří ověřený http index klienta služby Azure Cognitive Search. Další informace najdete v tématu [Jak používat Azure Cognitive Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na řádku 41 si všimněte funkce Suggest. Je založen na [DocumentsOperationsExtensions.Suggest metoda](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda. Metoda vytvoří [Objekt SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), který je pak předán suggest API. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

Na řádku 69 si všimněte funkce automatického dokončování. Je založen na [DocumentsOperationsExtensions.Autocomplete metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Funkce automatického dokončování přebírá vstup hledaného výrazu. Metoda vytvoří [objekt AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

(Volitelné) Přidejte na začátek funkce Suggest zarážku a postupně si kód projděte. Všimněte si odpovědi vrácené sadou SDK a způsobu, jakým je převedena na výsledek vrácený z metody.

Ostatní příklady na stránce postupujte podle stejného vzoru přidat zvýraznění přístupů a omezující stránky pro podporu ukládání výsledků automatického dokončování na straně klienta. Projděte si je, abyste pochopili, jak fungují a jak je můžete využít ve vlastním vyhledávání.

## <a name="javascript-example"></a>Příklad JavaScriptu

JavaScript implementace automatického dokončování a návrhy volá rozhraní REST API, pomocí URI jako zdroj pro určení indexu a operace. 

Chcete-li zkontrolovat implementaci JavaScriptu, otevřete **index IndexJavaScript.cshtml**. Všimněte si, že funkce automatického dokončování jQuery UI se také používá pro vyhledávací pole, shromažďování vstupů vyhledávacích dotazů a provádění asynchronních volání azure kognitivní hodování k načtení navržených shod nebo dokončených termínů. 

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

Pokud porovnáte tento příklad s výše uvedeným příkladem, který volá domácí řadič, všimnete si několika podobností.  Konfigurace automatického `minLength` dokončování pro a `position` jsou přesně stejné. 

Významná změna je ve zdroji. Namísto volání Metody Suggest v domácím řadiči je požadavek REST vytvořen ve funkci JavaScripta a proveden pomocí Ajaxu. Odpověď se pak zpracuje ve funkci success (úspěch) a použije se jako zdroj.

Volání REST pomocí identifikátorů URI určují, zda se provádí automatické [dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nebo volání rozhraní API [návrhů.](https://docs.microsoft.com/rest/api/searchservice/suggestions) Následující identifikátory URI jsou na řádcích 9 a 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na řádku 148 najdete skript, `autocompleteUri`který volá . První hovor `suggestUri` je na lince 39.

> [!Note]
> Volání REST ke službě v JavaScriptu je zde nabízeno jako praktická ukázka rozhraní REST API, ale nemělo by být vykládáno jako osvědčený postup nebo doporučení. Zahrnutí klíče rozhraní API a koncového bodu ve skriptu otevírá vaši službu až k útokům odmítnutí služby komukoli, kdo může číst tyto hodnoty ze skriptu. Zatímco jeho bezpečné použití JavaScriptu pro účely učení, možná na indexy hostované na bezplatné služby, doporučujeme používat Java nebo C# pro indexování a dotaz operace v produkčním kódu. 

<a name="configure-app"></a>

## <a name="create-an-nycjobs-index"></a>Vytvoření indexu NYCJobs

Až do teď, jste používali hostované NYCJobs demo index. Pokud chcete úplný přehled o všech kódu, včetně indexu, postupujte podle těchto pokynů k vytvoření a načtení indexu ve vlastní vyhledávací službě.

1. [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. V tomto příkladu můžete využít bezplatnou službu. 

   > [!Note]
   > Pokud používáte bezplatnou službu Azure Cognitive Search, jste omezeni na tři indexy. Nástroj pro načtení dat NYCJobs vytvoří dva indexy. Ujistěte se, že ve své službě máte místo pro příjem nových indexů.

1. Stáhnout ukázkový kód [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

1. Ve složce DataLoader ukázkového kódu NYCJobs otevřete **dataloader.sln** v sadě Visual Studio.

1. Přidejte informace o připojení pro službu Azure Cognitive Search. Otevřete nástroj App.config v rámci projektu DataLoader a změňte nastavení targetsearchservicename a targetsearchserviceapikey tak, aby odrážely vaši službu Azure Cognitive Search a klíč rozhraní API služby Azure Cognitive Search. Tyto informace najdete na webu Azure Portal.

1. Stisknutím klávesy F5 spusťte aplikaci, vytvořte dva indexy a importujte ukázková data NYCJob.

1. Otevřete **soubor AutocompleteTutorial.sln** a upravte soubor Web.config v projektu **AutocompleteTutorial.** Změňte hodnoty SearchServiceName a SearchServiceApiKey na hodnoty, které jsou platné pro vyhledávací službu.

1. Stisknutím klávesy F5 spusťte aplikaci. Ukázková webová aplikace se otevře ve výchozím prohlížeči. Prostředí je shodné s verzí izolovaného prostoru, pouze index a data jsou hostovány ve vaší službě.

## <a name="next-steps"></a>Další kroky

Tento příklad ukazuje základní kroky pro vytvoření vyhledávacího pole, které podporuje automatické dokončování a návrhy. Viděli jste, jak můžete vytvořit ASP.NET mvc aplikace a použít azure kognitivní vyhledávání .NET SDK nebo REST API načíst návrhy.

Jako další krok, se snaží integrovat návrhy a automatické dokončování do vyhledávání zkušenosti. Následující referenční články by měly pomoci.

> [!div class="nextstepaction"]
> [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> rest API[Návrhy REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [omezující vlastnosti atribut atribut u vytvořit index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)