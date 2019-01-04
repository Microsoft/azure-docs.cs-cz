---
title: Jak implementovat fasetovou navigaci v hierarchie category – Azure Search
description: Přidání omezující vlastnost navigace k aplikacím, které se integrují s Azure Search, vyhledávání služby hostované v cloudu v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/10/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 67cc955e4a265a2b29893bd0484dd905f4f2c2b2
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632429"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak v Azure Search implementovat fasetovou navigaci
Fasetová navigace je filtrační mechanismus, který poskytuje samořízeného k podrobnostem námětů a navigace ve vyhledávání aplikací. Termín 'Fasetové navigace' může být obeznámeni, ale pravděpodobně ho před jste použili. Jak ukazuje následující příklad, Fasetové navigace není nic jiného než kategorie slouží k filtrování výsledků.

 ![Ukázka portálu úlohy Azure Search][1]

Fasetová navigace je alternativní vstupní bod pro hledání. Nabízí vhodnou alternativu ručně psát složité hledaných výrazů. Omezující vlastnosti můžete najít, co hledáte, přitom zajistit, že nebudete mít nula výsledků. Jako vývojář omezující vlastnosti vám umožní vystavit nejužitečnější kritérií vyhledávání pro navigaci ve vyhledávacím korpusu služby. V aplikacích pro online maloobchodní prodej Fasetové navigace často integrované značky, oddělení (dětský obuv), velikost, ceny, oblíbenosti a hodnocení. 

Implementace Fasetové navigace se liší mezi vyhledávací technologie. Fasetová navigace se ve službě Azure Search vytvořil v době zpracování dotazu pomocí polí, která dříve přidělené ve schématu.

-   V dotazech, které vaše aplikace sestavena, musíte odeslat dotaz *parametry dotazu omezující vlastnost* získat hodnoty filtru k dispozici omezující vlastnost pro tento výsledek sadu dokumentů.

-   Ve skutečnosti oříznout dokumentu výsledek nastavit, musíte také použít aplikaci `$filter` výrazu.

Psaní kódu, který vytvoří dotazy vývoje aplikací, představuje velkou část práce. Řadu chování aplikací, které očekáváte od Fasetové navigace poskytované služby, včetně integrovaná podpora pro definování oblastí a získávání počty pro omezující vlastnost výsledky. Tato služba také zahrnuje rozumné výchozí hodnoty, které vám pomůžou zabránit těžkopádným navigační struktury. 

## <a name="sample-code-and-demo"></a>Ukázkový kód a ukázky
Tento článek používá portál pro prohledávání úlohy jako příklad. V příkladu je implementovaný jako aplikaci ASP.NET MVC.

