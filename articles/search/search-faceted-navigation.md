---
title: Postup implementace omezující navigace v hierarchii kategorií – Azure Search
description: Přidání navigační vlastnosti do aplikací, které se integrují s Azure Search, je cloudová vyhledávací služba na Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8e325abf1f58458d2fa035c8c8f081173efb0e65
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "69649896"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak v Azure Search implementovat fasetovou navigaci
Nafiltrovaná navigace je mechanismus filtrování, který umožňuje navigaci na podrobné procházení v aplikacích pro hledání. Termín "omezující navigace" možná není známý, ale pravděpodobně jste ho použili dřív. Jak ukazuje následující příklad, omezující navigace není nic větší než kategorie použité k filtrování výsledků.

 ![Ukázka portálu úloh Azure Search](media/search-faceted-navigation/azure-search-faceting-example.png "Ukázka portálu úloh Azure Search")

Omezující navigace je alternativní vstupní bod pro hledání. Nabízí pohodlnou alternativu k psaní složitých vyhledávacích výrazů ručně. Omezující vlastnosti vám pomůžou najít, co hledáte, a přitom zajistěte, aby nedošlo k žádným výsledkům. V rámci vývojářů umožňují charakteristiky vystavovat nejužitečnější vyhledávací kritéria pro procházení indexu vyhledávání. V online maloobchodních aplikacích je navržená navigace často vytvořená přes značky, oddělení (dětská obuv), velikost, ceny, oblíbenosti a hodnocení. 

Implementace omezujících navigačních funkcí se liší napříč technologiemi vyhledávání. V Azure Search je navržená navigace vytvořena v době dotazu pomocí polí, která jste předtím vytvořili ve schématu.

-   V dotazech, které vaše aplikace sestaví, musí dotaz odeslat *parametry dotazu omezující* vlastnosti, aby se získaly dostupné hodnoty filtru omezující vlastnosti pro tuto sadu výsledků dokumentů.

-   Aby bylo možné výslednou sadu dokumentů skutečně oříznout, aplikace musí také použít výraz `$filter`.

Při vývoji aplikací psaní kódu, který vytváří dotazy, představuje hromadnou práci. Mnohé z chování aplikace, které byste očekávali od vymezené navigace, poskytuje služba, včetně integrované podpory pro definování rozsahů a získání počtu výsledků omezujících vlastností. Služba také zahrnuje výchozí hodnoty rozumné, které vám pomůžou se vyhnout navigačním strukturám nepraktický. 

## <a name="sample-code-and-demo"></a>Ukázkový kód a ukázka
Tento článek používá jako příklad portál pro hledání úloh. Příklad je implementován jako aplikace ASP.NET MVC.

