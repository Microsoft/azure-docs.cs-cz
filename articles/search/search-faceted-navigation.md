---
title: Jak implementovat Fasetové navigace ve službě Azure Search | Microsoft Docs
description: Přidejte Fasetové navigace k aplikacím, které se integrují s Azure Search, hostované cloudové vyhledávací službu v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 3/10/2017
ms.author: heidist
ms.openlocfilehash: e00e875619e4ed6800f5739362ff0c52971f6f16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak v Azure Search implementovat fasetovou navigaci
Fasetové navigace je filtrační mechanismus, který poskytuje řízené samotným přejít k podrobnostem navigace ve vyhledávání aplikací. Termín "Fasetové navigace, může být obeznámeni, ale pravděpodobně ho před jste použili. Jak ukazuje následující příklad, Fasetové navigace není nic jiného než kategorie použito pro filtrování výsledků.

 ![Portál ukázkové úlohy vyhledávání systému Azure][1]

Fasetové navigace je alternativní vstupní bod pro vyhledávání. Nabízí vhodnou alternativu ručně zadáte komplexní vyhledávacích výrazech. Omezující vlastnosti můžete najít, co hledáte, a zajistit, že neobdržíte nulové výsledky. Jako vývojář omezující vlastnosti vám umožní vystavit nejužitečnější kritéria vyhledávání pro navigaci vaše vyhledávání souhrnu. V aplikacích online prodejní Fasetové navigace je často sestavena značky, oddělení (dětský vcítit), velikost, ceny, oblíbenosti a hodnocení. 

Implementace Fasetové navigace se liší mezi technologie vyhledávání. Ve službě Azure Search je Fasetové navigace vytvořená v době dotazů s využitím pole, které jste dříve s atributy ve schématu.

-   V dotazech, které vaše aplikace vytvoří, musíte odeslat dotaz *parametry dotazu omezující vlastnost* získat hodnoty filtru k dispozici omezující vlastnosti pro tuto sadu výsledků dokumentu.

-   Ve skutečnosti oříznout výsledek dokumentu nastavit, musíte také použít aplikaci `$filter` výraz.

Při vývoji vaší aplikace psaní kódu, který vytvoří dotazy se považuje za hromadné práce. Řadu chování aplikace, které by uživatel očekával od Fasetové navigace jsou poskytovány služby, včetně integrovanou podporu pro definování rozsahy a získávání počty omezující vlastnost výsledků. Služba také zahrnuje rozumný výchozí hodnoty, které vám pomůžou vyhnout nepraktické navigační struktury. 

## <a name="sample-code-and-demo"></a>Ukázkový kód a demo
Tento článek používá portál úlohy vyhledávání jako příklad. V příkladu je implementovaný jako aplikace ASP.NET MVC.