-   A testování pracovní si online ukázku v [Azure Search úlohy portálu Demo](https://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [Azure-Samples úložišti na Githubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Začínáme
Pokud začínáte hledání vývoje, je nejlepší způsob, jak si můžete představit Fasetové navigace, ukazuje možnosti usnadní nastavení hledání. Je typ procházení vyhledávací funkce, na základě předdefinovaných filtrů, použít pro rychlé zúžení výsledků hledání prostřednictvím akce ukázat a kliknout. 

### <a name="interaction-model"></a>Model interakce

Možnosti vyhledávání pro fasetovou navigaci je iterativní, Pojďme začít Pochopením jako posloupnost dotazy, které měly rozvinout v reakci na akce uživatele.

Počátečním bodem je, která poskytuje Fasetové navigace, obvykle umístěné v obvodu stránky aplikace. Fasetová navigace je často stromové struktury s zaškrtávací políčka pro každou hodnotu, nebo kliknout, čímž text. 

1. Dotaz odeslaný do služby Azure Search určuje struktuře Fasetové navigace prostřednictvím jednoho nebo více parametrů dotazu omezující vlastnost. Například může zahrnovat dotaz `facet=Rating`, klidně i s `:values` nebo `:sort` možnosti pro další upřesnění prezentaci.
2. Prezentační vrstva vykreslí stránku vyhledávání, která poskytuje Fasetové navigace pomocí omezujícími vlastnostmi zadanými v požadavku.
3. Zadaný fasetovou strukturu navigace, která zahrnuje hodnocení, kliknutí na "4" k označení, že mají být zobrazeny pouze produkty s hodnocení 4 nebo vyšší. 
4. Jako odpověď zašle dotaz, který obsahuje aplikaci `$filter=Rating ge 4` 
5. Prezentační vrstva aktualizuje stránku, zobrazuje se sníženou výsledek sada obsahující jenom ty položky, které splňují kritéria nové (v tomto případě produktů hodnocení 4 a vyšší).

Omezující vlastnost je parametr dotazu, ale Nezaměňujte je s vstup dotazu. To se nikdy nepoužívá jako kritéria pro výběr v dotazu. Místo toho přemýšlejte o omezující vlastnost parametry dotazu jako vstupy do navigační struktury, která v odpovědi se vrátí zpět. Pro každý parametr dotazu omezující vlastnost, který zadáte vyhodnotí Azure Search k tom, kolik dokumentů v částečné výsledky pro každý hodnota omezující vlastnosti.

Všimněte si, že `$filter` v kroku 4. Tento filtr je důležitou součástí Fasetové navigace. Ačkoli omezující vlastnosti a filtry jsou nezávislé v rozhraní API, je nutné k poskytování, který chcete. 

### <a name="app-design-pattern"></a>Vzor návrhu aplikace

Vzor v kódu aplikace, je použít omezující vlastnost parametry dotazu, který vrátí struktuře Fasetové navigace spolu s výsledky omezující vlastnost, a navíc výrazu $filter.  Výraz filtru zpracovává událost click na hodnota omezující vlastnosti. Představte si, že `$filter` výraz jako kód za skutečné oříznutí výsledků hledání vrátí do prezentační vrstvy. Zadaný omezující vlastnost barvy, kliknete na červenou barvu se implementuje pomocí `$filter` výraz, který vybere pouze položky, které mají barvu červené. 

### <a name="query-basics"></a>Základy dotazů

Ve službě Azure Search, požadavek se specifikuje prostřednictvím jednoho nebo více parametrů dotazu (viz [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) popis každé z nich). Žádný z parametrů dotazu není povinný, ale musí mít aspoň jeden v pořadí pro dotaz platný.

Přesnost, porozuměl jsem jim jako možnost filtrování irelevantní přístupů, můžete dosáhnout jeden nebo oba z těchto výrazů:

-   **hledání =**  
    Hodnota tohoto parametru představuje hledaný výraz. Může být jednoduchým text nebo komplexní hledaný výraz, který obsahuje více podmínek a operátory. Na serveru je použit výraz vracející vyhledávání pro fulltextové vyhledávání, dotazování prohledávatelná pole v indexu pro odpovídající podmínky, vrátí výsledky v pořadí řazení. Pokud nastavíte `search` na hodnotu null, dotazu je spuštění přes celý index (to znamená `search=*`). V takovém případě další prvky dotazu, například `$filter` nebo bodovací profil, budou primárními faktory ovlivňující dokumenty, které jsou vráceny `($filter`) a v jakém pořadí (`scoringProfile` nebo `$orderby`).

-   **$filter =**  
    Filtr je výkonný mechanismus pro omezení velikosti výsledky hledání na základě hodnot atributů určitého dokumentu. A `$filter` je vyhodnocen jako první, za nímž následuje "faceting" logiku, která generuje dostupné hodnoty a odpovídající počty pro každou hodnotu

Komplexní hledaných výrazů snížit výkon dotazu. Kde je to možné, zvýšit přesnost a zlepšit výkon dotazů pomocí dobře sestavený filtrovací výrazy.

Chcete-li lépe pochopit, jak filtr přidá další přesnosti, porovnejte komplexní hledaný výraz, který obsahuje výraz filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Oba dotazy jsou platné, ale druhá je vynikající Pokud hledáte bez motely s parkovací v Seattlu.
-   První dotaz závisí na těchto konkrétních slov uvedených nebo není podle pole řetězců, jako je název, popis a jakékoli jiné pole obsahující prohledávatelná data.
-   Druhý dotaz hledá přesné shody u strukturovaných dat a by mohla být mnohem přesnější.

