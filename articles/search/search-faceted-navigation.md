---
title: Přidání hierarchie fazetovaných navigačních kategorií
titleSuffix: Azure Cognitive Search
description: Přidejte fazetovou navigaci pro filtrování s vlastním nastavením ve vyhledávacích aplikacích, které se integrují s Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f4435ca213584fff84f3ddad9bda6f7e06628a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283157"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Jak implementovat fazetované navigace v Azure Cognitive Search

Fazetová navigace je mechanismus filtrování, který poskytuje navigaci pro přechod k podrobnostem s vlastním nastavením ve vyhledávacích aplikacích. Termín "fazetovaná navigace" může být neznámý, ale pravděpodobně jste jej použili dříve. Jak ukazuje následující příklad, fazetovaná navigace není nic jiného než kategorie použité k filtrování výsledků.

 ![Ukázka portálu úloh azure kognitivního hledání](media/search-faceted-navigation/azure-search-faceting-example.png "Ukázka portálu úloh azure kognitivního hledání")

Fazetovaná navigace je alternativní vstupní bod pro vyhledávání. Nabízí vhodnou alternativu k ručnímu psaní složitých vyhledávacích výrazů. Omezující aspekty vám mohou pomoci najít to, co hledáte, a zároveň zajistit, že nezískáte nulové výsledky. Jako vývojář vám omezující aspekty umožňují zpřístupnit nejužitečnější kritéria vyhledávání pro navigaci v indexu vyhledávání. V online maloobchodních aplikacích je fazetovaná navigace často postavena na značkách, odděleních (dětské boty), velikosti, ceně, popularitě a hodnocení. 

Implementace fazetované navigace se liší mezi vyhledávacími technologiemi. V Azure Cognitive Search fazetované navigace je postavena v době dotazu, pomocí polí, které jste dříve připsat ve schématu.

-   V dotazech, které vaše aplikace vytvoří, dotaz musí *odeslat parametry omezující vlastnosti dotazu* získat dostupné hodnoty filtru omezující vlastnosti pro tuto sadu výsledků dokumentu.

-   Chcete-li skutečně oříznout sadu výsledků dokumentu, aplikace musí také použít `$filter` výraz.

Ve vývoji aplikace psaní kódu, který vytváří dotazy představuje většinu práce. Mnoho chování aplikace, které byste očekávali od fazetované navigace jsou poskytovány službou, včetně integrované podpory pro definování rozsahů a získání počítá pro omezující fazety výsledky. Služba také obsahuje rozumné výchozí hodnoty, které vám pomohou vyhnout se těžkopádným navigačním strukturám. 

## <a name="sample-code-and-demo"></a>Ukázkový kód a ukázka
Tento článek používá jako příklad portál hledání zaměstnání. Příklad je implementován jako ASP.NET aplikace MVC.