-   Zobrazit a testování ukázkové pracovní online v [Azure Search úlohy portál ukázku](http://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [Azure-Samples úložišti na Githubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Začínáme
Pokud jste nové hledání vývoj, je nejlepší způsob, jak si můžete představit Fasetové navigace, ukazuje možnosti řízené samotným hledání. Je typ procházení hledání založené na předdefinovaných filtrů, použít pro rychlé zužující dolů výsledky hledání prostřednictvím akce bodu a kliknutím. 

### <a name="interaction-model"></a>Interakce modelu

Možnosti hledání Fasetové navigace je iterativní, proto budeme, začněte tím, že vysvětlení jako posloupnost dotazy, které unfold v odpovědi na akce uživatele.

Výchozí bod je stránku aplikace, která poskytuje Fasetové navigace, obvykle umístěny na obvodu. Fasetové navigace je často stromová struktura, s zaškrtávací políčka pro každou hodnotu, nebo můžete kliknout text. 

1. Dotaz odeslaných do služby Azure Search určuje struktuře Fasetové navigace přes jeden nebo více parametrů dotazu omezující vlastnosti. Například může zahrnovat dotaz `facet=Rating`, případně s `:values` nebo `:sort` možnost pro další upřesnění prezentaci.
2. Prezentační vrstva vykreslí stránky vyhledávání, který poskytuje Fasetové navigace, pomocí omezujícími vlastnostmi zadanými v žádosti.
3. Zadané strukturu Fasetové navigace, která zahrnuje hodnocení, kliknutí na tlačítko "4" k označení, že by se měly zobrazit jenom produkty s hodnocení 4 nebo vyšší. 
4. V odpovědi aplikace odešle dotaz, který zahrnuje `$filter=Rating ge 4` 
5. Prezentační vrstva aktualizací stránky zobrazující snížené výslednou sadu, obsahující pouze tyto položky, které splňují nová kritéria (v tomto případě produkty hodnocení 4 a vyšší).

Omezující vlastnost je parametr dotazu, ale Nezaměňujte se vstupem dotazu. Se nikdy nepoužívá jako kritéria pro výběr v dotazu. Místo toho představit omezující vlastnost parametry dotazu jako vstupy pro navigační strukturu, která se dodává zpět v odpovědi. Pro každý parametr dotazu omezující vlastnosti, které poskytnete vyhodnotí Azure Search, kolik dokumenty jsou v částečné výsledky pro každou hodnotu omezující vlastnosti.

Upozornění `$filter` v kroku 4. Filtr je důležitým aspektem Fasetové navigace. I když omezující vlastnosti a filtry jsou nezávislé v rozhraní API, je nutné i poskytující možnosti, které máte v plánu. 

### <a name="app-design-pattern"></a>Vzor návrhu aplikace

Vzor kód aplikace, je použít omezující vlastnost parametry dotazu, který vrátí struktuře Fasetové navigace spolu s výsledky omezující vlastnost plus výraz $filter.  Výraz filtru zpracovává událost kliknutím na je hodnota. Představte si, že `$filter` výraz kódu skutečné oříznutí výsledků vyhledávání vrátí prezentační vrstvy. Zadána omezující vlastnost barvy, klikněte na červenou barvu se implementuje pomocí `$filter` výraz, který vybere jen ty položky, které mají barva red. 

### <a name="query-basics"></a>Základy dotazů

Ve službě Azure Search, požadavek se specifikuje prostřednictvím jeden nebo více parametrů dotazu (viz [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) popis každé z nich). Žádný z parametrů dotazu jsou požadovány, ale musí mít alespoň jeden v pořadí pro dotaz platný.

Přesnost, porozuměl jsem jim jako možnost filtrování důležité přístupů je dosaženo pomocí jednoho nebo obou těchto výrazů:

-   **hledání =**  
    Hodnota tohoto parametru se považuje za hledaný výraz. Může být jediný text nebo výraz komplexní vyhledávání, který obsahuje více podmínek a operátory. Na serveru je použit výraz vyhledávání pro fulltextové vyhledávání, dotazování prohledatelná pole v indexu pro párování podmínky, vrátí výsledky v rank pořadí. Pokud nastavíte `search` na hodnotu null, dotaz zpracování se přes celý index (tedy `search=*`). V tomto případě další prvky dotazu, jako například `$filter` nebo vyhodnocování profil, jsou primární faktory, které mají vliv na dokumenty, které jsou vráceny `($filter`) a pořadí, v jakém (`scoringProfile` nebo `$orderby`).

-   **$filter =**  
    Filtr je výkonný mechanismus pro omezení velikosti výsledků vyhledávání na základě hodnot atributů určitého dokumentu. A `$filter` je první vyhodnocen, za nímž následuje používání omezujících vlastností logiky, která generuje dostupné hodnoty a odpovídající počty pro každou hodnotu

Komplexní vyhledávacích výrazech snížit výkon dotazu. Pokud je to možné, zvýšit přesnost a zlepšit výkon dotazů pomocí dobře sestavený filtru výrazů.

Abyste lépe pochopili, jak filtr přidá další přesnost, porovnejte výraz komplexní vyhledávání, které obsahuje výraz filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Obě dotazy jsou platné, ale druhý je vyšší, pokud hledáte bez motely s parkovací v Praze.
-   První dotaz závisí na těchto určitá slova se uvedených nebo není uveden v polí s řetězcem jako název, popis a žádné jiné pole obsahující prohledávatelná data.
-   Druhý dotaz hledá přesné shody na strukturovaných dat a může být mnohem přesnější.