V aplikacích, které zahrnují fasetová navigace, ujistěte se, že je přiložena akcí každého uživatele přes fasetovou strukturu navigace zúžení výsledků hledání. Chcete-li zúžit výsledky, použijte výraz filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Vytvoření aplikace fasetová navigace
Implementovat fasetovou navigaci pomocí služby Azure Search v kódu aplikace, který vytváří žádost o vyhledávání. Fasetová navigace spoléhá na elementy ve schématu, který jste definovali dříve.

Předdefinované při hledání index je `Facetable [true|false]` atribut indexu, nastavte pro vybraná pole k povolení nebo zakázání jejich použití ve struktuře Fasetové navigace. Bez `"Facetable" = true`, pole nelze použít v omezující vlastnost navigace.

Prezentační vrstva ve vašem kódu obsahuje uživatelské prostředí. Zveřejnit částí Fasetové navigace, jako je například popisku, hodnot, zaškrtávací políčka a počet. Rozhraní REST API Azure Search je pro více platforem, proto používat jakýkoli jazyk a platformu, která chcete. Důležité je zahrnují prvky uživatelského rozhraní, které podporují přírůstkové aktualizace s aktualizovaný stav uživatelského rozhraní je vybrána každá další omezující vlastnost. 

V době zpracování dotazu kódu aplikace vytvoří žádost, která zahrnuje `facet=[string]`, parametr požadavku, která obsahuje pole, které má omezující vlastnost podle. Dotaz může mít více omezující vlastnosti, jako například `&facet=color&facet=category&facet=rating`, každý z nich oddělených ampersandem (&) znaků.

Kód aplikace musí také sestavit `$filter` výraz pro zpracování událostí kliknutím na Fasetové navigace. A `$filter` snižuje ve výsledcích hledání pomocí hodnota omezující vlastnosti jako kritéria filtru.

Azure Search vrací výsledky hledání, založené na jeden nebo více výrazů, které zadáte, spolu s aktualizací struktuře Fasetové navigace. Ve službě Azure Search Fasetové navigace je konstrukce jedné úrovně, se hodnoty omezujících vlastností, počty a kolik výsledků se nacházejí pro každou z nich.

V následujících částech se budeme podrobněji podíváme na tom, jak vytvořit jednotlivé části.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Sestavení indexu
"Faceting" je povolena na základě pole pomocí pole v indexu přes tento atribut indexu: `"Facetable": true`.  
Jsou všechny typy polí, které může pravděpodobně použijí v Fasetové navigace `Facetable` ve výchozím nastavení. Zahrnout tyto typy polí `Edm.String`, `Edm.DateTimeOffset`a všechny typy číselné pole (v podstatě všechny typy polí jsou facetable s výjimkou `Edm.GeographyPoint`, který nelze použít v Fasetové navigace). 

Při vytváření indexu, je osvědčeným postupem pro fasetovou navigaci k explicitnímu vypnutí "faceting" pro pole, která byste nikdy neměli používat jako omezující vlastnost.  Pole řetězců pro hodnoty typu singleton, jako je například název ID nebo produktu, zejména, musí být nastavená na `"Facetable": false` zabránit jejich použití náhodného (a neefektivní) v Fasetové navigace. Zapnutí "faceting" vypnout kde ho nepotřebujete pomáhá udržet co nejmenší velikost indexu a obvykle zvyšuje výkon.

Toto je část schéma pro ukázkovou aplikaci portálu ukázkové úlohy oříznut některých atributů ke zmenšení velikosti:

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

Jak je vidět ve vzorku schématu `Facetable` je vypnuté pro pole řetězců, které by neměly sloužit jako omezující vlastnosti, jako je například ID hodnoty. Zapnutí "faceting" vypnout kde ho nepotřebujete pomáhá udržet co nejmenší velikost indexu a obvykle zvyšuje výkon.