- Podívejte se a otestujte pracovní ukázku online na [ukázce portálu úloh azure kognitivního hledání](https://aka.ms/azjobsdemo).

- Stáhněte si kód z [úložiště ukázek Azure na GitHubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Začínáme
Pokud jste novým vývojem vyhledávání, nejlepší způsob, jak přemýšlet o fazetované navigaci, je, že zobrazuje možnosti pro samořízené vyhledávání. Jedná se o typ vyhledávání v přechodech k podrobnostem založený na předdefinovaných filtrech, který se používá k rychlému zúžení výsledků hledání prostřednictvím akcí typu point-and-click. 

### <a name="interaction-model"></a>Model interakce

Prostředí pro vyhledávání fazetované navigace je iterativní, takže začněme tím, že ji porozumíme jako posloupnost dotazů, které se odvíjejí v reakci na akce uživatele.

Výchozím bodem je stránka aplikace, která poskytuje fazetovou navigaci, obvykle umístěnou na periferii. Fazetovaná navigace je často stromová struktura se zaškrtávacími políčky pro každou hodnotu nebo text, na který lze kliknout. 

1. Dotaz odeslaný do Azure Cognitive Search určuje fazetovou navigační strukturu prostřednictvím jednoho nebo více parametrů omezujícího parametru dotazu. Dotaz může například `facet=Rating`obsahovat , `:values` `:sort` například s možností nebo další upřesnění prezentace.
2. Prezentační vrstva vykreslí vyhledávací stránku, která poskytuje fazetovou navigaci, pomocí omezujících vlastností určených v požadavku.
3. Vzhledem k tomu, fazetované navigační struktury, která zahrnuje hodnocení, kliknete na tlačítko "4", které označuje, že by měly být zobrazeny pouze produkty s hodnocením 4 nebo vyšší. 
4. V odpovědi aplikace odešle dotaz, který obsahuje`$filter=Rating ge 4` 
5. Prezentační vrstva aktualizuje stránku se sníženou sadou výsledků, která obsahuje pouze ty položky, které splňují nová kritéria (v tomto případě produkty s hodnocením 4 a více).

Omezující_ je parametr dotazu, ale nezaměňujte jej se vstupem dotazu. Nikdy se nepoužívá jako kritéria výběru v dotazu. Místo toho si představte parametry omezující události dotazu jako vstupy do navigační struktury, která se vrátí v odpovědi. Pro každý parametr omezující ho dotazu, který zadáte, Azure Cognitive Search vyhodnotí, kolik dokumentů jsou v částečné výsledky pro každou hodnotu omezující chod.

Všimněte `$filter` si kroku 4. Filtr je důležitým aspektem fazetované navigace. Přestože omezující aspekty a filtry jsou nezávislé v rozhraní API, je třeba jak poskytovat prostředí, které máte v úmyslu. 

### <a name="app-design-pattern"></a>Vzor návrhu aplikace

V kódu aplikace je vzorem použití parametrů dotazu omezující ho fazety k vrácení fazetované navigační struktury spolu s výsledky omezující choda, plus $filter výraz.  Výraz filtru zpracovává událost kliknutí na hodnotě omezující vlastnost. Představte `$filter` si výraz jako kód za skutečné oříznutí výsledků hledání vrácených do prezentační vrstvy. Vzhledem k tomu, barvy omezující název, `$filter` kliknutím na barvu červená je implementována prostřednictvím výrazu, který vybere pouze ty položky, které mají barvu červené. 

### <a name="query-basics"></a>Základy dotazu

V Azure Cognitive Search je požadavek určen prostřednictvím jednoho nebo více parametrů dotazu (popis každého z nich najdete v tématu [Hledání dokumentů).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) Žádný z parametrů dotazu nejsou povinné, ale musíte mít alespoň jeden, aby byl dotaz platný.

Přesnost, chápaná jako schopnost odfiltrovat irelevantní přístupy, je dosažena jedním nebo oběma těmito výrazy:

-   **hledat=**  
    Hodnota tohoto parametru představuje hledaný výraz. Může se jedná o jeden text nebo složitý vyhledávací výraz, který obsahuje více termínů a operátorů. Na serveru se vyhledávací výraz používá pro fulltextové vyhledávání, dotazování prohledávatelných polí v indexu pro odpovídající termíny, vrácení výsledků v pořadí. Pokud nastavíte `search` hodnotu null, spuštění dotazu `search=*`je přes celý index (to znamená). V tomto případě jsou další prvky `$filter` dotazu, například profil hodnocení nebo `($filter`profil hodnocení, primárními faktory ovlivňujícími, které dokumenty jsou vráceny ) a v jakém pořadí (`scoringProfile` nebo `$orderby`).

-   **$filter=**  
    Filtr je výkonný mechanismus pro omezení velikosti výsledků hledání na základě hodnot určitých atributů dokumentu. A `$filter` je vyhodnocena jako první, následovaná fazetovou logikou, která generuje dostupné hodnoty a odpovídající počty pro každou hodnotu

Složité vyhledávací výrazy snižují výkon dotazu. Pokud je to možné, použijte dobře konstruované výrazy filtru ke zvýšení přesnosti a zvýšení výkonu dotazu.

Chcete-li lépe pochopit, jak filtr přidává větší přesnost, porovnejte složitý vyhledávací výraz s výrazem, který obsahuje výraz filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Oba dotazy jsou platné, ale druhý je lepší, pokud hledáte non-motely s parkováním v Seattlu.
-   První dotaz závisí na konkrétních slovech, která jsou uvedena nebo nejsou uvedena v řetězcových polích, jako je Název, Popis a všechna ostatní pole obsahující prohledávatelná data.
-   Druhý dotaz vyhledá přesné shody na strukturovaných dat a je pravděpodobné, že bude mnohem přesnější.

V aplikacích, které obsahují fazetované navigace, ujistěte se, že každá akce uživatele přes fazetované navigační struktury je doprovázena zúžení výsledků hledání. Chcete-li výsledky zúžit, použijte výraz filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Vytvoření fazetované navigační aplikace
Implementovat fazetované navigace s Azure Cognitive Search ve vašem kódu aplikace, která vytváří požadavek na hledání. Fazetovaná navigace závisí na prvcích ve schématu, které jste definovali dříve.

Předdefinovaný v indexu vyhledávání `Facetable [true|false]` je atribut rejstříku nastavený na vybraná pole, který umožňuje nebo zakáže jejich použití ve fasetové navigační struktuře. Bez `"Facetable" = true`, pole nelze použít v omezující fazet navigace.

Prezentační vrstva v kódu poskytuje uživatelské prostředí. Měl by v něm být uveden seznam součástí fazetované navigace, jako je popisek, hodnoty, zaškrtávací políčka a počet. Rozhraní REST azure cognitive search je agnostik platformy, takže použijte jakýkoli jazyk a platformu, které chcete. Důležité je zahrnout prvky uživatelského rozhraní, které podporují přírůstkovou aktualizaci s aktualizovaným stavem uživatelského rozhraní, protože je vybrána každá další omezující plocha. 

V době dotazu kód aplikace vytvoří `facet=[string]`požadavek, který zahrnuje parametr požadavku, který poskytuje pole omezující cenu. Dotaz může mít více omezujících `&facet=color&facet=category&facet=rating`ploch, například , každý z nich oddělený znakem ampersand (&).

Kód aplikace musí `$filter` také vytvořit výraz pro zpracování událostí kliknutí v famitové navigaci. A `$filter` snižuje výsledky hledání, pomocí omezující vlastnost hodnota jako kritéria filtru.

Azure Cognitive Search vrátí výsledky hledání na základě jednoho nebo více termínů, které zadáte, spolu s aktualizacemi fazetované navigační struktury. V Azure Cognitive Search fazetované navigace je jednoúrovňová konstrukce s omezující mise hodnoty a počty, kolik výsledků se nacházejí pro každý z nich.

V následujících částech se blíže podíváme na to, jak jednotlivé díly vytvořit.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Sestavení indexu
Fazetování je povoleno v indexu podle pole podle pole prostřednictvím tohoto atributu indexu: `"Facetable": true`.  
Všechny typy polí, které by mohly `Facetable` být použity v fazetované navigace jsou ve výchozím nastavení. Tyto typy `Edm.String`polí `Edm.DateTimeOffset`zahrnují , a všechny typy číselných polí (v podstatě všechny typy polí jsou facetable s výjimkou `Edm.GeographyPoint`, které nelze použít v fazetované navigaci). 

Při vytváření indexu, osvědčeným postupem pro fazetované navigace je explicitně vypnout omezující cenu pro pole, která by nikdy neměla být použita jako omezující položka.  Zejména řetězcová pole pro hodnoty singleton, jako je například ID nebo název produktu, by měla být nastavena tak, aby `"Facetable": false` se zabránilo jejich náhodnému (a neúčinnému) použití v fasetové navigaci. Vypnutí tváří tam, kde nepotřebujete, pomáhá zachovat velikost indexu malou a obvykle zlepšuje výkon.

Následuje část schématu ukázkové aplikace Job Portal, oříznuté z některých atributů, aby se zmenšila velikost:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Jak můžete vidět ve vzorovém `Facetable` schématu, je vypnutý pro pole řetězce, které by neměly být použity jako omezující okolnosti, jako jsou například hodnoty ID. Vypnutí tváří tam, kde nepotřebujete, pomáhá zachovat velikost indexu malou a obvykle zlepšuje výkon.

> [!TIP]
> Jako osvědčený postup zahrňte úplnou sadu atributů indexu pro každé pole. Ačkoli `Facetable` je ve výchozím nastavení pro téměř všechna pole zapnuto, záměrné nastavení každého atributu vám může pomoci promyslet důsledky každého rozhodnutí schématu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Zkontrolujte data
Kvalita dat má přímý vliv na to, zda se fazetovaná navigační struktura zhmotní tak, jak očekáváte. To také ovlivňuje snadnost vytváření filtrů ke snížení sady výsledků.

Pokud chcete omezující vlastnost podle značky nebo ceny, každý dokument by měl obsahovat hodnoty *pro BrandName* a *ProductPrice,* které jsou platné, konzistentní a produktivní jako možnost filtru.

Zde je několik připomínek, co křoviny pro:

* Pro každé pole, které chcete omezující podmínky, zeptejte se sami sebe, zda obsahuje hodnoty, které jsou vhodné jako filtry v samořízenévyhledávání. Hodnoty by měly být krátké, popisné a dostatečně výrazné, aby nabízely jasnou volbu mezi konkurenčními možnostmi.
* Překlepy nebo téměř odpovídající hodnoty. Pokud omezující jste v barvě a hodnoty polí zahrnují oranžovo-orál (překlep), omezující fazeta založená na poli Barva by zachytila obojí.
* Smíšený text případu může také způsobit zmatek v fazetované navigaci, přičemž oranžová a oranžová se zobrazují jako dvě různé hodnoty. 
* Jednoduché a množné číslo verze stejné hodnoty může mít za následek samostatný omezující cenu pro každý.

Jak si dokážete představit, pečlivost při přípravě dat je základním aspektem efektivní fazetované navigace.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní
Při zpětné matné práci z prezentační vrstvy můžete odhalit požadavky, které by jinak mohly chybět, a pochopit, které funkce jsou pro vyhledávání nezbytné.

Pokud jde o fazetované navigace, vaše webová stránka nebo stránka aplikace zobrazí fazetované navigační struktury, detekuje vstup uživatele na stránce a vloží změněné prvky. 

Pro webové aplikace ajax se běžně používá v prezentační vrstvě, protože umožňuje aktualizovat přírůstkové změny. Můžete také použít ASP.NET MVC nebo jiné vizualizační platformy, které se můžete připojit ke službě Azure Cognitive Search přes HTTP. Ukázková aplikace odkazovaná v tomto článku – **ukáznutí portálu úloh azure kognitivního hledání** – je ASP.NET aplikace MVC.

V ukázce je fazetovaná navigace integrována na stránce s výsledky hledání. Následující příklad převzatý `index.cshtml` ze souboru ukázkové aplikace zobrazuje statickou strukturu HTML pro zobrazení fazetované navigace na stránce s výsledky hledání. Seznam omezujících vlastností je vytvořen nebo dynamicky znovu sestaven při odeslání hledaného výrazu nebo při výběru nebo vymazání omezující vlastnosti.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Následující fragment kódu ze `index.cshtml` stránky dynamicky vytvoří HTML pro zobrazení první omezující vlastnost, obchodní název. Podobné funkce dynamicky vytvářejí HTML pro ostatní omezující vlastnosti. Každá omezující položka má popisek a počet, který zobrazuje počet položek nalezených pro tento výsledek omezující sosti.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Při návrhu stránky s výsledky hledání nezapomeňte přidat mechanismus pro vymazání omezujících fasejí. Pokud přidáte zaškrtávací políčka, můžete snadno zjistit, jak odstranit filtry. U jiných rozložení můžete potřebovat vzorek s popisem cesty nebo jiný přístup kreativy. Například v ukázkové aplikaci Portál hledání `[X]` úloh můžete klepnutím na za vybranou omezující položku vymazat omezující položku.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Sestavení dotazu
Kód, který píšete pro vytváření dotazů, by měl určit všechny části platného dotazu, včetně vyhledávacích výrazů, omezujících okolností, filtrů, profilů hodnocení – cokoli, co se používá k formulování požadavku. V této části zkoumáme, kde omezující okolnosti se vejdou do dotazu a jak se filtry používají s omezujícími stránkami k doručení sady snížených výsledků.

Všimněte si, že omezující strany jsou nedílnou součástí této ukázkové aplikace. Prostředí pro vyhledávání na ukázce portálu úloh je navrženo tak, aby se pohybovalo kolem fazetované navigace a filtrů. Prominentní umístění fazetované navigace na stránce demonstruje její důležitost. 

Příkladem je často dobré místo pro začátek. Následující příklad převzatý `JobsSearch.cs` ze souboru vytvoří požadavek, který vytvoří fazetovou navigaci na základě obchodní ho titulu, umístění, typu účtování a minimální mzdy. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr dotazu omezující fazety je nastaven na pole a v závislosti na datovém typu `count:<integer>`může `sort:<>` `interval:<integer>`být `values:<list>`dále parametrizován seznamem odděleným čárkami, který zahrnuje , , , a . Seznam hodnot je podporován pro číselná data při nastavování rozsahů. Podrobnosti o využití najdete [v tématu Search Documents (Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Spolu s omezující vlastnosti, požadavek formulovaný vaší aplikací by měl také vytvořit filtry zúžit sadu kandidátských dokumentů na základě výběru omezující vlastnost i hodnota. Pro obchod s koly, fazetované navigace poskytuje vodítka k otázkám, jako *jaké barvy, výrobci a typy kol jsou k dispozici?*. Filtrování odpovědi na otázky, jako *které přesné kola jsou červené, horská kola, v této cenové kategorii?*. Po klepnutí na tlačítko "Červená" označíte, že by `$filter=Color eq 'Red'`měly být zobrazeny pouze červené produkty, další dotaz, který aplikace odešle, zahrnuje .

Následující fragment kódu ze `JobsSearch.cs` stránky přidá vybranou obchodní hlavu do filtru, pokud vyberete hodnotu z omezující vlastnosti Obchodní hlava.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="indexing-tips"></a>Tipy pro indexování
**Zvýšení efektivity indexu, pokud nepoužíváte vyhledávací pole**

Pokud vaše aplikace používá fazetované navigace výhradně (to znamená, `searchable=false`že `facetable=true` žádné vyhledávací pole), můžete označit pole jako , k vytvoření kompaktnější index. Kromě toho indexování dochází pouze u celé omezující události hodnoty, bez word-break nebo indexování součástí víceslovné hodnoty.

**Určení polí, která lze použít jako omezující podmínky**

Připomeňme, že schéma indexu určuje, která pole jsou k dispozici pro použití jako omezující okolnost. Za předpokladu, že pole je facetable, dotaz určuje, která pole omezující. Pole, podle kterého se sací strany poskytuje hodnoty, které se zobrazí pod popiskem. 

Hodnoty, které se zobrazí pod každý popisek jsou načteny z indexu. Pokud je například pole omezující *vlastnosti Barva*, hodnoty dostupné pro další filtrování jsou hodnoty pro toto pole – červená, černá atd.

Pouze pro číselné hodnoty a hodnoty DateTime můžete explicitně nastavit `facet=Rating,values:1|2|3|4|5`hodnoty v poli omezující sazeje (například ). Pro tyto typy polí je povolen seznam hodnot, který zjednodušuje oddělení omezujících ploch výsledků do souvislých oblastí (rozsahy založené na číselných hodnotách nebo časových obdobích). 

**Ve výchozím nastavení můžete mít pouze jednu úroveň fazetované navigace** 

Jak již bylo uvedeno, neexistuje žádná přímá podpora pro vnoření omezující chod v hierarchii. Ve výchozím nastavení fazetovaná navigace v Azure Cognitive Search podporuje jenom jednu úroveň filtrů. Existují však zástupná řešení. Hierarchickou strukturu omezujících položek můžete `Collection(Edm.String)` zakódovat v jedné vstupní oblasti na hierarchii. Implementace tohoto zástupného řešení je nad rámec tohoto článku. 

### <a name="querying-tips"></a>Tipy pro dotazování
**Ověřit pole**

Pokud vytváříte seznam omezujících vlastností dynamicky na základě nedůvěryhodného uživatelského vstupu, ověřte, zda jsou názvy fazetovaných polí platné. Nebo uniknout názvy při vytváření adres `Uri.EscapeDataString()` URL pomocí buď v .NET nebo ekvivalent ve vaší platformě volby.

### <a name="filtering-tips"></a>Tipy pro filtrování
**Zvýšení přesnosti vyhledávání pomocí filtrů**

Používejte filtry. Pokud spoléháte pouze na hledané výrazy, může vyplývající způsobit vrácení dokumentu, který nemá přesnou hodnotu omezující vlastnosti v žádném z jeho polí.

**Zvýšení výkonu vyhledávání pomocí filtrů**

Filtry zúžit sadu kandidátských dokumentů pro vyhledávání a vyloučit je z pořadí. Pokud máte velkou sadu dokumentů, použití selektivní omezující vlastnost kpodrobností často poskytuje lepší výkon.
  
**Filtrování pouze fazetovaných polí**

V podavač přechodu k podrobnostem obvykle chcete zahrnout pouze dokumenty, které mají hodnotu omezující spád v určitém (fazetovaném) poli, nikoli nikde ve všech prohledávatelných polích. Přidání filtru posílí cílové pole přesměrováním služby tak, aby hledala odpovídající hodnotu pouze ve fazetovaném poli.

**Oříznutí omezujících ploch s více filtry**

Výsledky omezující chodjsou dokumenty nalezené ve výsledcích hledání, které odpovídají výrazu omezující chod. V následujícím příkladu ve výsledcích hledání *pro cloud computing*má 254 položek také interní *specifikaci* jako typ obsahu. Položky se nemusí nutně vzájemně vylučovat. Pokud položka splňuje kritéria obou filtrů, počítá se v každém z nich. Tato duplikace je možná `Collection(Edm.String)` při fazetování na polích, které se často používají k implementaci označování dokumentů.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Obecně platí, že pokud zjistíte, že omezující možnost výsledky jsou konzistentně příliš velké, doporučujeme přidat další filtry poskytnout uživatelům více možností pro zúžení hledání.

### <a name="tips-about-result-count"></a>Tipy pro počet výsledků

**Omezení počtu položek v navigaci omezujících položek**

Pro každé fazetované pole v navigačním stromu je výchozí limit 10 hodnot. Toto výchozí nastavení má smysl pro navigační struktury, protože udržuje seznam hodnot na zvládnutelnou velikost. Výchozí hodnotu můžete přepsat přiřazením hodnoty k počítání.

* `&facet=city,count:5`určuje, že pouze prvních pět měst nalezených v nejvyšších hodnocených výsledcích je vráceno jako omezující výsledek. Zvažte ukázkový dotaz s hledaným výrazem "letiště" a 32 shodami. Pokud dotaz určuje `&facet=city,count:5`, pouze prvních pět jedinečných měst s nejvíce dokumentů ve výsledcích hledání jsou zahrnuty ve výsledcích omezující vlastnost.

Všimněte si rozdílu mezi omezující mise výsledky a výsledky hledání. Výsledky hledání jsou všechny dokumenty, které odpovídají dotazu. Omezující fazeta výsledky jsou shody pro každou hodnotu omezující chod. V příkladu zahrnují výsledky hledání názvy měst, které nejsou v seznamu klasifikace omezujícívlastností (5 v našem příkladu). Výsledky, které jsou odfiltrovány prostřednictvím fazetované navigace, se zobrazí, když vymažete omezující vlastnosti nebo zvolíte jiné omezující vlastnosti kromě města. 

> [!NOTE]
> Diskuse `count` o tom, kdy existuje více než jeden typ může být matoucí. Následující tabulka nabízí stručný přehled o tom, jak se termín používá v rozhraní API Azure cognitive search, ukázkový kód a dokumentaci. 

* `@colorFacet.count`<br/>
  V kódu prezentace byste měli vidět parametr count na omezující ploše, který se používá k zobrazení počtu výsledků omezující chod. Ve výsledcích omezující chod označuje počet dokumentů, které odpovídají termínu omezující složky nebo rozsahu.
* `&facet=City,count:12`<br/>
  V fazetovém dotazu můžete nastavit počet na hodnotu.  Výchozí hodnota je 10, ale můžete ji nastavit výš nebo níž. Nastavení `count:12` získá prvních 12 shod ve výsledcích omezující vlastnost podle počtu dokumentů.
* "`@odata.count`"<br/>
  V odpovědi na dotaz tato hodnota označuje počet odpovídajících položek ve výsledcích hledání. V průměru je větší než součet všech omezujících položek v kombinaci, vzhledem k přítomnosti položek, které odpovídají hledaný termín, ale nemají žádné odpovídající hodnoty omezující chod.

**Získat počty ve výsledcích omezujících výsledků**

Když přidáte filtr do fazetovaného dotazu, můžete chtít zachovat `facet=Rating&$filter=Rating ge 4`příkaz omezující vlastnost (například). Technicky vzato facet=Hodnocení není potřeba, ale jeho ponechání vrátí počty hodnot omezujících ploch pro hodnocení 4 a vyšší. Pokud například klepnete na tlačítko "4" a dotaz obsahuje filtr pro větší nebo rovno "4", počty jsou vráceny pro každé hodnocení, které je 4 a vyšší.  

**Ujistěte se, že máte přesné počítání omezujících ploch**

Za určitých okolností můžete zjistit, že počet omezujících podmínek neodpovídá sady výsledků (viz [Fasetové navigace v Azure Cognitive Search (forum post)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Počet omezujících příhod může být nepřesné z důvodu architektury sříznutí. Každý index hledání má více úlomků a každý úlomek hlásí horní N omezující vlastnosti podle počtu dokumentů, který je pak sloučen do jednoho výsledku. Pokud některé úlomky mají mnoho odpovídajícíhodnoty, zatímco jiné mají méně, můžete zjistit, že některé omezující taki hodnoty chybí nebo podpočtené ve výsledcích.

I když toto chování může kdykoli změnit, pokud narazíte na toto chování dnes,\<můžete jej obejít umělým nafouknutím počet: číslo> na velké číslo vynutit úplné vykazování z každého oddílu. Pokud je hodnota count: větší nebo rovna počtu jedinečných hodnot v poli, jsou zaručeny přesné výsledky. Pokud jsou však počty dokumentů vysoké, je snížení výkonu, proto tuto možnost použijte uvážlivě.

### <a name="user-interface-tips"></a>Tipy pro uživatelské rozhraní
**Přidání popisků pro každé pole v navigaci omezujících položek**

Popisky jsou obvykle definovány v`index.cshtml` HTML nebo formuláři ( v ukázkové aplikaci). Neexistuje žádné rozhraní API v Azure Cognitive Search pro omezující fazet navigační popisky nebo jiná metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtr na základě rozsahu
Fazetování přes rozsahy hodnot je běžný požadavek na vyhledávací aplikace. Rozsahy jsou podporovány pro číselná data a datetime hodnoty. Další informace o jednotlivých přístupech si můžete přečíst v [dokumentech vyhledávání (Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Azure Cognitive Search zjednodušuje konstrukci rozsahu tím, že poskytuje dva přístupy pro výpočet rozsahu. Pro oba přístupy Azure Cognitive Search vytvoří příslušné rozsahy dané vstupy, které jste zadali. Pokud například zadáte hodnoty rozsahu 10|20|30, automaticky vytvoří rozsahy 0-10, 10-20, 20-30. Aplikace může volitelně odebrat všechny intervaly, které jsou prázdné. 

**Přístup 1: Použití parametru intervalu**  
Chcete-li nastavit cenové aspekty v přírůstcích o 10 USD, zadejte:`&facet=price,interval:10`

**Přístup 2: Použití seznamu hodnot**  
Pro číselná data můžete použít seznam hodnot.  Zvažte rozsah omezující `listPrice` chod pole vykreslený takto:

  ![Ukázkový seznam hodnot](media/search-faceted-navigation/Facet-5-Prices.PNG "Ukázkový seznam hodnot")

Chcete-li určit oblast omezující saze, jako je ta na předchozím snímku obrazovky, použijte seznam hodnot:

    facet=listPrice,values:10|25|100|500|1000|2500

Každý rozsah je sestaven pomocí 0 jako výchozí bod, hodnota ze seznamu jako koncový bod a pak oříznuté předchozí oblasti vytvořit diskrétní intervaly. Azure Cognitive Search provádí tyto věci jako součást fazetované navigace. Není nutné psát kód pro strukturování každý interval.

### <a name="build-a-filter-for-a-range"></a>Vytvoření filtru pro rozsah
Chcete-li filtrovat dokumenty na základě vybrané `"ge"` `"lt"` oblasti, můžete použít operátory a operátory filtrů ve dvoudílném výrazu, který definuje koncové body oblasti. Pokud například pro `listPrice` pole zvolíte rozsah 10-25, `$filter=listPrice ge 10 and listPrice lt 25`bude filtr . V ukázkovém kódu výraz filtru používá **parametry priceFrom** a **priceTo** k nastavení koncových bodů. 

  ![Dotaz na rozsah hodnot](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Dotaz na rozsah hodnot")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtr na základě vzdálenosti
Je běžné, že vidíte filtry, které vám pomohou vybrat obchod, restauraci nebo cíl na základě jeho blízkosti k vaší aktuální poloze. I když tento typ filtru může vypadat jako fazetovaná navigace, je to jen filtr. Zmíníme se zde pro ty z vás, kteří hledají konkrétně prováděcí poradenství pro tento konkrétní problém návrhu.

Existují dvě geoprostorové funkce v Azure Cognitive Search, **geo.distance** a **geo.intersects**.

* Funkce **geo.distance** vrátí vzdálenost v kilometrech mezi dvěma body. Jeden bod je pole a druhý je konstanta předaná jako součást filtru. 
* Funkce **geo.intersects** vrátí hodnotu true, pokud je daný bod v daném polygu. Bod je pole a polygon je určen jako konstantní seznam souřadnic předávaných jako součást filtru.

Příklady filtrů najdete v [syntaxi výrazu OData (Azure Cognitive Search).](query-odata-filter-orderby-syntax.md)

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Vyzkoušejte ukázku
Ukázka portálu úloh azure kognitivní hojení obsahuje příklady uvedené v tomto článku.

-   Podívejte se a otestujte pracovní ukázku online na [ukázce portálu úloh azure kognitivního hledání](https://aka.ms/azjobsdemo).

-   Stáhněte si kód z [úložiště ukázek Azure na GitHubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Při práci s výsledky hledání sledujte na adrese URL změny v konstrukci dotazu. Tato aplikace se stane připojit omezující strany uri při výběru každé z nich.

1. Chcete-li použít funkci mapování ukázkové aplikace, získejte klíč Mapy Bing z [Centra pro vývoj map Bing](https://www.bingmapsportal.com/). Vložte jej přes existující `index.cshtml` klíč na stránce. Nastavení `BingApiKey` v `Web.config` souboru se nepoužívá. 

2. Spusťte aplikaci. Vydejte se na volitelnou prohlídku nebo zavřete dialogové okno.
   
3. Zadejte hledaný výraz, například "analytik", a klikněte na ikonu Hledat. Dotaz se spustí rychle.
   
   Fazetovaná navigační struktura je také vrácena s výsledky hledání. Na stránce s výsledky hledání zahrnuje fazetovaná navigační struktura počty pro každý výsledek omezující sazeje. Nejsou vybrány žádné omezující strany, takže jsou vráceny všechny odpovídající výsledky.
   
   ![Výsledky hledání před výběrem omezujících ploch](media/search-faceted-navigation/faceted-search-before-facets.png "Výsledky hledání před výběrem omezujících ploch")

4. Klikněte na obchodní název, místo nebo minimální plat. Omezující jsou null na počáteční hledání, ale jak se na hodnoty, výsledky hledání jsou oříznuty položek, které již neodpovídají.
   
   ![Výsledky hledání po výběru omezujících ploch](media/search-faceted-navigation/faceted-search-after-facets.png "Výsledky hledání po výběru omezujících ploch")

5. Chcete-li vymazat fazetovaný dotaz, abyste mohli `[X]` vyzkoušet různé chování dotazu, klepnutím na následující omezující okolnosti vymažete omezující okolnosti.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Další informace
Podívejte se [na Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). V 45:25, je demo o tom, jak implementovat aspekty.

Pro více informací o principech návrhu pro fazetální navigaci doporučujeme následující odkazy:

* [Návrhové vzory: Famitová navigace](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front-end obavy při provádění fazetované vyhledávání - část 1](https://articles.uie.com/faceted_search2/)

