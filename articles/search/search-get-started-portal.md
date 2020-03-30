---
title: Vytvoření indexu vyhledávání na webu Azure Portal
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu na webu Azure Portal použijte Průvodce importem dat k vytvoření, načtení a dotazování na první index vyhledávání v Azure Cognitive Search.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 8324ca0184c508591fa4568175bad0f606f952a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369455"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search na webu Azure Portal
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Pomocí průvodce **importem dat** na portálu a nástrojů **průzkumníka vyhledávání** můžete rychle rozjet koncepty a během několika minut napsat zajímavé dotazy proti indexu.

Pokud jsou nástroje příliš omezující, můžete zvážit [úvod založený na kódu programování Azure Cognitive Search v rozhraní .NET](search-howto-dotnet-sdk.md) nebo použít [Postman pro volání rozhraní REST API](search-get-started-postman.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="prerequisites"></a>Požadavky

[Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu. 

### <a name="check-for-space"></a>Kontrola místa

Mnoho zákazníků začíná s bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden.

Oddíly na řídicím panelu služby ukazují, kolik indexů, indexerů a zdrojů dat již máte. 

![Seznamy indexů, indexerů a zdrojů dat](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy provádějí iterace [*indexu*](search-what-is-an-index.md), který obsahuje data s možností vyhledávání, metadata a další konstrukce, které optimalizují určité chování vyhledávání.

Pro účely tohoto kurzu používáme vestavěnou ukázkovou datovou sadu, kterou lze procházet pomocí [*indexeru*](search-indexer-overview.md) pomocí Průvodce [ **importem dat** ](search-import-data-portal.md). Indexer je prohledávací modul pro určitý zdroj, který dokáže číst metadata a obsah z podporovaných zdrojů dat Azure. Indexery se obvykle používají programově, ale na portálu k nim můžete přistupovat prostřednictvím Průvodce **importem dat.** 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 – Spuštění Průvodce importem dat a vytvoření zdroje dat

1. Na řídicím panelu služby Azure Cognitive Search klikněte na **importovat data** na panelu příkazů a vytvořte a naplníte index hledání.

   ![Příkaz pro import dat](media/search-get-started-portal/import-data-cmd.png)

2. V průvodci klikněte na **Připojit k vašim datům** > **Ukázky** > **hotelů**. Tento zdroj dat je vestavěný. Pokud jste vytvářeli vlastní zdroj dat, bylo nutné zadat název, typ a informace o připojení. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

   ![Výběr ukázkové datové sady](media/search-get-started-portal/import-datasource-sample.png)

3. Pokračujte na další stránku.

### <a name="step-2---skip-the-enrich-content-page"></a>Krok 2 – Přeskočení stránky "Obohatit obsah"

Průvodce podporuje vytvoření [kanálu obohacení AI](cognitive-search-concept-intro.md) pro začlenění algoritmů AI cognitive services do indexování. 

Tento krok prozatím přeskočíme a přejdeme přímo k **přizpůsobení cílového indexu**.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Můžete krokovat příklad indexování AI v [rychlém startu](cognitive-search-quickstart-blob.md) nebo [kurzu](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 – Konfigurace indexu

Vytváření indexu je obvykle cvičení založené na kódu, dokončené před načtením dat. Však jak tento kurz naznačuje, průvodce můžete generovat základní index pro všechny zdroje dat, které lze procházet. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat. Kromě toho můžete zadat analyzátory jazyka nebo návrhy, pokud chcete automatické dokončování nebo navrhované dotazy.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole.

* **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. Zaškrtnutím tohoto políčka můžete označit jednotlivá pole jako zakázané, například pro pole používaná pouze ve výrazech filtru.
* **Klíč** je jedinečný identifikátor dokumentu. Je to vždy řetězec, a je to nutné.
* **Filtrovatelné**, **seřaditelné**a **s tůlnou stůl na plochu** určují, zda jsou pole použita ve struktuře navigace filtru, řazení nebo fazetované navigace.
* **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Požadavky na úložiště se v důsledku výběru neliší. Pokud například nastavíte atribut **Retrievable** na více polích, požadavky na úložiště nepůjdou nahoru.

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. Řetězce jsou *připisovány* jako **retrievable** a **Searchable**. *Celá čísla* jsou připisována jako **retrievable**, **filterable**, **sortable**a **Facetable**.

1. Přijměte výchozí hodnoty. 

   Pokud průvodce znovu spustíte podruhé pomocí existujícího zdroje dat hotelů, index nebude nakonfigurován s výchozími atributy. Budete muset ručně vybrat atributy pro budoucí importy. 

   ![Index generovaných hotelů](media/search-get-started-portal/hotelsindex.png)

2. Pokračujte na další stránku.


### <a name="step-4---configure-indexer"></a>Krok 4 – Konfigurace indexeru

Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru.

Tento objekt definuje spustitelný proces. Dalo by se dát do opakovaného plánu, ale nyní použít výchozí možnost spustit indexer jednou, okamžitě.

Chcete-li indexer vytvořit a současně spustit, klepněte na **tlačítko Odeslat.**

  ![indexer hotelů](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Sledovat průběh

Průvodce by vás měl převést do seznamu indexerů, kde můžete sledovat průběh. Vlastní navigaci najdete na stránce Přehled a klikněte na **Indexery**.

Může trvat několik minut, než portál aktualizuje stránku, ale v seznamu byste měli vidět nově vytvořený indexer se stavem označujícím "probíhá" nebo úspěchem spolu s počtem indexovaných dokumentů.

   ![Zpráva indexeru o průběhu](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Zobrazení indexu

Hlavní stránka služby obsahuje odkazy na prostředky vytvořené ve službě Azure Cognitive Search.  Chcete-li zobrazit právě vytvořený index, klikněte na **položku Indexy** ze seznamu odkazů. 

Počkejte na aktualizaci stránky portálu. Po několika minutách byste měli vidět index s počtem dokumentů a velikostí úložiště.

   ![Seznam indexů na řídicím panelu služby](media/search-get-started-portal/indexes-list.png)

V tomto seznamu můžete kliknout na index *ukázkového indexu hotelů,* který jste právě vytvořili, zobrazit schéma indexu. a volitelně přidávat nová pole. 

Karta **Pole** zobrazuje schéma indexu. Přejděte na konec seznamu a zadejte nové pole. Ve většině případů nelze změnit existující pole. Existující pole mají fyzickou reprezentaci v Azure Cognitive Search a jsou tedy neupravitelné, ani v kódu. Chcete-li zásadně změnit existující pole, vytvořte nový index a vyhovte původnímu.

   ![Ukázková definice indexu](media/search-get-started-portal/sample-index-def.png)

Kdykoli lze přidat další konstrukce, jako jsou bodovací profily a možnosti CORS.

Vyhraďte si chvilku na prostudování možností definice indexu, abyste dobře chápali, co všechno můžete při návrhu indexu upravovat, a co naopak ne. Možnosti zobrazené šedě naznačují, že se určitá hodnota nedá upravit nebo odstranit. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a>Dotaz pomocí Průzkumníka vyhledávání

Teď už byste měli mít vyhledávací index, který je připravený na dotazování pomocí integrované stránky dotazů [**Průzkumník služby Hledání**](search-explorer.md). Ta obsahuje vyhledávací pole, ve kterém si můžete otestovat libovolné řetězce dotazů.

**Průzkumník vyhledávání** je vybaven pouze pro zpracování [požadavků rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), ale přijímá syntaxi pro [jednoduchou syntaxi dotazu](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a [úplnéanalyzátor dotazu Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)a všechny parametry vyhledávání dostupné v operacích [rozhraní REST ROZHRANÍ API vyhledávacího dokumentu.](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples)

> [!TIP]
> Následující kroky jsou znázorněny na 6m08s do [videa Azure Cognitive Search Overview](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   ![Příkaz průzkumníka služby Search](media/search-get-started-portal/search-explorer-cmd.png)

2. V rozevíracím souboru **Index** zvolte *index vzorků hotelů*. Kliknutím na rozevírací soubor **Verze rozhraní API** zobrazíte, která rozhraní REST API jsou k dispozici. Pro níže uvedené dotazy použijte obecně dostupnou verzi (2019-05-06).

   ![Příkazy rozhraní API a index](media/search-get-started-portal/search-explorer-changeindex.png)

3. Na vyhledávacím panelu vložte níže uvedené řetězce dotazu a klepněte na **tlačítko Hledat**.

   ![Řetězec dotazu a tlačítko hledání](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Ukázkové dotazy

Můžete zadat výrazy a fráze podobné tomu, co můžete dělat ve vyhledávání Bingnebo Google, nebo plně zadanývýrazdotaz. Výsledky jsou vráceny jako podrobné dokumenty JSON.

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-query-searchspa"></a>Příklad (řetězec dotazu):`search=spa`

* Vyhledávací **search** parametr slouží k zadání vyhledávání klíčových slov pro fulltextové vyhledávání, v tomto případě vrácení hotelových dat pro ty, kteří obsahují *lázně* v libovolném prohledávatelném poli v dokumentu.

* **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. To je úmyslné; přehled do celého dokumentu je důležitý pro účely vývoje, zejména během testování. Pokud chcete uživatelské prostředí zlepšit, je potřeba napsat kód, který [zpracuje výsledky hledání](search-pagination-page-layout.md) a vybere z nich důležité elementy.

* Dokumenty se skládají ze všech polí, která mají v indexu označení Zobrazitelné. Chcete-li zobrazit atributy indexu na portálu, klikněte na *ukázku hotelů* v seznamu **Indexy.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Příklad (parametrizovaný dotaz):`search=spa&$count=true&$top=10`

* Symbol **&** se používá k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí.

* Parametr **$count=true** vrátí celkový počet všech vrácených dokumentů. Tato hodnota se zobrazí v horní části výsledků hledání. Monitorováním změn hlášených parametrem **$count=true** můžete ověřovat filtrovací dotazy. Menší počet výsledků naznačuje, že filtr funguje.

* **$top=10** vrátí nejvyšší hodnocených 10 dokumentů z celkového počtu. Ve výchozím nastavení Azure Cognitive Search vrátí prvních 50 nejlepších shod. Pomocí parametru **$top** můžete tento počet navýšit nebo snížit.

### <a name="filter-the-query"></a><a name="filter-query"></a>Filtrování dotazu

Filtry se do požadavků hledání zahrnou po připojení parametru **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Příklad (filtrovaný): `search=beach&$filter=Rating gt 4`

* Parametr **$filter** vrací výsledky odpovídající kritériím, která jste zadali. V tomto případě hodnocení větší než 4.

* Syntaxe parametru Filter je založená na konstruktech jazyka OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a>Omezení vlastností dotazu

Součástí požadavků hledání jsou filtry omezující vlastnost. Pomocí parametru facet můžete vrátit celkový počet dokumentů odpovídajících hodnotě omezující vlastnosti, kterou zadáte.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Příklad (s omezujícími vlastnostmi a zmenšením rozsahu): `search=*&facet=Category&$top=2`

* Parametr **search=*** znamená prázdné vyhledávání. Prázdné vyhledávání prohledává všechno. Jedním z důvodů odeslání prázdného dotazu je použití filtru nebo omezující vlastnosti na kompletní sadu dokumentů. Například chcete fazetování navigační struktura se skládá ze všech hotelů v indexu.
* Parametr **facet** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrací kategorie a počet. V tomto případě jsou kategorie založeny na poli pohodlně nazývaném *Kategorie*. Neexistuje žádná agregace v Azure Cognitive Search, ale můžete `facet`přibližné agregace prostřednictvím , který poskytuje počet dokumentů v každé kategorii.

* Parametr **$top=2** vrací dva dokumenty a ilustruje, že parametr `top` můžete použít ke snížení i navýšení počtu výsledků.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Příklad (omezující vlastnost číselných hodnot): `search=spa&facet=Rating`

* Tento dotaz je omezující jako vyhodnorok pro hodnocení, při textovém vyhledávání *lázní*. Termín *Hodnocení* lze zadat jako omezující vlastnost, protože pole je v indexu označeno jako vyhledatelné, filtrovatelné a možné a hodnoty, které obsahuje (číselné, 1 až 5), jsou vhodné pro kategorizaci výpisů do skupin.

* Kategorizovat je možné pouze filtrovatelná pole. Ve výsledcích je možné vrátit pouze zobrazitelná pole.

* Pole *Hodnocení* je s dvojitou přesností s plovoucí desetinnou desetinnou desetinnou nebo seskupenou podle přesné hodnoty. Další informace o seskupování podle intervalu (například "hodnocení 3 hvězdičky", "4 hvězdičky" atd.) najdete [v tématu Jak implementovat fazetovou navigaci v Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Zvýraznění výsledků hledání

Zvýrazňování shod označuje formátování textu odpovídajícího klíčovému slovu (za předpokladu, že se v konkrétním poli našly shody). Pokud je hledaný termín schovaný v popisu, můžete pomocí zvýrazňování shod usnadnit jeho nalezení.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Příklad (zvýraznění): `search=beach&highlight=Description`

* V tomto příkladu je formátované slovo *pláž* snadněji rozpoznatelné v poli popisu.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Příklad (lingvistická analýza): `search=beaches&highlight=Description`

* Fulltextové vyhledávání rozpozná základní varianty ve formulářích slov. V tomto případě výsledky vyhledávání obsahují zvýrazněný text pro "pláž", pro hotely, které mají toto slovo ve svých prohledávatelných polích, v reakci na vyhledávání klíčových slov na "plážích". Ve výsledcích se díky lingvistické analýze mohou zobrazit různé tvary téhož slova. 

* Azure Cognitive Search podporuje 56 analyzátorů od Lucene i Microsoftu. Výchozí používá Azure Cognitive Search je standardní Analyzátor Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Zkouška vyhledávání přibližných shod

Ve výchozím nastavení chybně napsané termíny dotazu, jako *je seatle* pro "Seattle", nevracejí shody v typickém hledání. Následující příklad nevrátí žádné výsledky.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Příklad (chybně zadaný termín bez zpracování): `search=seatle`

Ke zpracování chybně napsaných slov můžete využít vyhledávání přibližných shod. Vyhledávání přibližných shod se povolí v případě, že použijete úplnou syntaxi dotazů Lucene. K tomu je potřeba provést dvě věci: nastavit v dotazu **queryType=full** a připojit k řetězci dotazu znak **~**.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Příklad (chybně zadaný termín se zpracováním): `search=seatle~&queryType=full`

Tento příklad nyní vrací dokumenty, které obsahují shody v "Seattle".

Pokud parametr **queryType** není zadaný, použije se výchozí jednoduchý analyzátor dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat celou syntaxi.

Vyhledávání přibližných shod a vyhledávání pomocí zástupných znaků mají vliv na výstup hledání. U těchto formátů dotazů se neprovádí lingvistická analýza. Před použitím fuzzy a zástupné hledání, přečtěte si [jak fulltextové vyhledávání funguje v Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) a podívejte se na část o výjimkách z lexikální analýzy.

Další informace o scénářích dotazů povolených úplným analyzátorem dotazů najdete [v tématu Syntaxe syntaxe dotazu Lucene v Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a>Vyzkoušení geoprostorového hledání

 Geoprostorové hledání je podporované prostřednictvím [datového typu edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) v polích, které obsahují souřadnice. Geoprostorové hledání je typ filtru určený v [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Příklad (filtry geografických souřadnic): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Tento vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky od daného bodu (zadaného pomocí zeměpisné šířky a délky) vzdálené méně než 5 kilometrů. Přidáním parametru **$count** můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice.

Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci typu „najít poblíž“ nebo používá navigaci podle map. Není to ale fulltextové vyhledávání. Pokud máte požadavky uživatelů na vyhledávání v městě nebo zemi nebo oblasti podle názvu, přidejte kromě souřadnic také pole obsahující názvy měst nebo oblastí.

## <a name="takeaways"></a>Shrnutí

Tento kurz poskytl rychlý úvod do Azure Cognitive Search pomocí portálu Azure.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce pro **Import dat**. Dozvěděli jste se o [indexerech](search-indexer-overview.md) a také o základních pracovních postupech návrhu indexu, včetně [podporovaných úprav publikovaného indexu](https://docs.microsoft.com/rest/api/searchservice/update-index).

V **průzkumníku služby Search** na webu Azure Portal jste se prostřednictvím praktických příkladů klíčových funkcí, jako jsou filtry, zvýrazňování shod, vyhledávání přibližných shod a geografické vyhledávání, seznámili se syntaxí dotazů.

Také jste se naučili najít indexy, indexery a zdroje dat na portálu. Pomocí portálu můžete v budoucnu u nových zdrojů dat s minimálním úsilím rychle zkontrolovat jejich definice nebo kolekce polí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Pomocí průvodce portálem vygenerujte webovou aplikaci připravenou k použití, která se spouští v prohlížeči. Můžete vyzkoušet tohoto průvodce na malý index, který jste právě vytvořili, nebo použít jednu z předdefinovaných ukázkových datových sad pro bohatší vyhledávání.

> [!div class="nextstepaction"]
> [Vytvoření vyhledávací aplikace na portálu](search-create-app-portal.md)