> [!TIP]
> Jako osvědčený postup zahrnout úplnou sadu atributů indexu pro každé pole. I když `Facetable` je ve výchozím pro téměř všechna pole, záměrně nastavení každý atribut může pomoci při zvažování důsledky každé rozhodnutí schématu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Zkontrolujte data
Kvalita dat má přímý vliv na, jestli bude struktuře Fasetové navigace realizována podle očekávání. Ovlivní také snadné vytváření filtrů omezit sadu výsledků dotazu.

Pokud chcete omezující vlastnosti ve značce nebo cena každého dokumentu by měl obsahovat hodnoty *BrandName* a *ProductPrice* , které jsou platné, konzistentní a produktivní jako možnost filter.

Tady je pár připomenutí toho, jak procházení pro:

* Pro každé pole, které chcete omezující vlastnost podle položte si otázku, zda obsahuje hodnoty, které jsou vhodné jako filtry v usnadní nastavení hledání. Hodnoty by měl být krátký, popisný a dostatečně výrazný, ale nabízet možnost volby zrušte zaškrtnutí možnosti konkurenčních.
* Chybně napsaná slova nebo téměř odpovídající hodnoty. Pokud podmínka na barvy a hodnoty polí obsahují oranžovou a Ornage (chyba), obě by vyzvednutí omezující vlastností na základě pole barev.
* Smíšené případu textu můžete také způsobí zmatek v Fasetové navigace s orange a oranžová zobrazení jako dva různé hodnoty. 
* Jednotné i množné číslo verze stejné hodnoty může způsobit samostatné omezující vlastnost pro každý.

Jak si dokážete představit, co nejopatrněji přípravy dat je zásadní aspekt efektivní Fasetové navigace.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní
Zpět od prezentační vrstvy můžete odhalit další požadavky, které jinak může chybět a seznamte se s možností, které jsou nezbytné pro vyhledávání.

Z hlediska Fasetové navigace stránky webu nebo aplikace zobrazí struktuře Fasetové navigace, zjistí vstupu uživatele na stránce a vloží změněné prvky. 

Pro webové aplikace se AJAX běžně používá v prezentační vrstvě vzhledem k tomu je možné aktualizovat přírůstkové změny. Můžete také použít technologie ASP.NET MVC nebo jakékoli jiné platformě vizualizace, který se může připojit ke službě Azure Search prostřednictvím protokolu HTTP. Ukázkovou aplikaci odkazovat v rámci tohoto článku – **Azure Search úlohy portálu Demo** – je aplikace ASP.NET MVC.

Ve vzorku Fasetové navigace je součástí na stránce výsledků hledání. Následující příklad z `index.cshtml` stránky soubor ukázkové aplikaci ukazuje statický kód HTML strukturu pro zobrazování fasetovou navigaci na hledání výsledků. Seznam omezující vlastnosti sestavení nebo znovu sestavit dynamicky při odeslání hledaný termín, nebo zaškrtněte nebo zrušte zaškrtnutí omezující vlastnost.

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

Následující fragment kódu z `index.cshtml` stránky dynamicky sestaví HTML zobrazíte první podmínka, název firmy. Podobné funkce jako dynamicky vytvářet kód HTML pro jiné omezující vlastnosti. Každý omezující vlastnosti má popisek a počet, který zobrazuje počet položek pro výsledek omezující vlastnost nalezena.

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
> Při návrhu stránku výsledků hledání, nezapomeňte přidat mechanismus pro vymazání omezujících vlastností. Pokud přidáte zaškrtávací políčka, můžete snadno zobrazit jak zrušit filtry. Pro jiné rozložení může být nutné vzoru s popisem cesty nebo další kreativní přístup. Například v ukázkové aplikaci portál pro prohledávání úlohu můžete kliknout na `[X]` po vybrané omezující vlastnost zrušte omezující vlastnost.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Vytvoření dotazu
Kód, který napíšete pro tvorbu dotazů byste určit všechny části platného dotazu včetně vyhledávacích výrazech, omezující vlastnosti, filtry, bodovací profily – všechno umožňuje zformulujte podobnou žádost. V této části se podíváme na které je omezující vlastnosti umístit do dotazu a použití filtrů s omezujícími vlastnostmi k zajištění nižší výslednou sadu.