-   Přečtěte si a otestujte pracovní ukázku online na stránce [Azure Search ukázka pracovního portálu](https://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [úložiště ukázek Azure na GitHubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Začít
Pokud začínáte s vývojem pro hledání, nejlepším způsobem, jak si představit možnost navigace, je, že se zobrazí možnosti samoobslužného vyhledávání. Je to typ možností vyhledávání v podrobnostech na základě předdefinovaných filtrů, které se používají pro rychlé zúžení výsledků hledání prostřednictvím akcí typu Point-to-Click. 

### <a name="interaction-model"></a>Model interakce

Vyhledávací prostředí pro navýšení navigace je iterativní, takže začneme tím, že ho pochopíme jako sekvenci dotazů, které jsou přeloženy v reakci na akce uživatele.

Výchozím bodem je stránka aplikace, která poskytuje omezující navigaci, obvykle umístěnou na obvodu. Naomezující navigace je často stromovou strukturou se zaškrtávacími políčky pro každou hodnotu nebo kliknutím na text. 

1. Dotaz odeslaný do Azure Search určuje omezující navigační strukturu prostřednictvím jednoho nebo více parametrů dotazu omezující vlastnosti. Dotaz může například zahrnovat `facet=Rating`, například s možností `:values` nebo `:sort` k dalšímu upřesnění prezentace.
2. Prezentační vrstva vykreslí stránku hledání, která poskytuje omezující navigaci, pomocí omezujících vlastností zadaných v žádosti.
3. Vzhledem k omezující navigační struktuře, která obsahuje hodnocení, klikněte na 4 a určete, že se mají zobrazit jenom produkty se hodnocením 4 nebo vyšším. 
4. V reakci aplikace pošle dotaz, který obsahuje `$filter=Rating ge 4` 
5. Prezentační vrstva aktualizuje stránku a zobrazí omezenou sadu výsledků, která obsahuje pouze ty položky, které odpovídají novým kritériím (v tomto případě produkty ohodnocené 4 a v tomto případě).

Omezující vlastnost je parametr dotazu, ale Nepleťe se s vstupem dotazu. Nikdy se nepoužívá jako kritéria výběru v dotazu. Místo toho si popřemýšlejte parametry dotazu omezující vlastnosti jako vstupy do navigační struktury, která se vrátí v odpovědi. Pro každý parametr dotazu omezující podmínky, který zadáte, Azure Search vyhodnotí, kolik dokumentů je v částečných výsledcích pro každou hodnotu omezující vlastnosti.

Všimněte si `$filter` v kroku 4. Filtr je důležitým aspektem omezující navigace. I když jsou charakteristiky a filtry nezávislé na rozhraní API, je potřeba, abyste mohli doručovat prostředí, které máte v úmyslu. 

### <a name="app-design-pattern"></a>Vzor návrhu aplikace

V kódu aplikace je vzorem použití parametrů dotazu na omezující vlastnost k vrácení omezující navigační struktury spolu s výsledky omezující vlastnosti a výrazem $filter.  Výraz filtru zpracovává událost Click u hodnoty omezující vlastnosti. Výraz `$filter` můžete představit jako kód za skutečným oříznutím výsledků hledání vrácených do prezentační vrstvy. V případě omezující vlastnosti barev je po kliknutí na červenou barvu implementována prostřednictvím výrazu `$filter`, který vybere pouze ty položky, které mají červenou barvu. 

### <a name="query-basics"></a>Základy dotazů

V Azure Search je požadavek zadán prostřednictvím jednoho nebo více parametrů dotazu (viz dokumenty pro [hledání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) s popisem každého z nich). Žádný z parametrů dotazu není povinný, ale aby byl dotaz platný, musíte mít aspoň jednu.

Přesnost, která se považuje za schopnost vyfiltrovat nepodstatné přístupy, se dosahuje prostřednictvím jednoho nebo obou těchto výrazů:

-   **Hledat =**  
    Hodnota tohoto parametru představuje hledaný výraz. Může se jednat o jednotlivý text nebo složitý vyhledávací výraz, který obsahuje více podmínek a operátorů. Na serveru se vyhledávací výraz používá pro fulltextové vyhledávání, dotazování na hledaná pole v indexu pro vyhovující výrazy a vrácení výsledků v pořadí řazení. Nastavíte-li `search` na hodnotu null, provádění dotazu bude po celém indexu (tj. `search=*`). V tomto případě jsou další prvky dotazu, jako je `$filter` nebo profil vyhodnocování, primární faktory, které mají vliv na to, které dokumenty se vrátí `($filter`) a v jakém pořadí (`scoringProfile` nebo `$orderby`).

-   **$filter =**  
    Filtr je účinný mechanismus pro omezení velikosti výsledků hledání na základě hodnot konkrétních atributů dokumentu. Nejprve se vyhodnotí `$filter`, následované logikou pro vytváření vlastností, která generuje dostupné hodnoty a odpovídající počty pro každou hodnotu.

Složité výrazy hledání snižují výkon dotazu. Pokud je to možné, využijte dobře konstruované výrazy filtru ke zvýšení přesnosti a zlepšení výkonu dotazů.

Pro lepší pochopení, jak filtr přidává větší přesnost, porovnejte složitý vyhledávací výraz s výrazem, který obsahuje výraz filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Oba dotazy jsou platné, ale druhá je nadřazená, pokud hledáte nemotelsy s parkováním v Seattlu.
-   První dotaz spoléhá na tato konkrétní slova uvedená nebo není uvedena v polích řetězců, jako je název, popis a jakékoli jiné pole obsahující hledaná data.
-   Druhý dotaz vyhledá přesné shody strukturovaných dat a pravděpodobně bude mnohem přesnější.

