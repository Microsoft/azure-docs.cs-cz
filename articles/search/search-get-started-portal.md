---
title: Kurz pro Azure Search k indexování, dotazům a filtrování pomocí portálu | Microsoft Docs
description: K vytvoření indexu v Azure Search použijte na webu Azure Portal předdefinovaná ukázková data. Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod, geografické vyhledávání a další funkce.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9ee88b254131b40fdf1e01b771afa92127734e18
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>Vytvoření, dotazování a filtrování indexu Azure Search na portálu

Na webu Azure Portal začněte s předdefinovanou ukázkovou datovou sadou a rychle vytvořte index Azure Search pomocí průvodce **importem dat**. Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, vyhledávání přibližných shod a geografické vyhledávání pomocí **průzkumníka služby Search**.  

Tento úvod bez kódu vám pomůže začít s předdefinovanými daty, abyste mohli hned začít psát zajímavé dotazy. Nástroje portálu nejsou náhradou za kód, ale můžou být užitečné pro tyto úlohy:

+ Praktická výuka s minimálním zdržením
+ Vytvoření prototypu indexu před vlastním psaním kódu s využitím **importu dat**
+ Testovací dotazy a syntaxe analyzátoru v **průzkumníku služby Search**
+ Zobrazení stávajícího indexu publikovaného ve vaší službě a vyhledání jeho atributů

**časový odhad:** Přibližně 15 minut, ale déle, pokud se vyžaduje také registrace účtu nebo služby. 