Všimněte si, že omezující vlastnosti jsou nedílnou součástí v této ukázkové aplikaci. Možnosti hledání na portálu ukázkové úlohy navržené s ohledem na Fasetové navigace a filtry. Toto umístění fasetovou navigaci na stránce ukazuje jeho význam. 

Příkladem je často dobrým místem, kde začít. Následující příklad z `JobsSearch.cs` soubor žádosti, která vytvoří omezující vlastnost navigace na základě obchodní název, umístění, typ účtování a minimální Salary sestavení. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr dotazu omezující vlastnost je nastavena na pole a v závislosti na datový typ, může být dále parametrizován čárkami oddělený seznam, který obsahuje `count:<integer>`, `sort:<>`, `interval:<integer>`, a `values:<list>`. Seznam hodnot se podporuje pro číselná data při nastavování rozsahů. Zobrazit [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) podrobnosti o použití.

Spolu s omezující vlastnosti by měl žádost formulované aplikace také vytvářet filtry můžete zúžit sadu Release candidate dokumentů na základě výběru hodnota omezující vlastnosti. Pro úložiště kol, Fasetové navigace poskytuje možné dotazy, jako jsou *barvy, výrobci a typech kol jsou k dispozici?*. Filtrování získáte odpovědi na otázky jako *které přesně kol jsou červené, horská kola, v tomto cena rozsah?*. Po kliknutí na "Red" k označení, že mají být zobrazeny pouze produkty Red zahrnuje další dotaz aplikace odesílá `$filter=Color eq ‘Red’`.

Následující fragment kódu z `JobsSearch.cs` stránku přidá vybrané obchodní název filtru Pokud vyberete hodnotu z omezující vlastnost název firmy.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="indexing-tips"></a>Indexování tipy
**Zlepšení efektivity indexu, pokud nechcete použít vyhledávací pole**

Pokud vaše aplikace používá výhradně Fasetové navigace (to znamená bez vyhledávacího pole), můžete označit pole jako `searchable=false`, `facetable=true` k vytvoření kompaktnějším indexu. Kromě toho indexování probíhá pouze na hodnoty celý omezujících vlastností, žádná dělením slov nebo indexování součásti hodnoty více slov.

**Zadejte pole, která může sloužit jako omezující vlastnosti**

Připomínáme, že schéma indexu určuje pole, která je možné použít jako omezující vlastnost. Za předpokladu, že pole je facetable, dotaz Určuje, která pole pro omezující vlastnosti podle. Pole, podle kterého se "faceting" obsahuje hodnoty, které se zobrazí pod popiskem. 

Hodnoty, které se zobrazí v rámci každého popisku se načítají z indexu. Například, pokud je pole omezující vlastnost *barva*, k dispozici pro další filtrování hodnoty jsou hodnoty pro toto pole – Red, Black a tak dále.

Pro číselné hodnoty typu DateTime pouze, můžete explicitně nastavit hodnoty v poli s omezující vlastnosti (například `facet=Rating,values:1|2|3|4|5`). Seznam hodnot je povolený pro tyto typy polí ke zjednodušení oddělení omezující vlastnost výsledky do souvislých rozsahů (buď oblastí na základě číselné hodnoty nebo časových období). 

**Ve výchozím nastavení můžete mít jenom jeden stupeň fasetová navigace** 

Jak je uvedeno, neexistuje žádné přímé podpory pro vnoření omezující vlastnosti v hierarchii. Ve výchozím nastavení Fasetové navigace ve službě Azure Search podporuje pouze jednu úroveň filtrů. Ale existuje alternativní řešení. Omezující vlastnost hierarchické struktury v můžete kódovat `Collection(Edm.String)` s jeden vstupní bod na hierarchii. Implementace tohoto řešení je nad rámec tohoto článku. 

### <a name="querying-tips"></a>Dotazování tipy
**Ověřování polí**

Pokud vytvoříte seznam omezující vlastnosti dynamicky podle nedůvěryhodným uživatelským vstupům, ověřte, že jsou platné názvy polí, Fasetové. Nebo při vytváření adres URL pomocí escape názvy `Uri.EscapeDataString()` v .NET nebo odpovídající cenou v vybranou platformu.