V aplikacích, které zahrnují nahodnocenou navigaci, se ujistěte, že každá akce uživatele nad naomezující navigační strukturou je doprovázena zúžením výsledků hledání. K zúžení výsledků použijte výraz filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Vytvoření omezující navigační aplikace
V kódu aplikace, který sestaví požadavek hledání, implementujete omezující navigaci s Azure Search. Omezující navigace se spoléhá na prvky ve schématu, které jste definovali dříve.

Předdefinovaný v indexu vyhledávání je atribut `Facetable [true|false]` index, který je nastaven u vybraných polí na povolení nebo zakázání jejich použití v omezující navigační struktuře. Bez `"Facetable" = true` nemůže být pole použito v navigaci omezující vlastnosti.

Prezentační vrstva v kódu poskytuje uživatelské prostředí. Měl by zobrazovat seznam částí naomezujících navigačních prvků, jako je popisek, hodnoty, zaškrtávací políčka a počet. Azure Search REST API je platforma nezávislá, takže použijte libovolný jazyk a platformu, kterou požadujete. Důležité je, abyste zahrnuli prvky uživatelského rozhraní, které podporují přírůstkovou aktualizaci s aktualizovaným stavem uživatelského rozhraní, když je vybraná každá další omezující vlastnost. 

V době dotazu vytvoří kód aplikace požadavek, který obsahuje `facet=[string]`, parametr žádosti, který poskytuje pole omezující vlastnosti. Dotaz může mít několik omezujících vlastností, například `&facet=color&facet=category&facet=rating`, každý z nich oddělený znakem ampersand (&).

Kód aplikace musí také vytvořit výraz `$filter` pro zpracování událostí kliknutí v omezující navigaci. @No__t_0 redukuje výsledky hledání pomocí hodnoty omezující vlastnosti jako kritérií filtru.

Azure Search vrátí výsledky hledání na základě jednoho nebo více podmínek, které zadáte, spolu s aktualizacemi omezující navigační struktury. V Azure Search je omezující navigace v rámci jedné úrovně konstrukce s hodnotami omezujícími vlastnostmi a počty výsledků pro každý z nich.

V následujících částech se podrobněji podíváme na to, jak sestavovat jednotlivé části.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Sestavení indexu
Omezující vlastnost je povolena pro pole podle pole v indexu prostřednictvím tohoto atributu index: `"Facetable": true`.  
Ve výchozím nastavení jsou `Facetable` všechny typy polí, které by mohly být použity v omezující navigaci. Mezi takové typy polí patří `Edm.String`, `Edm.DateTimeOffset` a všechny typy číselného pole (v podstatě všechny typy polí jsou plošky kromě `Edm.GeographyPoint`, které se nedají použít v omezující navigaci). 

Při sestavování indexu je osvědčeným postupem pro navázání navigace explicitně zapnout omezující vlastnost pro pole, která by nikdy neměla být použita jako omezující vlastnost.  Konkrétně pole řetězců pro hodnoty singleton, jako je ID nebo název produktu, by měla být nastavená na `"Facetable": false`, aby se předešlo jejich nechtěnému (a neúčinnému) použití v omezující navigaci. Vypnutí omezujících vlastností tam, kde je nepotřebujete, pomáhá udržet velikost indexu malou a obvykle zvyšuje výkon.

Následuje část schématu pro ukázkovou ukázkovou aplikaci portálu úloh. Tyto atributy se oříznou, aby se snížila velikost:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Jak vidíte ve vzorovém schématu, `Facetable` je vypnuté pro pole řetězců, která by se neměla používat jako omezující vlastnosti, jako jsou například hodnoty ID. Vypnutí omezujících vlastností tam, kde je nepotřebujete, pomáhá udržet velikost indexu malou a obvykle zvyšuje výkon.

> [!TIP]
> Jako osvědčený postup zahrňte úplnou sadu atributů indexu pro každé pole. I když je ve výchozím nastavení pro téměř všechna pole funkce `Facetable` zapnutá, může vám každý atribut při rozhodování považovat za důsledky pro každé rozhodnutí o schématu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Kontrolovat data
Kvalita vašich dat má přímý vliv na to, jestli namaterializujea omezující navigační strukturu podle očekávání. Má vliv také na snadné vytváření filtrů ke snížení sady výsledků.