V aplikacích, které zahrnují Fasetové navigace, ujistěte se, že je přiložena akce každého uživatele v struktuře Fasetové navigace zúžení výsledků vyhledávání. Chcete-li zúžit výsledky, použijte výraz filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Sestavení aplikace Fasetové navigace
V kódu aplikace, vytvoří žádost o vyhledávání implementujete Fasetové navigace s Azure Search. Fasetové navigace spoléhá na elementy ve vašem schématu, které jste definovali dříve.

Předdefinované v hledání index je `Facetable [true|false]` atribut index, nastavte na vybrané pole k povolení nebo zakázání jejich použití ve struktuře Fasetové navigace. Bez `"Facetable" = true`, pole nelze použít v omezující vlastnost navigace.

Prezentační vrstvy do vašeho kódu nabízí uživatelské prostředí. By měl zobrazit seznam základních součástí Fasetové navigace, například štítek, hodnoty, zaškrtněte políčka a počet. REST API služby Azure Search je nezávislá na platformě, takže použijte jakoukoli jazyk a platformy, které chcete. Je důležité zahrnout prvky uživatelského rozhraní, které podporují přírůstkové aktualizace s aktualizovaný stav uživatelského rozhraní, protože každý další omezující vlastnost je vybrána. 

V době dotazů kódu aplikace vytvoří žádost, která zahrnuje `facet=[string]`, parametr požadavek, který obsahuje pole, které chcete omezující vlastnosti podle. Dotaz může mít více omezující, jako například `&facet=color&facet=category&facet=rating`, každé z nich oddělených ampersand (&) znaků.

Kód aplikace také musí vytvořit `$filter` výraz pro zpracování události kliknutí na v Fasetové navigace. A `$filter` snižuje výsledky hledání, pomocí hodnoty omezující vlastnost jako kritéria filtru.

Služba Azure Search vrátí výsledky hledání, založené na jeden nebo více výrazů, které zadáte, společně s aktualizace struktuře Fasetové navigace. Ve službě Azure Search Fasetové navigace je konstrukce jedna úroveň, s hodnotami omezující vlastnost, počty a kolik výsledků, které se nacházejí pro každé z nich.

V následujících částech se budeme trvat bližší pohled na jak sestavit každou část.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Sestavení indexu
Používání omezujících vlastností je povolena na základě pole pole v indexu, prostřednictvím tohoto atributu indexu: `"Facetable": true`.  
Všechny typy pole, které by mohly být použity pravděpodobně v Fasetové navigace jsou `Facetable` ve výchozím nastavení. Zahrnout tyto typy polí `Edm.String`, `Edm.DateTimeOffset`a všechny typy číselné pole (v podstatě všechny typy polí jsou facetable (kategorizovatelné) s výjimkou `Edm.GeographyPoint`, který nelze použít v Fasetové navigace). 

Při vytváření indexu, je vhodné, Fasetové navigace explicitně vypnout používání omezujících vlastností pro pole, která má být nikdy použit jako omezující vlastnost.  Konkrétně polí s řetězcem hodnoty typu singleton, jako je například ID nebo produktu, název, musí být nastavena na `"Facetable": false` zabránit jejich náhodnému (a neúčinná) využití v Fasetové navigace. Zapnutí používání omezujících vlastností vypnout kde nepotřebujete ho pomáhá udržet co nejmenší velikost indexu a obvykle zvyšuje výkon.

Toto je součástí schéma pro ukázkovou aplikaci portálu ukázkové úlohy oříznut některých atributů ke snížení velikosti:

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

Jak je vidět ve schématu ukázka `Facetable` je vypnuté pro pole řetězce, které by se neměla používat jako omezující, jako je třeba ID hodnoty. Zapnutí používání omezujících vlastností vypnout kde nepotřebujete ho pomáhá udržet co nejmenší velikost indexu a obvykle zvyšuje výkon.

> [!TIP]
> Jako osvědčený postup zahrnovat kompletní atributy indexu pro každé pole. I když `Facetable` je ve výchozím skoro všech polí, účelově nastavení každý atribut vám může pomoct pečlivě promyslete důsledky každé rozhodnutí schématu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Zkontrolujte, zda data
Kvality dat. má přímý vliv na tom, jestli bude realizována struktuře Fasetové navigace, jak očekáváte. Ovlivní také snadné vytváření filtrů ke snížení sadu výsledků dotazu.