### <a name="filtering-tips"></a>Filtrování tipy
**Zvýší přesnost hledání s filtry**

Pomocí filtrů. Pokud se spoléháte na samostatně, rozklad může způsobit, že dokument, který se má vrátit, který nemá hodnotu přesné omezující vlastnost v některém z jeho polí. stačí hledaných výrazů.

**Zvýšit výkon hledání s filtry**

Filtry zúžit sadu Release candidate dokumentů pro vyhledávání a vyloučit z hodnocení. Pokud máte velké sady dokumentů, často pomocí selektivní omezující vlastnost procházení poskytuje lepší výkon.
  
**Filtrovat pouze fasetová pole**

V fasetová procházení chcete obvykle obsahují pouze dokumenty, které mají hodnota omezující vlastnosti v konkrétním poli (fasetová), ne kdekoli napříč všechna prohledatelná pole. Cílové pole přidáním filtru posiluje přesměrováním service k vyhledání pouze v poli fasetová pro odpovídající hodnotu.

**Trim omezující vlastnost výsledky s další filtry.**

Omezující vlastnost výsledky jsou dokumenty nalezen ve výsledcích hledání, které odpovídají omezující vlastnost termín. V následujícím příkladu se ve výsledcích hledání *cloud computingu*, 254 položky mají také *interní specifikace* jako typ obsahu. Položky nejsou nutně vzájemně vylučují. Pokud položka splňuje kritéria obou filtrů, počítá se v každém z nich. Tato duplikace je možné, kdy "faceting" na `Collection(Edm.String)` pole, které jsou často používána k implementaci označování dokumentu.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Obecně platí Pokud zjistíte, že omezující vlastnost výsledky jsou příliš velké konzistentně, doporučujeme přidat další filtry, které uživatelům udělit další možnosti pro zúžení hledání.

### <a name="tips-about-result-count"></a>Tipy k počet výsledků

**Omezit počet položek v omezující vlastnost navigace**

Pro každé fasetová pole v navigačním stromu je výchozí limit 10 hodnot. Toto výchozí nastavení dává smysl pro navigační strukturu, protože udržuje seznam hodnot na vhodnou velikostí. Přiřazení hodnoty ke zjištění můžete přepsat výchozí nastavení.

* `&facet=city,count:5` Určuje, že pouze prvních pět měst v horní části seřazené výsledky se vrátí jako výsledek omezující vlastnost. Vezměte v úvahu ukázkový dotaz s hledaný termín "letiště" a 32 shody. Pokud dotaz specifikuje `&facet=city,count:5`, pouze prvních pět jedinečný města s nejvíce dokumenty ve výsledcích hledání jsou zahrnuty ve výsledcích omezující vlastnost.

Všimněte si rozdílu mezi omezující vlastnost výsledky a výsledky hledání. Výsledky hledání jsou všechny dokumenty, které odpovídají dotazu. Omezující vlastnost výsledky jsou odpovídajících položek pro každý hodnota omezující vlastnosti. V příkladu výsledky hledání obsahují názvy měst, které nejsou v seznamu omezující vlastnost klasifikace (5 v našem příkladu). Výsledky, které filtrují prostřednictvím Fasetové navigace viditelná, když vymazat omezující vlastnosti, nebo zvolte jiné omezující vlastnosti kromě město. 

> [!NOTE]
> Diskuze o `count` při více než jeden typ může být matoucí. Následující tabulka nabízí stručný přehled použití termín v rozhraní API Azure Search, ukázkový kód a dokumentace. 

* `@colorFacet.count`<br/>
  V prezentaci kódu měli byste vidět počet parametrů na omezující vlastnosti, které slouží k zobrazení počtu výsledků omezující vlastnost. Ve výsledcích omezující vlastnost počet označuje počet dokumentů, které odpovídají na omezující vlastnost termín nebo rozsah.
* `&facet=City,count:12`<br/>
  Omezující vlastnost dotazu můžete nastavit počet na hodnotu.  Výchozí hodnota je 10, ale můžete jako adresu nastavit vyšší nebo nižší. Nastavení `count:12` získá horní 12 odpovídá ve výsledcích omezující vlastnost s počet dokumentů.