Pokud chcete omezující vlastnost podle značky nebo ceny, každý dokument by měl obsahovat hodnoty pro *brandy* a *ProductPrice* , které jsou platné, konzistentní a produktivní jako možnost filtru.

Tady je několik připomenutí, co je potřeba pro:

* Pro každé pole, podle kterého chcete omezující vlastnost, si položte sami, jestli obsahuje hodnoty vhodné pro filtry v rámci samoobslužného vyhledávání. Hodnoty by měly být krátké, popisné a dostatečně odlišnější, aby bylo možné nabídnout jasný výběr mezi konkurenčními možnostmi.
* Chybné pravopisné nebo téměř vyhovující hodnoty. Pokud jste nastavili vlastnost Color a pole obsahují oranžová a Ornage (chybná kontrola), omezující vlastnost založená na poli Color si vybírá.
* Smíšený text případu může být také wreak zmatek v navýšené navigaci s oranžovým a oranžovým zobrazením jako dvě různé hodnoty. 
* Jedna a plural verze stejné hodnoty můžou mít za následek samostatnou omezující vlastnost pro každý z nich.

Jak můžete představovat, opatrnost při přípravě dat je zásadním aspektem efektivní navigace s omezujícími vlastnostmi.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní
Práce zpátky z prezentační vrstvy vám pomůže odhalit požadavky, které se můžou vyskytnout jinak, a porozumět tomu, které funkce jsou pro hledání zásadní.

V případě vymezené navigace zobrazí stránka web nebo aplikace naomezující navigační strukturu, detekuje vstup uživatele na stránce a vloží změněné prvky. 

V případě webových aplikací se AJAX obvykle používá v prezentační vrstvě, protože umožňuje aktualizovat přírůstkové změny. Můžete také použít ASP.NET MVC nebo jinou platformu pro vizualizaci, která se může připojit k Azure Search službě přes protokol HTTP. Ukázková aplikace, na kterou se odkazuje v celém tomto článku – **ukázka Azure Search úlohový portál** – se stane aplikací ASP.NET MVC.

V ukázce je do stránky výsledků hledání integrována omezující navigace. Následující příklad pořízený z `index.cshtml` souboru ukázkové aplikace zobrazuje statickou strukturu HTML pro zobrazení omezující navigace na stránce s výsledky hledání. Seznam omezujících vlastností se sestaví nebo znovu vytvoří dynamicky při odeslání hledaného termínu nebo zaškrtnutí nebo zrušení omezující vlastnosti.

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

Následující fragment kódu ze stránky `index.cshtml` dynamicky sestaví kód HTML pro zobrazení první omezující vlastnosti, obchodní titul. Podobné funkce dynamicky sestavují kód HTML pro ostatní omezující vlastnosti. Každá omezující vlastnost má popisek a počet, který zobrazuje počet nalezených položek pro daný výsledek omezující vlastnosti.

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
> Při návrhu stránky výsledků hledání nezapomeňte přidat mechanismus pro mazání omezujících vlastností. Pokud přidáte zaškrtávací políčka, můžete snadno zjistit, jak filtry vymazat. Pro jiná rozložení budete možná potřebovat vzor navigace s popisem cesty nebo jiný tvůrčí přístup. Například na ukázkové aplikaci portálu pro vyhledávání úloh můžete kliknout na `[X]` po vybrané omezující vlastnosti, aby se tato omezující vlastnost vymazala.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Sestavení dotazu
Kód, který napíšete pro vytváření dotazů, by měl určovat všechny části platného dotazu, včetně vyhledávacích výrazů, omezujících vlastností, filtrů a profilů vyhodnocování – cokoli, co se používá k formulaci žádosti. V této části zkoumáme, kde se omezující vlastnosti vejdou do dotazu, a jak se používají filtry s omezujícími vlastnostmi k doručení omezené sady výsledků.

Všimněte si, že jsou v této ukázkové aplikaci integrální charakteristiky. Vyhledávání na portálu úloh ukázka je navrženo kolem s omezujícími možnostmi navigace a filtry. Výrazným umístěním naomezujícího navigace na stránce je demonstrace důležitosti. 

