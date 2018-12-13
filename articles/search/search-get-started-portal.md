---
title: Kurz indexování, dotazům a filtrování na webu Azure portal – Azure Search
description: V tomto kurzu vytvoříte v Azure Search index pomocí webu Azure Portal a předdefinovaných ukázkových dat. Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod, geografické vyhledávání a další funkce.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d8b95aaab99fc4f0aa5df21374d6ec023f869b7d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314019"
---
# <a name="tutorial-use-built-in-portal-tools-for-azure-search-indexing-and-queries"></a>Kurz: Použít integrované nástroje portálu pro Azure Search indexování a dotazy

K rychlému seznámení se s koncepty služby Azure Search a jejich vyzkoušení můžete použít integrované nástroje, které najdete na stránce služby Azure Search na webu Azure Portal. Tyto nástroje nemusí nabízet všechny funkce .NET a rozhraní REST API. Průvodci a editory však nabízejí úvod do služby Azure Search bez kódu a umožňují okamžitě psát zajímavé dotazy na ukázkovou datovou sadu.

> [!div class="checklist"]
> * Začnete s veřejnými ukázkovými daty a automaticky vygenerujete index Azure Search pomocí průvodce **Importovat data**.
> * Zobrazíte schéma a atributy indexu pro jakýkoli index publikovaný v Azure Search.
> * Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod a geografické vyhledávání pomocí **průzkumníka služby Search**.  