Případně to můžete urychlit pomocí [úvodu založeném na kódu do programování Azure Search v .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a [službu Azure Search](search-create-service-portal.md). 

Pokud nechcete zřizovat službu hned, můžete shlédnout 6minutovou ukázku kroků v tomto kurzu, která začíná přibližně po prvních třech minutách v tomto [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Vyhledání služby
1. Přihlaste se k webu [Portál Azure](https://portal.azure.com).
2. Otevřete řídící panel služby Azure Search. Pokud jste dlaždici této služby nepřipnuli řídicí panel, můžete službu najít takto: 
   
   * Na panelu v levém navigačním podokně klikněte na **Všechny služby**.
   * Do vyhledávacího pole zadejte *vyhledávání*. Zobrazí se seznam služeb hledání pro vaše předplatné. V seznamu by se měla objevit i vaše služba. 

## <a name="check-for-space"></a>Kontrola místa
Mnoho zákazníků začíná s bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden. 

> [!TIP] 
> Dlaždice na řídicím panelu služby zobrazují, kolik indexů, indexerů a zdrojů dat už máte. Dlaždice s indexery zobrazuje indikátory úspěchu a neúspěchu. Kliknutím na tuto dlaždici zobrazíte počet indexerů. 
>
> ![Dlaždice pro indexery a zdroje dat][1]
>

## <a name="create-index"></a>Vytvoření indexu a načtení dat
Vyhledávací dotazy provádějí iterace *indexu* obsahujícího data s možností vyhledávání, metadata a konstrukce používané k optimalizaci určitého chování vyhledávání.

Aby bylo možné provést tuto úlohu z portálu, použijeme integrovanou ukázkovou datovou sadu, kterou je možné procházet pomocí indexeru prostřednictvím průvodce **importem dat**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Krok 1: Spuštění průvodce importem dat
1. Na řídicím panelu služby Azure Search klikněte na panelu příkazů na **Importovat data** a spusťte průvodce, který vytvoří a naplní index.
   
    ![Příkaz pro import dat][2]

2. V průvodci klikněte na **Zdroj dat** > **Ukázky** > **realestate-us-sample**. Pro tento zdroj dat je předem nakonfigurovaný název, typ a informace o připojení. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

    ![Výběr ukázkové datové sady][9]

3. Po kliknutí na **OK** se použije.

#### <a name="step-2-define-the-index"></a>Krok 2: Definování indexu
Vytvoření indexu je většinou ruční operace založená na kódu, ale průvodce může vygenerovat index pro libovolný zdroj dat, který může procházet. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole. 

* **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. Jednotlivá pole můžete označit jako zakázaná pro výsledky hledání zrušením zaškrtnutí tohoto políčka, například když se pole používají jenom ve výrazech filtru. 
* **Filterable** (Filtrovatelné), **Sortable** (Seřaditelné) a **Facetable** (Kategorizovatelné) určují, zda lze pole použít ve filtru, v řazení nebo ve struktuře fasetové navigace. 
* **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné. 

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. Řetězce mají atributy Retrievable a Searchable (zobrazitelné a prohledávatelné). Celá čísla mají atributy Retrievable, Filterable, Sortable a Facetable (zobrazitelné, filtrovatelné, seřaditelné a kategorizovatelné).

  ![Vytvořený index realestate][3]

Kliknutím na **OK** vytvořte index.

#### <a name="step-3-define-the-indexer"></a>Krok 3: Definování indexeru
Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru. 

Tento objekt definuje spustitelný proces. Může ho zařadit do plánu opakování, ale pro tentokrát použijte výchozí možnost spustit indexer okamžitě (jednou) a klikněte na tlačítko **OK**.  

  ![Indxer realestate][8]

## <a name="check-progress"></a>Kontrola průběhu
Pokud chcete monitorovat import dat, vraťte se na řídicí panel služby, přesuňte se dolů a dvakrát klikněte na dlaždici **Indexery**, aby se otevřel seznam indexerů. V seznamu byste měli vidět indexer, který jste právě vytvořili, se stavem indikujícím průběh práce nebo úspěch a s počtem indexovaných dokumentů.

   ![Zpráva indexeru o průběhu][4]

## <a name="query-index"></a> Dotazování indexu
Nyní máte index vyhledávání, který je připraven k dotazování. **Průzkumník služby Search** je nástroj pro dotazování, který je integrovaný v portálu. Poskytuje vyhledávací pole, abyste mohli ověřit, jestli výsledky hledání odpovídají vašemu očekávání. 

> [!TIP]
> Ve [videu s přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) jsou tyto kroky předvedené přibližně 6 min 8 s od zahájení.
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   ![Příkaz průzkumníka služby Search][5]

2. Kliknutím na **Změnit index** na panelu příkazů přejdete k *realestate-us-sample*.

   ![Příkazy rozhraní API a index][6]

3. Kliknutím na **Nastavit verzi API** na panelu příkazů zobrazíte, která rozhraní REST API jsou dostupná. Rozhraní API ve verzi Preview poskytují přístup k novým funkcím, které ještě nejsou obecně dostupné. Pro níže uvedené dotazy používejte obecně dostupnou verzi (11. listopad 2017), pokud nedostanete jiné pokyny. 

    > [!NOTE]
    > [Rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) a [knihovna .NET](search-howto-dotnet-sdk.md#core-scenarios) jsou plně ekvivalentní, ale **průzkumník služby Search** umožňuje zpracovat jenom volání REST. Přijímá syntaxi pro [jednoduché dotazy](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i [kompletní analyzátor dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) a navíc všechny parametry hledání, které jsou dostupné pro operace [hledání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Na panelu hledání zadejte uvedené řetězce dotazu a klikněte na **Hledat**.

  ![Příklad vyhledávacího dotazu][7]

**`search=seattle`**

+ Parametr **search** se používá k zadání klíčového slova pro fulltextové vyhledávání. V tomto případě vrátí výpisy v King County ve státě Washington, které v libovolném prohledávatelném poli dokumentu obsahují text *Seattle*. 

+ **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. V závislosti na vašich dokumentech bude možná nutné napsat kód, který výsledky hledání zpracuje a extrahuje z nich důležité elementy. 

+ Dokumenty se skládají ze všech polí označených v indexu jako Retrievable (zobrazitelné). Pokud chcete zobrazit atributy indexu na portálu, klikněte na *realestate-us-sample* na dlaždici **Indexy**.

**`search=seattle&$count=true&$top=100`**

+ Symbol **&** slouží k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí. 

+  Parametr **$count=true** vrací počet všech vrácených dokumentů. Monitorováním změn hlášených parametrem **$count=true** můžete ověřovat filtrovací dotazy. 

+ Parametr **$top=100** vrací 100 dokumentů s nejvyšším hodnocením. Ve výchozím nastavení vrací služba Azure Search prvních 50 nejlepších shod. Pomocí parametru **$top** můžete tento počet navýšit nebo snížit.


## <a name="filter-query"></a>Filtrování dotazu

Filtry se do požadavků hledání zahrnou po připojení parametru **$filter**. 

**`search=seattle&$filter=beds gt 3`**

+ Parametr **$filter** vrací výsledky odpovídající kritériím, která jste zadali. V tomto případě víc než 3 ložnice. 

+ Syntaxe parametru Filter je založená na konstruktech jazyka OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="facet-query"></a>Omezení vlastností dotazu

Součástí požadavků hledání jsou filtry omezující vlastnost. Pomocí parametru facet můžete vrátit celkový počet dokumentů odpovídajících hodnotě omezující vlastnosti, kterou zadáte. 

**`search=*&facet=city&$top=2`**

+ Parametr **search=*** znamená prázdné vyhledávání. Prázdné vyhledávání prohledává všechno. Jedním z důvodů odeslání prázdného dotazu je použití filtru nebo omezující vlastnosti na kompletní sadu dokumentů. Chcete například, aby se fasetová navigační struktura skládala ze všech měst v indexu.

+  Parametr **facet** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrací kategorie a počet. V tomto případě kategorie závisí na počtu měst. Ve službě Azure Search neexistuje agregace, ale můžete ji odhadnout pomocí parametru `facet`, který vrací počet dokumentů v každé kategorii.

+ Parametr **$top=2** vrací dva dokumenty a ilustruje, že parametr `top` můžete použít ke snížení i navýšení počtu výsledků.

**`search=seattle&facet=beds`**

+ Tento dotaz je omezující vlastností na postele v textovém vyhledávání výrazu *Seattle*. Termín *beds* je možné použít jako omezující vlastnost, protože toto pole je v indexu označené jako Retrievable, Filterable a Facetable (zobrazitelné, filtrovatelné a kategorizovatelné) a hodnoty, které obsahuje (číselné, od 1 do 5), jsou vhodné pro zařazení výpisů do skupin (výpisy se 3 ložnicemi nebo se 4 ložnicemi). 

+ Kategorizovat je možné pouze filtrovatelná pole. Ve výsledcích je možné vrátit pouze zobrazitelná pole.

## <a name="highlight-query"></a>Přidání zvýrazňování

Zvýrazňování shod označuje formátování textu odpovídajícího klíčovému slovu (za předpokladu, že se v konkrétním poli našly shody). Pokud je hledaný termín schovaný v popisu, můžete pomocí zvýrazňování shod usnadnit jeho nalezení. 

**`search=granite countertops&highlight=description`**

+ V tomto příkladu je formátovaná fráze *granite countertops* v poli popisu lépe viditelná.

**`search=mice&highlight=description`**

+ Fulltextové vyhledávání vyhledá tvary slov s podobnou sémantikou. V tomto případě výsledky hledání obsahují zvýrazněný text „mouse“ u domů se zamořením myšmi, a to v reakci na hledání klíčového slova „mice“. Ve výsledcích se díky lingvistické analýze mohou zobrazit různé tvary téhož slova. 

+ Azure Search podporuje 56 analyzátorů od společností Lucene a Microsoft. Jako výchozí se pro Azure Search používá standardní analyzátor Lucene. 

## <a name="fuzzy-search"></a>Použití vyhledávání přibližných shod

Chybně napsaná slova, například *samamish* místo Samammish v okolí Seattlu, při typickém hledání obvykle nevrátí shodu. Ke zpracování chybně napsaných slov můžete využít vyhledávání přibližných shod, které je popsaném v dalším příkladu.

**`search=samamish`**

+ V tomto příkladu je chybně napsaný název sousedství v okolí Seattlu.

**`search=samamish~&queryType=full`**

+ Vyhledávání přibližných shod se povolí, pokud zadáte symbol **~** a použijete kompletní analyzátor dotazů, který syntaxi **~** správně interpretuje a parsuje. 

+ Vyhledávání přibližných shod bude dostupné, když vyjádříte výslovný souhlas s použitím kompletního analyzátoru dotazů nastavením parametru **queryType=full**. Další informace o scénářích, které umožňuje použít kompletní analyzátor dotazů, najdete v tématu věnovaném [syntaxi dotazů Lucene ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

+ Pokud parametr **queryType** není zadaný, použije se výchozí jednoduchý analyzátor dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat celou syntaxi. 

## <a name="geo-search"></a>Vyzkoušení geoprostorového hledání

Geoprostorové hledání je podporované prostřednictvím [datového typu edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) v polích, které obsahují souřadnice. Geoprostorové hledání je typ filtru určený v [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ Tento vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky od daného bodu (zadaného pomocí zeměpisné šířky a délky) vzdálené méně než 5 kilometrů. Přidáním parametru **$count** můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice. 

+ Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci typu „najít poblíž“ nebo používá navigaci podle map. Není to ale fulltextové vyhledávání. Pokud chcete, aby uživatelé mohli hledat město nebo okres podle názvu, přidejte kromě souřadnic také pole, která budou obsahovat názvy města nebo okresu.

## <a name="next-steps"></a>Další kroky

+ Upravte libovolný z objektů, které jste právě vytvořili. Po prvním spuštění průvodce se můžete vrátit a zobrazit nebo upravit jednotlivé komponenty: index, indexer nebo zdroj dat. Některé úpravy, jako je například změna datového typu pole, nejsou u indexu povoleny, ale většinu vlastností a nastavení lze měnit.

  Chcete-li zobrazit jednotlivé komponenty, klikněte na dlaždice **Index**, **Indexer** nebo **Zdroje dat** na řídicím panelu, aby se zobrazil seznam existujících objektů. Další informace o úpravách indexu, které nevyžadují opětovné sestavení, najdete v části věnované [aktualizaci indexu (rozhraní REST API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/update-index).

+ Uvedené nástroje a kroky vyzkoušejte i pro další zdroje dat. Ukázková datová sada, `realestate-us-sample`, je ze služby Azure SQL Database, kterou může Azure Search procházet. Kromě služby Azure SQL Database může Azure Search procházet také ploché struktury dat (a odvozovat z nich indexy) ve službách Azure Table Storage, Blob Storage, SQL Server na virtuálním počítači Azure a ve službě Azure Cosmos DB. Tento průvodce podporuje všechny tyto zdroje dat. V kódu můžete index snadno vytvořit a naplnit pomocí *indexeru*.

+ Všechny ostatní zdroje dat bez indexeru jsou podporované prostřednictvím modelu Push, kdy kód do indexu aktivně vkládá nové a změněné sady řádků ve formátu JSON. Další informace najdete v tématu věnovaném [přidání, aktualizaci a odstranění dokumentů ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Další informace o ostatních funkcích uvedených v tomto článku najdete pomocí těchto odkazů:

* [Přehled indexerů](search-indexer-overview.md)
* [Vytvoření indexu (zahrnuje podrobné vysvětlení atributů indexu)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Průzkumník služby Search](search-explorer.md)
* [Hledání dokumentů (zahrnuje příklady syntaxe dotazů)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


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