Příkladem je často dobrým místem, kde začít. Následující příklad pořízený z `JobsSearch.cs` souboru vytvoří požadavek, který vytvoří navigaci omezující vlastnosti na základě obchodních titulů, umístění, typu zaúčtování a minimálního platu. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr dotazu omezující vlastnosti je nastaven na pole a v závislosti na datovém typu může být dále parametrizovaný seznamem odděleným čárkami, který obsahuje `count:<integer>`, `sort:<>`, `interval:<integer>` a `values:<list>`. Seznam hodnot se při nastavování rozsahů podporuje pro číselná data. Podrobnosti o využití najdete v tématu [hledání dokumentů (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

Společně s omezujícími vlastnostmi by požadavek, který aplikace formuluje, měl také vytvořit filtry pro zúžení sady kandidátských dokumentů na základě výběru hodnoty omezující vlastnosti. V případě úložiště kol napodobná navigace nabízí otázky *, jako jsou jaké barvy, výrobci a typy kol k dispozici?* . Dotazy na odpovědi *, jako je například přesná kola, jsou v tomto cenovém rozsahu červené, horská kola?* Když kliknete na tlačítko "červená" a označíte, že by se měly zobrazit jenom červené produkty, další dotaz, který aplikace odešle, zahrnuje `$filter=Color eq ‘Red’`.

Následující fragment kódu ze stránky `JobsSearch.cs` přidá vybraný obchodní titul do filtru, pokud vyberete hodnotu z omezující vlastnosti obchodního titulu.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="indexing-tips"></a>Tipy pro indexování
**Vylepšit efektivitu indexu, pokud nepoužíváte vyhledávací pole**

Pokud vaše aplikace používá nastavované navigace výhradně (tj. bez vyhledávacího pole), můžete pole označit jako `searchable=false`, `facetable=true` vytvořit kompaktnější index. Kromě toho indexování probíhá pouze u úplných hodnot omezujících vlastností bez dělení na slova nebo při indexování částí hodnoty více slov.

**Určete, která pole se dají použít jako omezující vlastnosti.**

Odvolání, že schéma indexu Určuje, která pole jsou k dispozici pro použití jako omezující vlastnost. Za předpokladu, že pole je ploška, dotaz určuje, která pole jsou omezující vlastností. Pole, podle kterého vytváříte omezující vlastnosti, poskytuje hodnoty, které se zobrazí pod popiskem. 

Hodnoty, které se zobrazí pod každým popiskem, se načítají z indexu. Například pokud je pole omezující vlastnost *Color*, hodnoty dostupné pro další filtrování jsou hodnoty pro toto pole – červená, černá a tak dále.

Pouze pro číselné hodnoty a hodnoty DateTime můžete explicitně nastavit hodnoty v poli omezující podmínky (například `facet=Rating,values:1|2|3|4|5`). Seznam hodnot je povolený pro tyto typy polí, aby se zjednodušilo oddělení výsledků omezujících vlastností do souvislých rozsahů (v závislosti na numerických hodnotách nebo časových obdobích). 

**Ve výchozím nastavení můžete mít jenom jednu úroveň s vlastností navigace.** 

Jak je uvedeno, neexistuje žádná přímá podpora pro vnořování omezujících vlastností v hierarchii. Ve výchozím nastavení je v Azure Search omezující navigace v podporovaná jenom jedna úroveň filtrů. Existují však alternativní řešení. Hierarchickou strukturu omezující vlastnosti můžete kódovat v `Collection(Edm.String)` s jedním vstupním bodem na hierarchii. Implementace tohoto alternativního řešení je nad rámec tohoto článku. 

### <a name="querying-tips"></a>Tipy pro dotazování
**Ověřit pole**

Pokud vytvoříte seznam omezujících vlastností dynamicky založených na nedůvěryhodném uživatelském vstupu, ověřte, zda jsou názvy omezujících polí platné. Případně můžete při sestavování adres URL pomocí `Uri.EscapeDataString()` v rozhraní .NET nebo ekvivalentu na vaší platformě zvolit, aby se názvy vyhnuly.

### <a name="filtering-tips"></a>Tipy pro filtrování
**Zvýšit přesnost vyhledávání pomocí filtrů**

Použijte filtry. Pokud spoléháte na pouze hledané výrazy, lemmatizátor může způsobit vrácení dokumentu, který nemá přesnou hodnotu omezující vlastnosti v žádném z jeho polí.

**Zvýšení výkonu hledání pomocí filtrů**

Filtry zúží sadu kandidátních dokumentů pro hledání a vyloučí je z řazení. Pokud máte rozsáhlou sadu dokumentů, při použití selektivních aspektů procházení hierarchie často nabízí lepší výkon.
  
**Filtrovat pouze pole s omezujícími vlastnostmi**

V podrobném procházení podrobností obvykle chcete zahrnout pouze dokumenty, které mají hodnotu omezující vlastnosti v konkrétním (omezujícím) poli, a ne kdekoli ve všech prohledávatelných polích. Přidání filtru posiluje cílové pole směrováním služby tak, aby hledalo pouze v poli s omezující hodnotou.

**Oříznout výsledky omezujících vlastností s dalšími filtry**

Výsledky omezující vlastnosti jsou dokumenty nalezené ve výsledcích hledání, které odpovídají termínům omezující podmínky. V následujícím příkladu jsou ve výsledcích hledání pro *cloud computingu*254 položek *interní specifikace* jako typ obsahu. Položky nejsou nutně vzájemně exkluzivní. Pokud položka splňuje kritéria obou filtrů, počítá se v každé z nich. Tato duplicita je možná při vytváření omezujících podmínek v `Collection(Edm.String)`ch polích, které se často používají k implementaci označování dokumentu.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Obecně platí, že pokud zjistíte, že výsledky omezující vlastnosti jsou konzistentně příliš velké, doporučujeme přidat další filtry, abyste uživatelům poskytli více možností pro zúžení hledání.

### <a name="tips-about-result-count"></a>Tipy k počtu výsledků

**Omezte počet položek v navigaci omezujícími vlastnostmi.**

Pro každé naomezující pole v navigační větvi je výchozí limit 10 hodnot. Toto výchozí nastavení dává smysl pro navigační struktury, protože udržuje seznam hodnot pro spravovatelnou velikost. Výchozí hodnotu můžete přepsat přiřazením hodnoty k počtu.

* `&facet=city,count:5` určuje, že jako výsledek omezující vlastnosti se vrátí jenom prvních pět měst nalezených v horních seřazených výsledcích. Vezměte v úvahu vzorový dotaz s hledaným termínem "letiště" a 32 shody. Pokud dotaz určuje `&facet=city,count:5`, jsou do výsledků těchto vlastností zahrnuty pouze prvních pět jedinečných měst s největším dokumentem ve výsledcích hledání.

Všimněte si rozdílu mezi výsledky omezujících vlastností a výsledky hledání. Výsledky hledání jsou všechny dokumenty, které odpovídají dotazu. Výsledky omezující vlastnosti jsou odpovídajícími hodnotami každé hodnoty omezující vlastnosti. V příkladu výsledky hledání zahrnují názvy měst, které nejsou v seznamu klasifikace omezujících vlastností (5 v našem příkladu). Výsledky, které jsou vyfiltrované prostřednictvím navýšení v rámci navýšení navigace, se budou zobrazovat při vymazání omezujících vlastností, nebo kromě měst. 

> [!NOTE]
> Diskuze o `count`, pokud existuje více než jeden typ, může být matoucí. Následující tabulka nabízí stručný souhrn toho, jak se pojem používá v rozhraní Azure Search API, vzorový kód a dokumentace. 

* `@colorFacet.count`<br/>
  V kódu prezentace byste měli vidět parametr Count pro omezující vlastnost, která se používá k zobrazení počtu výsledků omezující vlastnosti. Ve výsledcích omezující vlastnosti Count označuje počet dokumentů, které odpovídají podmínky nebo rozsahu omezující vlastnosti.
* `&facet=City,count:12`<br/>
  V dotazu na omezující vlastnost můžete nastavit počet na hodnotu.  Výchozí hodnota je 10, ale je možné ji nastavit na vyšší nebo nižší. Nastavení `count:12` získá prvních 12 shod v rámci výsledků omezujících vlastností podle počtu dokumentů.
* `@odata.count`<br/>
  V odpovědi na dotaz tato hodnota označuje počet vyhovujících položek ve výsledcích hledání. V průměru je větší než součet všech výsledků omezujících vlastností z důvodu přítomnosti položek, které odpovídají hledanému termínu, ale nemají shodné hodnoty omezující vlastnosti.

**Výsledky získání omezujících vlastností**

Když přidáte filtr do omezujícího dotazu, je vhodné zachovat příkaz omezující podmínky (například `facet=Rating&$filter=Rating ge 4`). Technicky, omezující vlastnost = hodnocení není potřeba, ale udržování vrátí počty hodnot omezujících vlastností hodnocení 4 a vyšší. Pokud například kliknete na "4" a dotaz obsahuje filtr pro větší nebo roven "4", vrátí se počty pro každé hodnocení, které je 4 a vyšší.  

**Ujistěte se, že získáte přesné počty omezujících vlastností.**

Za určitých okolností možná zjistíte, že počty omezujících vlastností se neshodují s sadami výsledků (viz téma [s omezujícími odkazy v Azure Search (příspěvek ve fóru)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Počty omezujících podmínek můžou být kvůli architektuře horizontálního dělení nepřesné. Každý index hledání má několik horizontálních oddílů a každá horizontálních oddílů oznamuje hlavní N omezující vlastnosti podle počtu dokumentů, který se pak sloučí do jednoho výsledku. Pokud některé horizontálních oddílů obsahují mnoho hodnot, zatímco jiné mají méně, může se stát, že některé hodnoty omezující vlastnosti ve výsledcích chybí nebo jsou ve výsledku zjištěny.

I když se toto chování může kdykoli změnit, pokud k tomuto chování dojde v dnešní době, můžete ho obejít uměle tím, že vyřadíte počet: \<number > na velké číslo, abyste vynutili úplné generování sestav každého horizontálních oddílů. Pokud hodnota Count: je větší nebo rovna počtu jedinečných hodnot v poli, jsou zaručeny přesné výsledky. Pokud jsou ale počty dokumentů vysoké, dochází ke snížení výkonu, takže tuto možnost používejte uvážlivě.

### <a name="user-interface-tips"></a>Tipy pro uživatelské rozhraní
**Přidat popisky pro každé pole v navigaci omezujícími vlastnostmi**

Popisky jsou obvykle definovány ve formátu HTML nebo formuláře (`index.cshtml` v ukázkové aplikaci). V Azure Search není žádné rozhraní API pro navigační popisky omezující vlastnosti ani jiná metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrovat podle rozsahu
Omezující vlastnosti přes rozsahy hodnot jsou běžným požadavkem na aplikaci vyhledávání. Rozsahy jsou podporovány pro číselná data a hodnoty data a času. V [dokumentech pro hledání (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)si můžete přečíst další informace o každém přístupu.

Azure Search zjednodušuje vytváření rozsahu zadáním dvou přístupů k výpočtu rozsahu. U obou přístupů Azure Search vytvoří odpovídající rozsahy pro dané vstupy. Pokud například zadáte hodnoty rozsahu 10 | 20 | 30, automaticky se vytvoří rozsahy 0-10, 10-20, 20-30. Aplikace může volitelně odebrat všechny intervaly, které jsou prázdné. 

**Přístup 1: použití parametru intervalu**  
Chcete-li nastavit cenové charakteristiky v $10 přírůstcích, zadejte: `&facet=price,interval:10`

**Přístup 2: použití seznamu hodnot**  
Pro číselná data můžete použít seznam hodnot.  Zvažte rozsah omezujících podmínek pro `listPrice` pole vykreslený takto:

  ![Seznam ukázkových hodnot](media/search-faceted-navigation/Facet-5-Prices.PNG "Seznam ukázkových hodnot")

Chcete-li určit rozsah omezujících vlastností, jako je například na předchozím snímku obrazovky, použijte seznam hodnot:

    facet=listPrice,values:10|25|100|500|1000|2500

Každý rozsah je sestaven jako výchozí bod hodnotou 0, což je hodnota ze seznamu jako koncový bod a pak se ořízne předchozí rozsah, aby bylo možné vytvořit diskrétní intervaly. Azure Search provádí tyto akce jako součást omezující navigace. Nemusíte psát kód pro strukturování každého intervalu.

### <a name="build-a-filter-for-a-range"></a>Vytvoření filtru pro rozsah
Chcete-li filtrovat dokumenty na základě zvoleného rozsahu, můžete použít operátory filtru `"ge"` a `"lt"` ve výrazu se dvěma částmi, který definuje koncové body rozsahu. Pokud například vyberete rozsah 10-25 pro pole `listPrice`, filtr bude `$filter=listPrice ge 10 and listPrice lt 25`. Ve vzorovém kódu výraz filtru používá parametry **priceFrom** a **priceTo** pro nastavení koncových bodů. 

  ![Dotaz na rozsah hodnot](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Dotaz na rozsah hodnot")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrovat podle vzdálenosti
Je běžné, že vidíte filtry, které vám pomůžou vybrat obchod, restaurace nebo cíl na základě jeho blízkosti s vaším aktuálním umístěním. I když tento typ filtru může vypadat jako s vlastností navigace, je to jen filtr. Zmiňujeme se to pro vás, kdo si konkrétně vyhledá Rady pro implementaci tohoto konkrétního problému s návrhem.

Existují dvě geoprostorové funkce Azure Search, **geografické. Distance** a **geo. intersects**.

* Funkce **geo. Distance** vrací vzdálenost v kilometrech mezi dvěma body. Jedním z bodů je pole a další je konstanta předaná jako součást filtru. 
* Funkce **geo. intersects** vrátí hodnotu true, pokud je daný bod v rámci daného mnohoúhelníku. Bod je pole a mnohoúhelník je zadán jako konstantní seznam souřadnic předaných jako součást filtru.

Příklady filtrů můžete najít v [syntaxi výrazu OData (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Vyzkoušejte si ukázku
Ukázka na portálu úloh Azure Search obsahuje příklady, na které se odkazuje v tomto článku.

-   Přečtěte si a otestujte pracovní ukázku online na stránce [Azure Search ukázka pracovního portálu](https://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [úložiště ukázek Azure na GitHubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Při práci s výsledky hledání Sledujte adresu URL pro změny v vytváření dotazů. Tato aplikace se k identifikátoru URI připojí, když je vyberete.

1. Pokud chcete používat funkce mapování ukázkové aplikace, Získejte klíč mapy Bing z centra pro vývojáře pro [mapy Bing](https://www.bingmapsportal.com/). Vložte ho přes existující klíč na stránku `index.cshtml`. Nastavení `BingApiKey` v souboru `Web.config` se nepoužívá. 

2. Spusťte aplikaci. Pořídit volitelnou prohlídku nebo zrušit dialog.
   
3. Zadejte hledaný termín, například "analytika", a klikněte na ikonu hledání. Dotaz se rychle spustí.
   
   Ve výsledcích hledání se také vrátí omezující navigační struktura. V rámci stránky výsledků hledání obsahuje omezující navigační struktura napočty pro každý výsledek omezující vlastnosti. Nejsou vybrány žádné omezující vlastnosti, takže se vrátí všechny vyhovující výsledky.
   
   ![Výsledky hledání před výběrem omezujících vlastností](media/search-faceted-navigation/faceted-search-before-facets.png "Výsledky hledání před výběrem omezujících vlastností")

4. Klikněte na název firmy, umístění nebo minimální mzdu. U počátečního vyhledávání byly omezující vlastnosti null, ale při jejich provádění jsou pro výsledky hledání oříznuté položky, které se už neshodují.
   
   ![Výsledky hledání po výběru omezujících vlastností](media/search-faceted-navigation/faceted-search-after-facets.png "Výsledky hledání po výběru omezujících vlastností")

5. Pokud chcete vymazat omezující dotaz, abyste mohli vyzkoušet jiné chování dotazů, klikněte na `[X]` za vybranými omezujícími vlastnostmi, aby se vymazaly omezující vlastnosti.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Další informace
Sledujte [Azure Search podrobně hloubkovou](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). V 45:25 je k dispozici ukázka implementace omezujících vlastností.

Další informace o principech návrhu pro návrhovou navigaci vám doporučujeme tyto odkazy:

* [Vzory návrhu: omezující navigace](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Obavy z front-endu při implementaci hledání podle vlastností – část 1](https://articles.uie.com/faceted_search2/)