* `@odata.count`<br/>
  V odpovědi na dotaz tato hodnota označuje počet odpovídajících položek ve výsledcích hledání. V průměru je větší než součet všech výsledků omezující vlastnost kombinovat z důvodu přítomnosti položky, které odpovídají hledanému výrazu, ale žádné shody hodnota omezující vlastnosti.

**Získat počty ve výsledcích omezující vlastnost**

Když přidáte filtr do fasetová dotazu, můžete chtít zachovat příkaz omezující vlastnosti (například `facet=Rating&$filter=Rating ge 4`). Technicky vzato omezující vlastnost = hodnocení není potřeba, ale to opět počty hodnoty omezujících vlastností pro hodnocení 4 nebo vyšší. Například pokud dotaz obsahuje filtr pro větší nebo rovna hodnotě "4", klikněte na tlačítko "4" počty jsou vrácené za každé hodnocení, který je 4 a vyšší.  

**Ujistěte se, že získáte přesný omezující vlastnost počty**

Za určitých okolností může pro vás, že omezující vlastnost počty se neshodují s sad výsledků (viz [Fasetové navigace ve službě Azure Search (příspěvek na fóru)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Počty omezující vlastnost může být nesprávné vzhledem k architektuře horizontálního dělení. Každý index vyhledávání má více horizontálních oddílů a každý horizontální oddíl sestavy horní N omezující vlastnosti podle počtu dokumentu, který je následně se spojí dohromady do jediného výsledku. Pokud některé horizontální oddíly mnoho odpovídající hodnoty, zatímco jiné mají méně, můžete zjistit, že chybí některé hodnoty omezujících vlastností, nebo pod počítáním ve výsledcích.

I když toto chování může kdykoli změnit, pokud narazíte na toto chování ještě dnes, můžete alternativně vyřešit ho pomocí uměle nafouknutí počet:<number> na velké množství vynutit Úplné generování sestav z jednotlivých horizontálních oddílů. Pokud hodnota počtu: je větší než nebo rovna počtu jedinečných hodnot v poli, můžete je zaručeno, že přesné výsledky. Ale při vysoké počty dokumentů, je snížení výkonu, proto tuto možnost použijte, uvážlivě.

### <a name="user-interface-tips"></a>Tipy pro uživatelské rozhraní
**Přidejte popisky pro každé pole v navigačním panelu omezující vlastnost**

Popisky jsou obvykle definovány ve formátu HTML nebo formuláře (`index.cshtml` v ukázkové aplikaci). Neexistuje žádné rozhraní API ve službě Azure Search pro omezující vlastnost navigace popisky nebo další metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrovat podle rozsahu
"Faceting" nad oblastí hodnot je běžné požadavky aplikace hledání. Rozsahy jsou podporované pro číselná data a hodnoty data a času. Další informace o jednotlivých přístupů v [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search zjednodušuje tím, že poskytuje dva přístupy pro výpočty rozsah konstrukce rozsahu. Azure Search pro oba přístupy, vytvoří příslušné rozsahy na základě vstupů, které jste zadali. Například pokud zadáte hodnoty rozsahu 10 | 20 | 30, automaticky vytvoří rozsah 0 až 10, 10-20, 20 – 30. Aplikace můžete volitelně odebrat intervalech, které jsou prázdné. 

**Způsob 1: Použijte parametr interval**  
Nastavení cen omezující vlastnosti v přírůstcích po 10 USD, zadali byste: `&facet=price,interval:10`

**Způsob 2: Použijte seznam hodnot**  
Pro číselná data můžete použít seznam hodnot.  Vezměte v úvahu rozsah omezující vlastnosti `listPrice` pole, vykreslí následujícím způsobem:

  ![Seznam ukázkových hodnot][5]

Můžete určit rozsah omezující vlastnost jako na předchozím snímku obrazovky, použijte seznam hodnot:

    facet=listPrice,values:10|25|100|500|1000|2500

Každý rozsah je sestavena pomocí 0 jako výchozí bod, hodnota v seznamu jako koncový bod a potom ořízne předchozí rozsahu k vytvoření diskrétní intervalech. Azure Search dělá tyto věci jako součást Fasetové navigace. Není nutné napsat kód pro strukturování v každém intervalu.

### <a name="build-a-filter-for-a-range"></a>Vytvořit filtr pro rozsah
Chcete-li filtrovat dokumenty na základě rozsahu vyberete, můžete použít `"ge"` a `"lt"` filtrovat operátory ve dvou částí výrazu, který definuje koncové body rozsahu. Například pokud zvolíte rozsah 10 až 25 `listPrice` pole Filtr by `$filter=listPrice ge 10 and listPrice lt 25`. Ve vzorovém kódu používá výraz filtru **priceFrom** a **priceTo** parametrů pro nastavení koncových bodů. 

  ![Dotaz na rozsah hodnot][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrovat na základě vzdálenosti
Je běžné zobrazíte filtry, které pomáhají zvolit úložiště, restaurace nebo cíl v závislosti na umístění v blízkosti své aktuální polohy. Tento typ filtru může vypadat Fasetové navigace, je právě filtru. Jsme tady zmiňovat pro ty z vás, kteří hledají konkrétně pro implementaci Rady, jak tento problém konkrétního návrhu.

Existují dva geoprostorové funkce ve službě Azure Search **geo.distance** a **geo.intersects**.

* **Geo.distance** funkce vrací vzdálenost v kilometrech mezi dvěma body. Jeden bod je pole a druhá je konstantní předanou v rámci filtru. 
* **Geo.intersects** funkce vrátí hodnotu true, pokud danému bodu v rámci dané mnohoúhelníku. Bod je pole a mnohoúhelník je zadána jako konstanta seznam souřadnice předanou v rámci filtru.

Můžete najít příklady filtrů v [syntaxe výrazů OData (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Vyzkoušet ukázkovou verzi
Azure Search úlohy portálu Demo obsahuje příklady odkazované v tomto článku.

-   A testování pracovní si online ukázku v [Azure Search úlohy portálu Demo](https://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [Azure-Samples úložišti na Githubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Při práci s výsledky hledání, podívejte se na adresu URL pro změny v konstrukce dotazů. Tato aplikace se stane se připojí omezující vlastnosti k identifikátoru URI při výběru každé z nich.

1. Pokud chcete používat funkce mapování pro ukázkovou aplikaci, získat klíče k mapám Bing z [Dev Center pro mapy Bing](https://www.bingmapsportal.com/). Vložte ji místo stávající klíč v `index.cshtml` stránky. `BingApiKey` Nastavení `Web.config` soubor nepoužívá. 

2. Spusťte aplikaci. Volitelné prohlídku nebo dialogové okno zavřete.
   
3. Zadejte hledaný termín, jako je například "analytik" a klikněte na ikonu hledání. Rychle spustí dotaz.
   
   Ve výsledcích vyhledávání je také vrácen fasetovou strukturu navigace. Na stránce výsledků hledání fasetová navigační struktura zahrnuje počty pro každého výsledku omezující vlastnost. Vyberou se žádné omezující vlastnosti, proto se vrátí všechny odpovídající výsledky.
   
   ![Výsledky hledání před výběrem omezující vlastnosti][11]

4. Klikněte na název firmy, umístění nebo minimální Salary. Hodnota null na počáteční vyhledávání omezujících vlastností, ale přijmou na hodnotách, výsledky hledání jsou oříznut položek, které už neodpovídá.
   
   ![Výsledky hledání po výběru omezující vlastnosti][12]

5. Fasetová dotaz vymažete, takže můžete zkusit chování jiný dotaz, klikněte na tlačítko `[X]` po vybrané omezující vlastnosti vymazání omezující vlastnosti.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Další informace
Sledování [podrobné informace o Azure Search](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Na 45:25 není o tom, jak implementovat omezující vlastnosti na ukázku.

Další přehledy o Principy návrhu pro fasetovou navigaci doporučujeme na následujících odkazech:

* [Návrh a Fasetové vyhledávání](http://www.uie.com/articles/faceted_search/)
* [Vzory návrhu: Fasetová navigace](https://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: https://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

