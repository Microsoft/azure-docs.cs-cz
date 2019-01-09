---
title: Rychlý úvodní kurz indexování a dotazování pomocí webu Azure portal – Azure Search
description: V tomto kurzu rychlý start pomocí webu Azure portal a integrované ukázková data k vytvoření indexu ve službě Azure Search. Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod, geografické vyhledávání a další funkce.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9bdc2e197b4d7aea270c954305a96a01a1371945
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121545"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Rychlý start: Integrované nástroje portálu použijte pro import Azure Search, indexování a dotazy

Pro rychlé Seznamte se s koncepty Azure Search zkuste integrované nástroje na webu Azure Portal. Průvodci a editory neposkytují úplnou paritu s využitím .NET a rozhraní REST API, ale můžete začít rychle s obsahuje úvod bez kódu zápis zajímavé dotazy na ukázková data během několika minut.

> [!div class="checklist"]
> * Začněte s bezplatnou veřejné ukázkové sadě dat hostované v Azure
> * Spustit **importovat data** Průvodce ve službě Azure Search k načtení dat a vytvoření indexu
> * Monitorování průběhu na portálu pro indexování
> * Zobrazení stávajícího indexu a možnosti pro změnu jeho
> * Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod a geografické vyhledávání pomocí **Průzkumníka služby Search**