Pokud budete chtít omezující vlastnost Brand nebo ceny, musí každý dokument obsahovat hodnoty pro *značka* a *ProductPrice* které jsou platné, konzistentní a produktivitu jako možnost filter.

Tady je několik připomenutí co mohou procházet nástroje pro:

* Pro každé pole, které chcete omezující vlastnosti podle položte si zda obsahuje hodnoty, které jsou vhodné jako filtry řízené samotným vyhledávání. Hodnoty musí být krátký, popisný a dostatečně odlišný a nabídnout jasná volba mezi konkurence mezi možnostmi.
* Pravopisné chyby nebo téměř odpovídající hodnoty. Pokud jste omezující vlastnost barvy a hodnoty polí obsahovat oranžová a Ornage (chybné), obě by vyzvednutí omezující vlastnost na základě pole barev.
* Smíšená případu textu můžete také způsobí zmatek v Fasetové navigace s oranžová a oranžová zobrazí jako dvě různé hodnoty. 
* Verze jednoho a množném čísle stejné hodnoty může způsobit samostatné omezující vlastnosti pro každou.

Jak si lze představit, péči k přípravě dat je zásadní aspekt efektivní Fasetové navigace.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní
Pracovní zpět od prezentační vrstvy můžete odkrýt požadavky, které může být načteni jinak a pochopit, jaké funkce jsou nezbytné pro možnosti vyhledávání.

Z hlediska Fasetové navigace stránku webu nebo aplikace zobrazí struktuře Fasetové navigace, zjistí vstup uživatele na stránce a vloží změněné elementy. 

Pro webové aplikace se AJAX obvykle používá v prezentační vrstvě protože umožňuje aktualizaci přírůstkové změny. Můžete také použít rozhraní ASP.NET MVC nebo jakoukoli jinou vizualizaci platformu, zda se může připojit k službě Azure Search pomocí protokolu HTTP. Ukázkovou aplikaci odkazuje v tomto článku – **Azure Search úlohy portál ukázku** – se stane se aplikace ASP.NET MVC.

V ukázce Fasetové navigace je integrovaná stránky s výsledky hledání. Následující příklad, provedených od `index.cshtml` stránka s výsledky souboru ukázkové aplikace, struktura ukazuje statické HTML pro zobrazení Fasetové navigace v hledání. Seznam omezující vlastnosti je sestavit nebo znovu sestavit dynamicky při odeslání hledaný termín, nebo zaškrtněte nebo zrušte omezující vlastnost.

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

Následující fragment kódu z `index.cshtml` stránky dynamicky vytvoří HTML zobrazíte první omezující vlastnost název firmy. Podobné funkce jako dynamicky sestavení HTML pro jiné omezující vlastnosti. Každý omezující vlastnosti má popisek a počet, který zobrazí počet položek pro tento výsledek omezující vlastnost nalezena.

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
> Při návrhu stránky s výsledky hledání, nezapomeňte přidat mechanismus pro vymazání omezující vlastnosti. Pokud přidáte zaškrtávací políčka, zobrazí se snadno vymazání filtry. Pro jiné rozložení může být nutné vzor s popisem cesty nebo další tvůrčí přístup. Například v ukázkové aplikaci úlohy vyhledávání portál, můžete kliknout na `[X]` po vybrané omezující vlastnost zrušte omezující vlastnost.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Sestavení dotazu
Kód, který zapisuje pro tvorbu dotazů musí určovat všech součástí platný dotaz, včetně vyhledávacích výrazech, omezující vlastnosti, filtrů, vyhodnocování profilů – nic používá k formulovali žádost. V této části jsme zjistit, kde omezující vlastnosti začlenit do dotazu, a způsobu použití filtrů s omezujícími vlastnostmi k poskytování snížené výslednou sadu.

Všimněte si, že omezující vlastnosti jsou nedílnou součástí v této ukázkové aplikaci. Možnosti vyhledávání v ukázce portál úlohy je navržen na Fasetové navigace a filtry. Výrazné umístění Fasetové navigace na stránce ukazuje její význam. 