Pokud vás nástroje moc omezují, můžete se podívat na [úvod do programování Azure Search v .NET založený na kódu](search-howto-dotnet-sdk.md) nebo na [webové testovací nástroje pro volání rozhraní REST API](search-fiddler.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. Můžete se taky podívat na 6minutovou ukázku kroků v tomto kurzu, která začíná přibližně po prvních třech minutách v tomto [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Požadavky

[Vytvořte službu Azure Search](search-create-service-portal.md) nebo ve svém aktuálním předplatném najděte stávající službu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Otevřete řídící panel služby Azure Search. Pokud jste dlaždici této služby nepřipnuli řídicí panel, můžete službu najít takto:

   * Na panelu v levém navigačním podokně klikněte na **Všechny služby**.
   * Do vyhledávacího pole zadejte *vyhledávání*. Zobrazí se seznam vyhledávacích služeb pro vaše předplatné. Klikněte na **Služby vyhledávání**. V seznamu by se měla objevit i vaše služba.

### <a name="check-for-space"></a>Kontrola místa

Mnoho zákazníků začíná s bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden.

> [!TIP]
> Dlaždice na řídicím panelu služby zobrazují, kolik indexů, indexerů a zdrojů dat už máte. Dlaždice s indexery zobrazuje indikátory úspěchu a neúspěchu. Kliknutím na tuto dlaždici zobrazíte počet indexerů.
>
> ![Dlaždice pro indexery a zdroje dat][1]
>

## <a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy provádějí iterace [*indexu*](search-what-is-an-index.md), který obsahuje data s možností vyhledávání, metadata a další konstrukce, které optimalizují určité chování vyhledávání.

V tomto kurzu použijeme integrovanou ukázkovou datovou sadu, kterou je možné procházet pomocí [*indexeru*](search-indexer-overview.md) prostřednictvím průvodce pro **Import dat**. Indexer je prohledávací modul pro určitý zdroj, který dokáže číst metadata a obsah z podporovaných zdrojů dat Azure. Tyto indexery se zobrazují na portálu v průvodci pro **Import dat**. Později můžete indexery vytvářet a spravovat jako samostatné prostředky prostřednictvím kódu programu.

### <a name="step-1-start-the-import-data-wizard"></a>Krok 1: Spusťte Průvodce importem dat

1. Spusťte průvodce kliknutím na **Import dat** na panelu příkazů na řídicím panelu služby Azure Search. Tento průvodce vám pomůže vytvořit a naplnit index vyhledávání.

    ![Příkaz pro import dat][2]

2. V průvodci klikněte na **Připojit se k datům** > **Ukázky** > **realestate-us-sample**. Pro tento zdroj dat je předem nakonfigurovaný název, typ a informace o připojení. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

    ![Výběr ukázkové datové sady][9]

3. Po kliknutí na **OK** se použije.

### <a name="skip-cognitive-skills"></a>Vynechání kognitivních dovedností

Průvodce pro **Import dat** nabízí volitelný krok kognitivních dovedností, který umožňuje do indexování přidat vlastní algoritmy umělé inteligence. Tento krok prozatím přeskočte a přejděte k **Přizpůsobení cílového indexu**.

> [!TIP]
> Novou funkci kognitivního vyhledávání ve verzi Preview pro Azure Search si můžete vyzkoušet v [rychlém startu pro kognitivní vyhledávání](cognitive-search-quickstart-blob.md) nebo v souvisejícím [kurzu](cognitive-search-tutorial-blob.md).

   ![Vynechání kroku kognitivních dovedností][11]

### <a name="step-2-define-the-index"></a>Krok 2: Definování indexu

Vytvoření indexu se obvykle provádí ručně prostřednictvím kódu. Pro účely tohoto kurzu může průvodce vygenerovat index pro jakýkoli zdroj dat, který dokáže procházet. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole.

* **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. Jednotlivá pole můžete označit jako zakázaná pro výsledky hledání zrušením zaškrtnutí tohoto políčka, například když se pole používají jenom ve výrazech filtru.
* **Filterable** (Filtrovatelné), **Sortable** (Seřaditelné) a **Facetable** (Kategorizovatelné) určují, zda lze pole použít ve filtru, v řazení nebo ve struktuře fasetové navigace.
* **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. Řetězce mají atributy Retrievable a Searchable (zobrazitelné a prohledávatelné). Celá čísla mají atributy Retrievable, Filterable, Sortable a Facetable (zobrazitelné, filtrovatelné, seřaditelné a kategorizovatelné).

  ![Vytvořený index realestate][3]

Kliknutím na **OK** vytvořte index.

### <a name="step-3-define-the-indexer"></a>Krok 3: Definování indexeru

Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru.

Tento objekt definuje spustitelný proces. Může ho zařadit do plánu opakování, ale pro tentokrát použijte výchozí možnost spustit indexer okamžitě (jednou) a klikněte na tlačítko **OK**.  

  ![Indxer realestate][8]

### <a name="check-progress"></a>Kontrola průběhu

Pokud chcete monitorovat import dat, vraťte se na řídicí panel služby, přesuňte se dolů a dvakrát klikněte na dlaždici **Indexery**, aby se otevřel seznam indexerů. V seznamu byste měli vidět indexer, který jste právě vytvořili, se stavem indikujícím průběh práce nebo úspěch a s počtem indexovaných dokumentů.

   ![Zpráva indexeru o průběhu][4]

### <a name="step-4-view-the-index"></a>Krok 4: Zobrazení indexu

Dlaždice na řídicím panelu služby poskytují souhrnné informace o různých objektech v prostředcích i přístup k podrobným informacím. Dlaždice **Indexy** obsahuje seznam stávajících indexů včetně indexu *realestate-us-sample*, který jste vytvořili v předchozím kroku.

Klikněte na index *realestate-us-sample*. Zobrazí se možnosti portálu týkající se jeho definice. Možnost **Přidat/upravit pole** umožňuje vytvořit nová pole s úplnou sadou atributů. Existující pole mají v Azure Search fyzickou reprezentaci, takže se nedají změnit, dokonce ani v kódu. Pokud chcete zásadním způsobem změnit existující pole, vytvořte nové pole a to původní zrušte.

   ![Ukázková definice indexu][10]

Kdykoli lze přidat další konstrukce, jako jsou bodovací profily a možnosti CORS.

Vyhraďte si chvilku na prostudování možností definice indexu, abyste dobře chápali, co všechno můžete při návrhu indexu upravovat, a co naopak ne. Možnosti zobrazené šedě naznačují, že se určitá hodnota nedá upravit nebo odstranit. Podobně prozatím přeskočte i zaškrtávací políčka Analyzátor a Našeptávač.

## <a name="query-index"></a> Dotazování indexu

Teď už byste měli mít vyhledávací index, který je připravený na dotazování pomocí integrované stránky dotazů [**Průzkumník služby Hledání**](search-explorer.md). Ta obsahuje vyhledávací pole, ve kterém si můžete otestovat libovolné řetězce dotazů.

> [!TIP]
> Následující kroky jsou předvedené ve [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) přibližně 6 min 8 s od zahájení.
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   ![Příkaz průzkumníka služby Search][5]

2. Kliknutím na **Změnit index** na panelu příkazů přejdete k *realestate-us-sample*. Kliknutím na **Nastavit verzi API** na panelu příkazů zobrazíte, která rozhraní REST API jsou dostupná. Pro níže uvedené dotazy používejte obecně dostupnou verzi (11. listopad 2017).

   ![Příkazy rozhraní API a index][6]

3. Na panelu hledání zadejte uvedené řetězce dotazu a klikněte na **Hledat**.

    > [!NOTE]
    > **Průzkumník služby Hledání** umožňuje zpracovávat jenom [požadavky rozhraní API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Přijímá syntaxi pro [jednoduché dotazy](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i [kompletní analyzátor dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) a navíc všechny parametry hledání, které jsou dostupné pro operace [hledání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    >

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-searchseattle"></a>Příklad (řetězec): `search=seattle`

* Parametr **search** se používá k zadání klíčového slova pro fulltextové vyhledávání. V tomto případě vrátí výpisy v King County ve státě Washington, které v libovolném prohledávatelném poli dokumentu obsahují text *Seattle*.

* **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. Je tomu tak záměrně, protože viditelnost celého dokumentu je důležitá pro účely vývoje, zejména během testování. Pokud chcete uživatelské prostředí zlepšit, je potřeba napsat kód, který [zpracuje výsledky hledání](search-pagination-page-layout.md) a vybere z nich důležité elementy.

* Dokumenty se skládají ze všech polí, která mají v indexu označení Zobrazitelné. Pokud chcete zobrazit atributy indexu na portálu, klikněte na *realestate-us-sample* na dlaždici **Indexy**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Příklad (s parametry): `search=seattle&$count=true&$top=100`

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

Tento kurz obsahoval rychlý úvod do používání služby Azure Search z webu Azure Portal.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce pro **Import dat**. Dozvěděli jste se o [indexerech](search-indexer-overview.md) a také o základních pracovních postupech návrhu indexu, včetně [podporovaných úprav publikovaného indexu](https://docs.microsoft.com/rest/api/searchservice/update-index).

V **průzkumníku služby Search** na webu Azure Portal jste se prostřednictvím praktických příkladů klíčových funkcí, jako jsou filtry, zvýrazňování shod, vyhledávání přibližných shod a geografické vyhledávání, seznámili se syntaxí dotazů.

Také jste zjistili, jak používat dlaždice na řídicím panelu portálu pro index vyhledávání, indexer a zdroje dat. Pomocí portálu můžete v budoucnu u nových zdrojů dat s minimálním úsilím rychle zkontrolovat jejich definice nebo kolekce polí.

## <a name="clean-up"></a>Vyčištění

Pokud jste v tomto kurzu použili službu Azure Search poprvé, odstraňte skupinu prostředků obsahující službu Azure Search. Pokud ne, vyhledejte název odpovídající skupiny prostředků v seznamu služeb a odstraňte ji.

## <a name="next-steps"></a>Další postup

Můžete prozkoumat další funkce služby Azure Search s použitím programových nástrojů:

* [Vytvoření indexu pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Vytvoření indexu pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* Použití [webových testovacích nástrojů, jako je Postman nebo Fiddler, k volání rozhraní REST API služby Azure Search](search-fiddler.md)

<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png