Pokud jsou příliš omezení nástroje, můžete zvážit [založený na kódu Úvod do programování Azure Search v .NET](search-howto-dotnet-sdk.md) nebo použijte [Postman nebo Fiddler pro volání rozhraní REST API](search-fiddler.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. Můžete se taky podívat na 6minutovou ukázku kroků v tomto kurzu, která začíná přibližně po prvních třech minutách v tomto [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Požadavky

[Vytvořte službu Azure Search](search-create-service-portal.md) nebo ve svém aktuálním předplatném najděte stávající službu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Otevřete řídící panel služby Azure Search. Pokud jste dlaždici této služby nepřipnuli řídicí panel, můžete službu najít takto:

   * Na panelu v levém navigačním podokně klikněte na **Všechny služby**.
   * Do vyhledávacího pole zadejte *vyhledávání*. Zobrazí se seznam vyhledávacích služeb pro vaše předplatné. Klikněte na **Služby vyhledávání**. V seznamu by se měla objevit i vaše služba.

### <a name="check-for-space"></a>Kontrola místa

Mnoho zákazníků začíná s bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden.

Oddíly na řídicím panelu služby zobrazit, kolik indexů, indexerů a zdrojů dat už máte. 

![Seznam indexy, indexery a zdroje dat](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy provádějí iterace [*indexu*](search-what-is-an-index.md), který obsahuje data s možností vyhledávání, metadata a další konstrukce, které optimalizují určité chování vyhledávání.

V tomto kurzu použijeme integrovanou ukázkovou datovou sadu, kterou je možné procházet pomocí [*indexeru*](search-indexer-overview.md) prostřednictvím průvodce pro **Import dat**. Indexer je prohledávací modul pro určitý zdroj, který dokáže číst metadata a obsah z podporovaných zdrojů dat Azure. Za normálních okolností indexery se používají prostřednictvím kódu programu, ale na portálu jste můžete k nim přistupovat prostřednictvím **importovat data** průvodce. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 – Spusťte Průvodce importem dat a vytvoření zdroje dat

1. Na řídicím panelu služby Azure Search, klikněte na tlačítko **importovat data** na panelu příkazů pro vytvoření a naplnění indexu vyhledávání.

   ![Příkaz pro import dat](media/search-get-started-portal/import-data-cmd2.png)

2. V průvodci klikněte na **Připojit se k datům** > **Ukázky** > **realestate-us-sample**. Tento zdroj dat jsou integrované. Pokud vytváříte vlastní zdroj dat, je třeba zadat název, typ a informace o připojení. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

   ![Výběr ukázkové datové sady](media/search-get-started-portal/import-datasource-sample2.png)

3. Přejít na další stránku.

   ![Tlačítko Další stránky pro kognitivního vyhledávání](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Krok 2: přeskočit kognitivních dovedností

Průvodce podporuje vytváření [kognitivní dovednosti kanálu](cognitive-search-concept-intro.md) požadavky pro začlenění algoritmy Cognitive Services AI do indexování. 

Použijeme tento krok prozatím přeskočit a přejít přímo k **přizpůsobit cílový index**.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Novou funkci kognitivního vyhledávání ve verzi Preview pro Azure Search si můžete vyzkoušet v [rychlém startu pro kognitivní vyhledávání](cognitive-search-quickstart-blob.md) nebo v souvisejícím [kurzu](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3: Konfigurace indexu

Vytvoření indexu je obvykle založená na kódu cvičení, dokončeno před načtením dat. Ale protože naznačuje, v tomto kurzu, bude průvodce vytvářet základní index pro libovolný zdroj dat, které může procházet. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat. Kromě toho můžete určit jazykové analyzátory nebo moduly pro návrhy potřebujete automatické dokončování nebo navrhované dotazy.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole.

* **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. Jednotlivá pole můžete označit jako zakázaná pro výsledky hledání zrušením zaškrtnutí tohoto políčka, například když se pole používají jenom ve výrazech filtru.
* **Klíč** je jedinečný identifikátor dokumentu. Je vždy řetězec a je povinný.
* **Filterable**, **Sortable**, a **Facetable** určit, zda pole se používají v filtrovat, řadit nebo fasetovou strukturu navigace.
* **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Požadavky na úložiště v důsledku výběru neliší. Pokud nastavíte například **Retrievable** atribut podle více polí, požadavky na nepřejdou do úložiště.

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. Řetězce mají atributy Retrievable a Searchable (zobrazitelné a prohledávatelné). Celá čísla mají atributy Retrievable, Filterable, Sortable a Facetable (zobrazitelné, filtrovatelné, seřaditelné a kategorizovatelné).

1. Přijměte všechny výchozí hodnoty.

  ![Vytvořený index realestate](media/search-get-started-portal/realestateindex2.png)

2. Přejít na další stránku.

  ![Další stránka vytvořit indexer](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Krok 4: Konfigurace indexeru

Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru.

Tento objekt definuje spustitelný proces. Může umístit na plán opakování, ale prozatím použijte výchozí možnost spuštění indexeru, okamžitě.

Klikněte na tlačítko **odeslat** vytvořit a současně spustit indexer.

  ![Indxer realestate](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>Sledování průběhu

Průvodce by vám neměl zabrat do seznamu indexery ve kterém můžete sledovat průběh. Navigace v samostatné, najdete v přehledu stránky a klikněte na tlačítko **indexery**.

Může trvat několik minut, než se na portál a aktualizujte stránku, ale byste měli vidět indexer nově vytvořený v seznamu, se stavem indikujícím "Probíhá", nebo úspěch a s počtem indexovaných dokumentů.

   ![Zpráva indexeru o průběhu](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Zobrazení indexu

Na stránce hlavní služba obsahuje odkazy na prostředky vytvořené v rámci služby Azure Search.  Chcete-li zobrazit index, který jste právě vytvořili, klikněte na tlačítko **indexy** ze seznamu odkazů. 

   ![Seznam indexů na řídicím panelu služby](media/search-get-started-portal/indexes-list.png)

Z tohoto seznamu, můžete kliknout na *realestate-us-sample* index, který jste právě vytvořili, zobrazit schéma indexu. a volitelně přidat nová pole. 

**Pole** karta zobrazuje schéma indexu. Přejděte do dolní části seznamu zadejte nové pole. Ve většině případů nelze změnit existující pole. Existující pole mají v Azure Search fyzickou reprezentaci, takže se nedají změnit, dokonce ani v kódu. K zásadním způsobem mění stávající pole, vytvořte nový index, vyřadit původní.

   ![Ukázková definice indexu](media/search-get-started-portal/sample-index-def.png)

Kdykoli lze přidat další konstrukce, jako jsou bodovací profily a možnosti CORS.

Vyhraďte si chvilku na prostudování možností definice indexu, abyste dobře chápali, co všechno můžete při návrhu indexu upravovat, a co naopak ne. Možnosti zobrazené šedě naznačují, že se určitá hodnota nedá upravit nebo odstranit. 

## <a name="query-index"></a> Dotazování pomocí Průzkumníka služby Search

Teď už byste měli mít vyhledávací index, který je připravený na dotazování pomocí integrované stránky dotazů [**Průzkumník služby Hledání**](search-explorer.md). Ta obsahuje vyhledávací pole, ve kterém si můžete otestovat libovolné řetězce dotazů.

**Průzkumník služby Search** je umožňuje zpracovat jenom [požadavků na rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), ale přijímá syntaxi pro [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a [kompletní analyzátor dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), a navíc všechny parametry vyhledávání k dispozici v [REST API služby Search dokumentu](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) operace.

> [!TIP]
> Následující kroky jsou předvedené ve [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) přibližně 6 min 8 s od zahájení.
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   ![Příkaz průzkumníka služby Search](media/search-get-started-portal/search-explorer-cmd2.png)

2. Kliknutím na **Změnit index** na panelu příkazů přejdete k *realestate-us-sample*. Kliknutím na **Nastavit verzi API** na panelu příkazů zobrazíte, která rozhraní REST API jsou dostupná. Pro níže uvedené dotazy používejte obecně dostupnou verzi (11. listopad 2017).

   ![Příkazy rozhraní API a index](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. Do vyhledávacího pole vložte uvedené řetězce dotazu a klikněte na tlačítko **hledání**.

   ![Tlačítko řetězec a vyhledávací dotaz](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Příklady dotazů

Můžete zadat termínů a frází, podobný může provádět vyhledávání Bing nebo Google nebo výrazů plně zadaný dotaz. Výsledky jsou vráceny jako podrobné dokumenty JSON.

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-query-searchseattle"></a>Příklad (řetězce dotazu): `search=seattle`

* Parametr **search** se používá k zadání klíčového slova pro fulltextové vyhledávání. V tomto případě vrátí výpisy v King County ve státě Washington, které v libovolném prohledávatelném poli dokumentu obsahují text *Seattle*.

* **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. Je to záměr; Přehled celého dokumentu je důležité pro účely vývoje, zejména při testování. Pokud chcete uživatelské prostředí zlepšit, je potřeba napsat kód, který [zpracuje výsledky hledání](search-pagination-page-layout.md) a vybere z nich důležité elementy.

* Dokumenty se skládají ze všech polí, která mají v indexu označení Zobrazitelné. Chcete-li zobrazit atributy indexu na portálu, klikněte na tlačítko *realestate-us-sample* v **indexy** seznamu.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>Příklad (parametrický dotaz): `search=seattle&$count=true&$top=100`

* Symbol **&** slouží k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí.

* Parametr **$count=true** vrací celkový počet všech vrácených dokumentů. Tato hodnota se zobrazí v horní části výsledků hledání. Monitorováním změn hlášených parametrem **$count=true** můžete ověřovat filtrovací dotazy. Menší počet výsledků naznačuje, že filtr funguje.

* Parametr **$top=100** vrací 100 dokumentů s nejvyšším hodnocením. Ve výchozím nastavení vrací služba Azure Search prvních 50 nejlepších shod. Pomocí parametru **$top** můžete tento počet navýšit nebo snížit.

### <a name="filter-query"></a>Filtrování dotazu

Filtry se do požadavků hledání zahrnou po připojení parametru **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Příklad (filtrovaný): `search=seattle&$filter=beds gt 3`

* Parametr **$filter** vrací výsledky odpovídající kritériím, která jste zadali. V tomto případě víc než 3 ložnice.

* Syntaxe parametru Filter je založená na konstruktech jazyka OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a>Omezení vlastností dotazu

Součástí požadavků hledání jsou filtry omezující vlastnost. Pomocí parametru facet můžete vrátit celkový počet dokumentů odpovídajících hodnotě omezující vlastnosti, kterou zadáte.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Příklad (s omezujícími vlastnostmi a zmenšením rozsahu): `search=*&facet=city&$top=2`

* Parametr **search=*** znamená prázdné vyhledávání. Prázdné vyhledávání prohledává všechno. Jedním z důvodů odeslání prázdného dotazu je použití filtru nebo omezující vlastnosti na kompletní sadu dokumentů. Chcete například, aby se fasetová navigační struktura skládala ze všech měst v indexu.

* Parametr **facet** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrací kategorie a počet. V tomto případě kategorie závisí na počtu měst. Ve službě Azure Search neexistuje agregace, ale můžete ji odhadnout pomocí parametru `facet`, který vrací počet dokumentů v každé kategorii.

* Parametr **$top=2** vrací dva dokumenty a ilustruje, že parametr `top` můžete použít ke snížení i navýšení počtu výsledků.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Příklad (omezující vlastnost číselných hodnot): `search=seattle&facet=beds`**

* Tento dotaz je omezující vlastností na postele v textovém vyhledávání výrazu *Seattle*. Termín *beds* je možné použít jako omezující vlastnost, protože toto pole je v indexu označené jako Retrievable, Filterable a Facetable (zobrazitelné, filtrovatelné a kategorizovatelné) a hodnoty, které obsahuje (číselné, od 1 do 5), jsou vhodné pro zařazení výpisů do skupin (výpisy se 3 ložnicemi nebo se 4 ložnicemi).

* Kategorizovat je možné pouze filtrovatelná pole. Ve výsledcích je možné vrátit pouze zobrazitelná pole.

### <a name="highlight-query"></a> Zvýraznění výsledků hledání

Zvýrazňování shod označuje formátování textu odpovídajícího klíčovému slovu (za předpokladu, že se v konkrétním poli našly shody). Pokud je hledaný termín schovaný v popisu, můžete pomocí zvýrazňování shod usnadnit jeho nalezení.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Příklad (zvýraznění): `search=granite countertops&highlight=description`

* V tomto příkladu je formátovaná fráze *granite countertops* v poli popisu lépe viditelná.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Příklad (lingvistická analýza): `search=mice&highlight=description`

* Fulltextové vyhledávání vyhledá tvary slov s podobnou sémantikou. V tomto případě výsledky hledání obsahují zvýrazněný text „mouse“ u domů se zamořením myšmi, a to v reakci na hledání klíčového slova „mice“. Ve výsledcích se díky lingvistické analýze mohou zobrazit různé tvary téhož slova.

* Azure Search podporuje 56 analyzátorů od společností Lucene a Microsoft. Jako výchozí se pro Azure Search používá standardní analyzátor Lucene.

### <a name="fuzzy-search"></a> Zkouška vyhledávání přibližných shod

Ve výchozím nastavení chybně napsané termíny dotazu, třeba když název náhorní plošiny Samammish v okolí Seattlu napíšete jako *samamish*, při typickém hledání nevrátí žádnou shodu. Následující příklad nevrátí žádné výsledky.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Příklad (chybně zadaný termín bez zpracování): `search=samamish`

Ke zpracování chybně napsaných slov můžete využít vyhledávání přibližných shod. Vyhledávání přibližných shod se povolí v případě, že použijete úplnou syntaxi dotazů Lucene. K tomu je potřeba provést dvě věci: nastavit v dotazu **queryType=full** a připojit k řetězci dotazu znak **~**.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Příklad (chybně zadaný termín se zpracováním): `search=samamish~&queryType=full`

Tento příklad najednou vrací dokumenty, které obsahují shody s termínem „Sammamish“.

Pokud parametr **queryType** není zadaný, použije se výchozí jednoduchý analyzátor dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat celou syntaxi.

Vyhledávání přibližných shod a vyhledávání pomocí zástupných znaků mají vliv na výstup hledání. U těchto formátů dotazů se neprovádí lingvistická analýza. Než použijete vyhledávání přibližných shod a vyhledávání pomocí zástupných znaků, přečtěte si článek o [způsobu fungování fulltextového vyhledávání ve službě Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis), konkrétně část o výjimkách z lexikální analýzy.

Další informace o scénářích, které umožňuje použít kompletní analyzátor dotazů, najdete v tématu věnovaném [syntaxi dotazů Lucene ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a>Vyzkoušení geoprostorového hledání

Geoprostorové hledání je podporované prostřednictvím [datového typu edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) v polích, které obsahují souřadnice. Geoprostorové hledání je typ filtru určený v [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Příklad (filtry geografických souřadnic): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

Tento vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky od daného bodu (zadaného pomocí zeměpisné šířky a délky) vzdálené méně než 5 kilometrů. Přidáním parametru **$count** můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice.

Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci typu „najít poblíž“ nebo používá navigaci podle map. Není to ale fulltextové vyhledávání. Pokud chcete, aby uživatelé mohli hledat město nebo okres podle názvu, přidejte kromě souřadnic také pole, která budou obsahovat názvy města nebo okresu.

## <a name="takeaways"></a>Shrnutí

Tento kurz poskytuje rychlý úvod do služby Azure Search pomocí webu Azure portal.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce pro **Import dat**. Dozvěděli jste se o [indexerech](search-indexer-overview.md) a také o základních pracovních postupech návrhu indexu, včetně [podporovaných úprav publikovaného indexu](https://docs.microsoft.com/rest/api/searchservice/update-index).

V **průzkumníku služby Search** na webu Azure Portal jste se prostřednictvím praktických příkladů klíčových funkcí, jako jsou filtry, zvýrazňování shod, vyhledávání přibližných shod a geografické vyhledávání, seznámili se syntaxí dotazů.

Také jste zjistili, jak najít indexy, indexery a zdroje dat na portálu. Pomocí portálu můžete v budoucnu u nových zdrojů dat s minimálním úsilím rychle zkontrolovat jejich definice nebo kolekce polí.

## <a name="clean-up"></a>Vyčištění

Pokud v tomto kurzu byla vaše první používání služby Azure Search, odstraňte skupinu prostředků obsahující službu Azure Search. Pokud ne, vyhledejte název odpovídající skupiny prostředků v seznamu služeb a odstraňte ji.

## <a name="next-steps"></a>Další postup

Můžete prozkoumat další funkce služby Azure Search s použitím programových nástrojů:

* [Vytvoření indexu pomocí .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Vytvoření indexu pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Vytvoření indexu pomocí nástroje Postman nebo Fiddler a rozhraní REST API Azure Search](search-fiddler.md)