Příkladem je často dobrým místem, kde začít. Následující příklad, provedených od `JobsSearch.cs` souboru sestavení požadavek, který vytvoří omezující vlastnosti navigace na základě obchodní název, umístění, typ účtování a minimální mzda. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr dotazu omezující vlastnost nastavena na pole a v závislosti na typu dat, lze nastavit další parametry podle seznamu odděleného čárkami, který zahrnuje `count:<integer>`, `sort:<>`, `interval:<integer>`, a `values:<list>`. Seznam hodnot je podporována pro číselná data při nastavování rozsahů. V tématu [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) podrobnosti o použití.

Společně s omezujícími vlastnostmi musí požadavek formulovali vaše aplikace také vytvořit filtry můžete zúžit sady candidate dokumentů na základě výběru hodnota omezující vlastnosti. Pro úložiště kolo Fasetové navigace poskytuje různá vodítka na otázky, jako *jaké typy kol, výrobců a barvy jsou k dispozici?*. Filtrování odpovědi na dotazy jako *red které přesný kol se Horská kola, v tomto cena rozsah?*. Když kliknete na tlačítko "Red" k označení, že by se měly zobrazit jenom Red produkty, zahrnuje další dotaz aplikace odešle `$filter=Color eq ‘Red’`.

Následující fragment kódu z `JobsSearch.cs` stránky přidá vybrané obchodní název filtru, pokud vyberete hodnotu z omezující vlastnost název firmy.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="indexing-tips"></a>Tipy pro indexování
**Pokud nepoužijete vyhledávací pole zlepšit efektivitu indexu**

Pokud vaše aplikace používá výhradně Fasetové navigace (tedy bez vyhledávacího pole), můžete označit pole jako `searchable=false`, `facetable=true` k vytvoření kompaktnější index. Kromě toho indexování proběhne pouze celý omezující vlastnosti hodnoty, ovšem se žádné dělením slov nebo indexování součásti aplikace word hodnoty.

**Zadejte pole, která lze použít jako omezující vlastnosti**

Odvolat, aby schéma indexu určuje pole, která jsou k dispozici pro použití jako omezující vlastnost. Za předpokladu, že je pole facetable (kategorizovatelné), dotaz Určuje, která pole je vhodné omezující vlastnosti podle. Pole, podle kterého jsou používání faset obsahuje hodnoty, které se zobrazí pod popiskem. 

Hodnoty, které se zobrazí pod každý popisek se načítají z indexu. Například, pokud je pole omezující vlastnost *barva*, k dispozici pro další filtrování hodnoty jsou hodnoty pro toto pole - červená, černé a tak dále.

Číselné a data a času pouze hodnoty, je možné explicitně nastavit hodnoty v poli omezující vlastnosti (například `facet=Rating,values:1|2|3|4|5`). Seznam hodnot je povolena pro tyto typy polí pro zjednodušení oddělení omezující vlastnost výsledky do sousedních rozsahy (buď na základě číselných hodnot nebo časových období rozsahy). 

**Ve výchozím nastavení může mít pouze jednu úroveň Fasetové navigace** 

Jak jsme uvedli, neexistuje žádné přímé podpory pro vnoření omezující vlastnosti v hierarchii. Ve výchozím nastavení Fasetové navigace ve službě Azure Search podporuje pouze jednu úroveň filtry. Ale existuje alternativní řešení. Můžete kódovat omezující vlastnost hierarchická struktura v `Collection(Edm.String)` v jedné položce bodu na hierarchii. Implementace tohoto řešení je nad rámec tohoto článku. 

### <a name="querying-tips"></a>Dotazování tipy
**Ověřte pole**

Pokud vytvoříte seznam omezující vlastnosti dynamicky podle nedůvěryhodné uživatelský vstup, ověřte, zda jsou názvy polí Fasetové platný. Nebo řídicí názvy při vytváření adres URL pomocí `Uri.EscapeDataString()` v rozhraní .NET, nebo jeho ekvivalent v vaši platformu výběru.

### <a name="filtering-tips"></a>Filtrování tipy
**Zvýšit přesnost vyhledávání s filtry**

Použití filtrů. Pokud byste tedy spoléhat na právě vyhledávacích výrazech samostatně, rozklad by mohlo způsobit, že dokument, který se má vrátit, který nemá je přesné hodnota v některém z jeho polí.

**Zvýšení výkonu vyhledávání s filtry**

Filtry zúžit sady dokumentů kandidáta pro hledání a vyloučit z hodnocení. Pokud máte velké sady dokumentů, často pomocí selektivní omezující vlastnost procházení poskytuje lepší výkon.
  
**Filtrovat jenom Fasetové pole**

V Fasetové procházení chcete obvykle obsahovat jenom dokumenty, které mají je hodnota v určitém poli (Fasetové), všechna prohledatelná pole není kdekoli. Cílové pole přidáním filtru se zvyšuje přesměrováním službu, kterou chcete hledat pouze v Fasetové pole pro odpovídající hodnotu.

**Trim omezující vlastnost výsledků s další filtry**

Omezující vlastnost výsledky jsou dokumenty nalezen ve výsledcích hledání, které odpovídají termín omezující vlastnosti. V následujícím příkladu, ve výsledcích hledání *cloud computing*, 254 položky mají také *interní specifikace* jako typ obsahu. Položky nejsou nezbytně vzájemně vylučují. Pokud položku splňuje kritéria obou filtrů, se počítá v každé z nich. Tato duplikace je možné při používání omezujících vlastností na `Collection(Edm.String)` pole, které se často používají k implementaci označování dokumentu.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Obecně platí Pokud zjistíte, že výsledky omezující vlastnost konzistentně příliš velký, doporučujeme přidat další filtry uživatelům další možnosti pro zúžení hledání.

### <a name="tips-about-result-count"></a>Tipy k počet výsledků

**Omezit počet položek v navigačním panelu omezující vlastnost**

Pro každé Fasetové pole v navigačním stromu je výchozí limit 10 hodnot. Toto výchozí nastavení má smysl pro navigační struktury, protože udržuje seznam hodnot spravovat velikost. Přiřazením hodnoty počítat můžete přepsat výchozí nastavení.

* `&facet=city,count:5` Určuje, že pouze měst prvních pět najít v horní části seřazeny výsledky se vrátí jako výsledek omezující vlastnosti. Vezměte v úvahu ukázkový dotaz s hledaný termín "letiště" a odpovídá 32. Pokud dotaz Určuje `&facet=city,count:5`, jsou zahrnuty pouze prvních pět jedinečný města s největším dokumenty ve výsledcích hledání ve výsledcích omezující vlastnosti.

Všimněte si rozdíl mezi omezující vlastnost výsledky a výsledky hledání. Výsledky hledání jsou všechny dokumenty, které odpovídají dotazu. Omezující vlastnost výsledky jsou odpovídá pro každou hodnotu omezující vlastnosti. V příkladu se výsledky hledání obsahují města názvy, které nejsou v seznamu omezující vlastnost klasifikace (v našem příkladu 5). Po vymazání omezující vlastnosti, nebo zvolte jiné omezující vlastnosti kromě města se vidět výsledky, které jsou odfiltrována prostřednictvím Fasetové navigace. 

> [!NOTE]
> Hovoříte o `count` při více než jeden typ může být matoucí. Následující tabulka nabízí stručné souhrnné informace o použití termín v rozhraní API služby Azure Search, ukázky kódu a dokumentaci. 

* `@colorFacet.count`<br/>
  V kódu prezentace měli byste vidět počet parametrů na omezující vlastnosti, které slouží k zobrazení počet výsledků omezující vlastnosti. Ve výsledcích omezující vlastnost počet označuje počet dokumentů, které odpovídají na omezující vlastnost termín nebo rozsah.
* `&facet=City,count:12`<br/>
  V dotazu omezující vlastnosti můžete nastavit počet na hodnotu.  Výchozí hodnota je 10, ale můžete nastavit vyšší nebo nižší. Nastavení `count:12` získá horní 12 odpovídá ve výsledcích omezující vlastnosti podle počtu dokumentu.
* `@odata.count`<br/>
  V odpovědi na dotaz tato hodnota určuje počet odpovídajících položek ve výsledcích hledání. V průměru je větší než součet výsledků omezující vlastnost kombinaci z důvodu přítomnosti položky, které odpovídají hledaný termín, ale obsahuje žádné odpovídající hodnota omezující vlastnosti.

**Získat ve výsledcích omezující vlastnost**

Když přidáte filtr k Fasetové dotazu, můžete chtít zachovat příkaz omezující vlastnosti (například `facet=Rating&$filter=Rating ge 4`). Technicky omezující vlastnost = hodnocení není vyžadována, ale bude vrací počet hodnot omezující vlastnosti pro hodnocení, 4 a vyšší. Například pokud kliknete na tlačítko "4" a dotaz obsahuje filtr pro větší nebo rovna hodnotě "4", jsou počty, vrácený pro každý hodnocení, který je 4 a vyšší.  

**Ujistěte se, že můžete získat přesné omezující vlastnost**

Za určitých okolností, můžete zjistit, že počty omezující vlastnost neodpovídají sad výsledků dotazu (viz [Fasetové navigace ve službě Azure Search (příspěvek na fóru)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Počty omezující vlastnost může být nesprávné vzhledem k architektuře horizontálního dělení. Každý index vyhledávání má víc horizontálních oddílů a každý horizontálního oddílu sestavy omezující vlastnosti top N podle počtu dokumentu, který je pak zkombinované do jednoho výsledku. Pokud některé horizontálních oddílů mnoho odpovídající hodnoty, zatímco ostatní uživatelé mají méně, můžete zjistit, zda některé hodnoty omezující vlastnost chybí a v části započítány ve výsledcích.

I když toto chování změnit kdykoli, pokud dojde k tomuto chování dnes, můžete alternativně vyřešit ji pomocí uměle nafouknutí počet:<number> na velký počet vynutit Úplné generování sestav z každé horizontálního oddílu. Pokud hodnota počtu: je větší než nebo rovná počet jedinečných hodnot v poli, že je zaručeno přesné výsledky. Ale pokud jsou vysoké počty dokumentů, je snížení výkonu, proto tuto možnost použijte uvážlivě.

### <a name="user-interface-tips"></a>Tipy pro uživatelské rozhraní
**Přidání popisky pro každé pole v omezující vlastnosti navigace**

Popisky jsou obvykle definovány v HTML nebo formátu (`index.cshtml` v ukázkové aplikaci). Neexistuje žádné rozhraní API ve službě Azure Search pro omezující vlastnosti navigace popisky nebo další metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtr na základě rozsahu
Používání faset přes rozsah hodnot je běžné aplikace požadavek vyhledávání. Rozsahy jsou podporovány pro číselná data a hodnoty DateTime. Další informace o jednotlivých přístupů v [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Vyhledávání systému Azure zjednodušuje vytváření rozsahu tím, že poskytuje dva přístupy pro výpočty rozsah. Pro oba přístupy vytvoří Azure Search příslušné rozsahy zadané vstupních hodnot, které jste zadali. Například pokud zadáte hodnoty rozsahu 10 | 20 | 30, automaticky vytvoří rozsahy 0-10, 10 20, 20-30. Aplikace můžete volitelně odebrat žádné intervaly, které jsou prázdné. 

**Způsob 1: Použijte parametr interval**  
Pokud chcete nastavit omezující vlastnosti ceníku v přírůstcích po 10, zadali byste: `&facet=price,interval:10`

**Způsob 2: Použijte seznam hodnot**  
Číselná data můžete seznam hodnot.  Vezměte v úvahu rozsah omezující vlastnosti `listPrice` pole, vykresluje následujícím způsobem:

  ![Seznam ukázek hodnoty][5]

Pokud chcete zadat rozsah omezující vlastnosti jako v předchozí snímek obrazovky, použijte seznam hodnot:

    facet=listPrice,values:10|25|100|500|1000|2500

Každý rozsah je sestaven pomocí 0 jako počáteční bod, hodnotu ze seznamu jako koncový bod a pak oříznut předchozí rozsahu vytvořit samostatné intervaly. Vyhledávání systému Azure se provádí tyto věci jako součást Fasetové navigace. Nemáte napsat kód pro vytvoření struktury v každém intervalu.

### <a name="build-a-filter-for-a-range"></a>Vytvořit filtr pro rozsah
Chcete-li filtrovat na základě rozsahu vyberete dokumenty, můžete použít `"ge"` a `"lt"` filtrovat operátory ve dvou částech výraz, který definuje koncové body rozsahu. Například pokud se rozhodnete rozsah 10 až 25 `listPrice` pole Filtr by `$filter=listPrice ge 10 and listPrice lt 25`. Ukázkový kód používá výraz filtru **priceFrom** a **priceTo** parametry nastavit koncové body. 

  ![Dotazy na rozsah hodnot][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrovat na základě vzdálenosti
Běžné jeho zobrazíte filtry, které pomáhají zvolit úložiště, restaurace nebo cíl v závislosti na jeho blízkosti vaše aktuální umístění. Tento typ filtru může vypadat například Fasetové navigace, je právě filtru. Jsme zmínili ji sem pro těch, které jste, který hledáte konkrétně implementace Rady, jak tento problém konkrétního návrhu.

Ve službě Azure Search jsou dvě funkce geoprostorové **geo.distance** a **geo.intersects**.

* **Geo.distance** funkce vrací vzdálenost v kilometrech mezi dva body. Jeden bod je pole a druhá je konstanta předanou v rámci filtru. 
* **Geo.intersects** funkce vrátí hodnotu true, pokud k danému bodu je v rámci dané mnohoúhelníku. Bod je pole a mnohoúhelníku je zadán jako seznam souřadnice předanou v rámci filtru konstantní.

Můžete najít příklady filtru v [syntaxe výrazu OData (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Vyzkoušet ukázkovou verzi
Ukázkový portál Azure Search úlohy obsahuje příklady odkazuje v tomto článku.

-   Zobrazit a testování ukázkové pracovní online v [Azure Search úlohy portál ukázku](http://azjobsdemo.azurewebsites.net/).

-   Stáhněte si kód z [Azure-Samples úložišti na Githubu](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Při práci s výsledky hledání, podívejte se na adresu URL pro změny v dotazu konstrukce. Tato aplikace se stane se připojí omezující vlastnosti k identifikátoru URI při výběru každé z nich.

1. Pokud chcete používat mapování funkce ukázkovou aplikaci, získat mapy Bing klíč z [Dev Center pro mapy Bing](https://www.bingmapsportal.com/). Vložte přes existující klíč v `index.cshtml` stránky. `BingApiKey` Nastavení v `Web.config` soubor se nepoužije. 

2. Spusťte aplikaci. Volitelné prohlídku nebo zavřít dialogové okno.
   
3. Zadejte hledaný termín, jako je například "analytik" a klikněte na ikonu hledání. Rychle se provede dotaz.
   
   Struktura Fasetové navigace je také vrácené ve výsledcích hledání. Ve výsledcích hledání zahrnuje struktuře Fasetové navigace počty pro každý výsledek omezující vlastnosti. Žádné omezující vlastnosti jsou vybrané, takže jsou vráceny všechny odpovídající výsledky.
   
   ![Výsledky hledání před výběrem omezující vlastnosti][11]

4. Klikněte na název firmy, umístění nebo minimální mzda. Omezující vlastnost měla hodnotu null u počáteční hledání, ale kdy přijmou na hodnotách, výsledky hledání jsou oříznut položek, které už neodpovídá.
   
   ![Výsledky hledání po výběru omezující vlastnosti][12]

5. Chcete-li zrušit Fasetové dotazu, takže můžete zkusit chování různých dotazu, klikněte na tlačítko `[X]` po vybrané omezující vlastnosti zrušte omezující vlastnosti.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Další informace
Kukátko [Deep Dive informace o službě Azure Search](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). V 45:25 je ukázku na tom, jak implementovat omezující vlastnosti.

Pro další statistiky na zásadách návrhu pro fasetovou navigaci doporučujeme následující odkazy:

* [Návrh a Fasetové vyhledávání](http://www.uie.com/articles/faceted_search/)
* [Vzory návrhu: Fasetové navigace](http://alistapart.com/article/design-patterns-faceted-navigation)


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
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

