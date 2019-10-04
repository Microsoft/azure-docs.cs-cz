---
title: 'Rychlý Start: vytvoření indexu vyhledávání pomocí Azure Portal-Azure Search'
description: Průvodce importem dat v Azure Portal slouží k vytvoření, načtení a dotazování prvního indexu v Azure Search.
author: lobrien
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: laobri
ms.openlocfilehash: a4a25b8504d873b624e1f6822807c9c08ebd2e4f
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936985"
---
# <a name="quickstart-create-an-azure-search-index-using-the-azure-portal"></a>Rychlý Start: vytvoření indexu Azure Search pomocí Azure Portal
> [!div class="op_single_selector"]
> * [Bran](search-get-started-portal.md)
> * [Prostředí](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [C#](search-get-started-dotnet.md)

Pro zajištění rychlého spektra Azure Search konceptů si můžete vyzkoušet integrované nástroje v Azure Portal. Průvodci a editory nenabízejí úplnou paritu s rozhraními API .NET a REST, ale můžete rychle začít s úvodem do bezplatného kódu a psaním zajímavých dotazů na index během několika minut.

> [!div class="checklist"]
> * Začněte s bezplatnou veřejnou ukázkovou datovou sadou hostovanou v Azure
> * Spuštění průvodce **importem dat** v Azure Search, aby se načetla data a generoval index
> * Sledování průběhu indexování na portálu
> * Zobrazit existující index a možnosti pro jeho úpravu
> * Prozkoumejte fulltextové vyhledávání, filtry, omezující vlastnosti, přibližné vyhledávání a hledání v **Průzkumníkovi pomocí Průzkumníka vyhledávání**

Pokud jsou nástroje příliš omezené, můžete zvážit [Úvod do programování Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md) nebo použití [metody post pro REST API volání](search-get-started-postman.md). Můžete si také prohlédnout 6 minut ukázku kroků v tomto kurzu, počínaje přibližně 3 minuty do tohoto [Azure Search přehled videa](https://channel9.msdn.com/Events/Connect/2016/138).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

[Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

### <a name="check-for-space"></a>Kontrolovat místo

Spousta zákazníků začíná bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytvoří jeden z jednotlivých objektů.

Oddíly na řídicím panelu služby ukazují, kolik indexů, indexerů a datových zdrojů už máte. 

![Seznam indexů, indexerů a zdrojů dat](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy iterují v [*indexu*](search-what-is-an-index.md) , který obsahuje hledaná data, metadata a další konstrukce, které optimalizují určitá chování hledání.

Pro tento kurz používáme vestavěnou ukázkovou datovou sadu, kterou lze procházet pomocí [*indexeru*](search-indexer-overview.md) prostřednictvím [Průvodce **importem dat** ](search-import-data-portal.md). Indexer je prohledávací modul určený ke zdroji, který může číst metadata a obsah z podporovaných zdrojů dat Azure. Obvykle se indexer používá programově, ale na portálu můžete k nim přistupovat pomocí průvodce **importem dat** . 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 – spuštění Průvodce importem dat a vytvoření zdroje dat

1. Na řídicím panelu služby Azure Search klikněte na příkaz **importovat data** na panelu příkazů a vytvořte a naplňte index vyhledávání.

   ![Importovat data – příkaz](media/search-get-started-portal/import-data-cmd.png)

2. V průvodci klikněte na **připojit k datům** > **ukázky** > **hotely-Sample**. Tento zdroj dat je integrovaný. Pokud jste vytvořili vlastní zdroj dat, budete muset zadat název, typ a informace o připojení. Po vytvoření se zobrazí jako "existující zdroj dat", který se dá znovu použít v jiných operacích importu.

   ![Vybrat ukázkovou datovou sadu](media/search-get-started-portal/import-datasource-sample.png)

3. Pokračujte na další stránku.

   ![Tlačítko Další stránka pro hledání rozpoznávání](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Krok 2 – přeskočení schopností rozpoznávání

Průvodce podporuje vytvoření [kanálu vnímání dovedností](cognitive-search-concept-intro.md) pro zahrnutí algoritmů Cognitive Services AI do indexování. 

Tento krok prozatím přeskočíme a přejdete přímo na, abyste mohli **přizpůsobit cílový index**.

   ![Přeskočit krok dovednosti rozpoznávání](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Můžete si projít příkladem indexování AI v [rychlém](cognitive-search-quickstart-blob.md) startu nebo [kurzu](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 – konfigurace indexu

Vytvoření indexu je obvykle uplatněno na základě kódu, dokončeno před načtením dat. Jak však tento kurz indikuje, průvodce může vygenerovat základní index pro libovolný zdroj dat, který může procházet. Index vyžaduje minimálně název a kolekci polí; jedna z polí by měla být označena jako klíč dokumentu k jedinečné identifikaci každého dokumentu. Kromě toho můžete určit analyzátory jazyka nebo moduly pro návrhy, pokud požadujete automatické dokončování nebo navrhované dotazy.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu* , které řídí způsob použití pole.

* Možnosti získatelné **znamená,** že se zobrazí v seznamu výsledků hledání. Můžete označit jednotlivá pole jako vypnutá omezení pro výsledky hledání zrušením zaškrtnutí tohoto políčka, například u polí používaných pouze ve výrazech filtru.
* **Klíč** je jedinečný identifikátor dokumentu. Vždycky se jedná o řetězec, který je povinný.
* **Filtrovatelné**, seřaditelné a **plošky** **určují, zda**jsou pole používána ve filtru, řazení nebo v navigační struktuře s omezujícími vlastnostmi.
* **Prohledávatelné** znamená, že pole je zahrnuté do fulltextového vyhledávání. Řetězce lze prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Požadavky na úložiště se neliší v důsledku vašeho výběru. Například pokud nastavíte **atribut získat** u více polí, požadavky na úložiště se nevrátí.

Ve výchozím nastavení Průvodce hledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. *Řetězce* jsou atributy s možností **získat a** **prohledávatelné**. *Celá čísla jsou typu* **s možnou**hodnotou, **filtrovatelné**, **seřaditelné**a **plošky**.

1. Přijměte výchozí hodnoty. 

   Pokud průvodce znovu spustíte podruhé pomocí stávajícího zdroje dat hotelů, index nebude nakonfigurovaný s výchozími atributy. V budoucích importech bude nutné vybrat atributy ručně. 

   ![Index vygenerovaných hotelů](media/search-get-started-portal/hotelsindex.png)

2. Pokračujte na další stránku.

   ![Další stránka – vytvořit indexer](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Krok 4 – Konfigurace indexeru

Pořád v průvodci **importem dat** klikněte na **indexer** > **název**a zadejte název indexeru.

Tento objekt definuje spustitelný proces. Můžete ji umístit do opakujícího se plánu, ale teď použijte výchozí možnost ke spuštění indexeru hned hned.

Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

  ![Indexer hotelů](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Průběh monitorování

Průvodce by vás měl přenést do seznamu indexerů, kde můžete monitorovat průběh. V případě samoobslužné navigace přejděte na stránku Přehled a klikněte na **indexery**.

Aby portál mohl aktualizovat stránku, může trvat několik minut, ale v seznamu byste měli vidět nově vytvořený indexer se stavem "probíhá" nebo "úspěch" spolu s počtem indexovaných dokumentů.

   ![Zpráva o průběhu indexeru](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Zobrazit index

Hlavní stránka služby poskytuje odkazy na prostředky vytvořené ve službě Azure Search.  Pokud chcete zobrazit právě vytvořený index, klikněte na **indexy** ze seznamu odkazů. 

   ![Seznam indexů na řídicím panelu služby](media/search-get-started-portal/indexes-list.png)

V tomto seznamu můžete kliknout na index s *ukázkami hotelů* , který jste právě vytvořili, a zobrazit schéma indexu. a volitelně můžete přidat nová pole. 

Karta **pole** zobrazuje schéma indexu. Posuňte se do dolní části seznamu a zadejte nové pole. Ve většině případů nelze změnit existující pole. Existující pole mají v Azure Search fyzickou reprezentaci, takže nejsou ani v kódu. Chcete-li v podstatě změnit existující pole, vytvořte nový index, který vyřadí původní.

   ![definice vzorového indexu](media/search-get-started-portal/sample-index-def.png)

Jiné konstrukce, jako jsou například profily bodování a možnosti CORS, lze kdykoli přidat.

Abyste jasně pochopili, co můžete a nemůžete upravit během návrhu rejstříku, je třeba zobrazit možnosti definice indexu za minutu. Šedé možnosti jsou indikátorem, že hodnotu nelze upravit ani odstranit. 

## <a name="query-index"></a>Dotaz pomocí Průzkumníka vyhledávání

Přesun předem byste teď měli mít index vyhledávání, který je připravený k dotazování na základě integrovaného dotazu [**Průzkumníka hledání**](search-explorer.md) . Poskytuje vyhledávací pole, abyste mohli testovat libovolné řetězce dotazu.

**Průzkumník služby Search** je vybaven pouze pro [zpracování požadavků REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), ale přijímá syntaxi pro [jednoduchou syntaxi dotazu](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i [úplný analyzátor dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)a navíc všechny parametry hledání dostupné ve [vyhledávacím dokumentu REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) Operations.

> [!TIP]
> Následující kroky jsou popsané v 6m08s na video s [přehledem Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Na panelu příkazů klikněte na **Průzkumník vyhledávání** .

   ![Příkaz Průzkumníka hledání](media/search-get-started-portal/search-explorer-cmd.png)

2. V rozevíracím seznamu **index** vyberte možnost *hotely-ukázka*. Klikněte na rozevírací seznam **verze rozhraní API** , abyste viděli, která rozhraní REST API jsou k dispozici. Pro následující dotazy použijte všeobecně dostupnou verzi (2019-05-06).

   ![Příkazy indexu a rozhraní API](media/search-get-started-portal/search-explorer-changeindex.png)

3. Do panelu hledání vložte následující řetězce dotazu a klikněte na tlačítko **Hledat**.

   ![Řetězec dotazu a tlačítko Hledat](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Příklady dotazů

Můžete zadat podmínky a fráze, podobně jako na to, co můžete dělat v Bingu nebo Google Search, nebo plně určené výrazy dotazu. Výsledky se vrátí jako podrobné dokumenty JSON.

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-query-searchspa"></a>Příklad (dotaz na řetězec): `search=spa`

* Parametr **Search** se používá k zadání klíčového slova pro fulltextové vyhledávání. v tomto případě vrátí data hotelu pro ty, které obsahují *Spa* , do libovolného vyhledávacího pole v dokumentu.

* **Průzkumník vyhledávání** vrátí výsledek ve formátu JSON, který je podrobný a těžko čitelný, pokud dokumenty mají hustou strukturu. To je úmyslné; viditelnost celého dokumentu je důležitá pro účely vývoje, zejména při testování. Pro lepší uživatelské prostředí budete muset napsat kód, který [zpracovává výsledky hledání](search-pagination-page-layout.md) k uvedení důležitých prvků.

* Dokumenty se skládají ze všech polí označených jako "načístelné" v indexu. Chcete-li zobrazit atributy indexu na portálu, klikněte na možnost *hotely – ukázka* v seznamu **indexy** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Příklad (parametrizovaný dotaz): `search=spa&$count=true&$top=10`

* Symbol **&** slouží k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí.

* Parametr **$Count = true** vrátí celkový počet všech vrácených dokumentů. Tato hodnota se zobrazí u horní části výsledků hledání. Dotazy filtru můžete ověřit pomocí sledování změn hlášených **$Count = true**. Menší počty označují, že váš filtr funguje.

* **$Top = 10** vrátí nejvyšší seřazený 10 dokumentů z celkového počtu. Ve výchozím nastavení Azure Search vrátí prvních 50 nejlepších shod. Tuto hodnotu můžete zvýšit nebo snížit prostřednictvím **$Top**.

### <a name="filter-query"></a>Filtrovat dotaz

Filtry jsou součástí požadavků hledání při připojení parametru **$Filter** . 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Příklad (filtrováno): `search=beach&$filter=Rating gt 4`

* Parametr **$Filter** vrátí výsledky, které odpovídají zadaným kritériím. V tomto případě hodnocení větší než 4.

* Syntaxe filtru je konstrukce OData. Další informace najdete v tématu [Filter syntax OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a>Omezující vlastnost dotazu

Filtry omezující vlastnosti jsou součástí požadavků hledání. Pomocí parametru omezující vlastnosti můžete vracet agregovaný počet dokumentů, které odpovídají hodnotě omezující vlastnosti, kterou zadáte.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Příklad (s omezujícím omezením rozsahu): `search=*&facet=Category&$top=2`

* **hledání =** * je prázdné vyhledávání. Prázdná hledání prohledají všechno. Jedním z důvodů odeslání prázdného dotazu je vyfiltrování nebo omezující vlastnost nad úplnou sadou dokumentů. Například chcete, aby se navigační struktura omezující vlastnosti sestávat ze všech hotelů v indexu.
* **omezující vlastnost** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrátí kategorie a počet. V takovém případě jsou kategorie založené na poli, které se pohodlně označuje jako *kategorie*. V Azure Search není žádná agregace, ale je možné přibližnou agregaci pomocí `facet`, která poskytuje počet dokumentů v každé kategorii.

* **$Top = 2** přináší zpět dva dokumenty a ilustruje, že můžete použít `top` ke snížení nebo zvýšení výsledků.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Příklad (omezující vlastnost na číselné hodnoty): `search=spa&facet=Rating`

* Tento dotaz je omezující vlastnost pro hodnocení na základě textu, který slouží k vyhledávání *hesla*. Termín *hodnocení* lze zadat jako omezující vlastnost, protože pole je označeno jakoelné, filtrovatelné a plošky v indexu a hodnoty, které obsahuje (číslo, 1 až 5), jsou vhodné pro kategorizaci výpisů do skupin.

* Omezující vlastnosti mohou být pouze pole, která lze filtrovat. Ve výsledcích lze vracet pouze pole s možnostmi k dispozici.

* Pole *hodnocení* je plovoucí desetinná čárka dvojitá přesnost a seskupení bude podle přesné hodnoty. Další informace o seskupení podle intervalu (například hodnocení 3 hvězdičky, hodnocení 4 hvězdičkami atd.) najdete v tématu [implementace omezujících možností navigace v Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a>Zvýraznit výsledky hledání

Zvýrazňování hledaného znaku odkazuje na formátování textu odpovídajícího klíčovému slovu, zadané shody se nacházejí v konkrétním poli. Pokud je váš hledaný výraz hluboko ukryto v popisu, můžete přidat zvýraznění přístupů, aby bylo snazší je označit.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Příklad (zvýrazňovač): `search=beach&highlight=Description`

* V tomto příkladu je ve formátovaných *slovech* snazší místo v poli Popis.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Příklad (Lingvistická analýza): `search=beaches&highlight=Description`

* Fulltextové vyhledávání rozpozná základní variace ve wordových formulářích. V takovém případě výsledky hledání obsahují zvýrazněný text "pláž", v případě hotelů, který má toto slovo v jejich prohledávatelné pole, v reakci na klíčové slovo "pláže". V důsledku lingvistické analýzy se můžou ve výsledcích zobrazit různé formy stejného slova. 

* Azure Search podporuje analyzátory 56 od Lucene i od Microsoftu. Výchozím použitím Azure Search je standardní analyzátor Lucene.

### <a name="fuzzy-search"></a>Vyzkoušení přibližného vyhledávání

Ve výchozím nastavení nesprávně napsané výrazy dotazu, jako je například *Seatle* pro "Seattle", nevrátí shody v typickém hledání. Následující příklad nevrátí žádné výsledky.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Příklad (špatně napsaný termín, neošetřený): `search=seatle`

Pro zpracování chybných pravopisů můžete použít hledání přibližných výsledků. Vyhledávání s fuzzy logikou je povoleno, pokud použijete úplnou syntaxi dotazů Lucene, ke které dojde, když provedete dvě věci: nastavte pro dotaz **queryType = Full** a přidejte **~** k hledanému řetězci.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Příklad (nesprávně napsaný termín, zpracováno): `search=seatle~&queryType=full`

Tento příklad nyní vrátí dokumenty, které obsahují shodné položky "Praha".

Pokud není zadán parametr **queryType** , je použit výchozí Analyzátor jednoduchých dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat úplnou syntaxi.

Hledání přibližných výsledků a hledání pomocí zástupných znaků má vliv na výstup hledání. V těchto formátech dotazů se neprovádí jazyková analýza. Před použitím vyhledávání přibližných hodnot a zástupných znaků si projděte, [Jak funguje fulltextové vyhledávání v Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) a vyhledejte část o výjimkách lexikálních analýz.

Další informace o scénářích dotazů povolených úplným analyzátorem dotazů naleznete [v tématu Syntaxe dotazů Lucene v Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a>Vyzkoušet geoprostorové hledání

Geoprostorové hledání je podporováno prostřednictvím [modelu EDM. GeographyPoint datový typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) u pole obsahujícího souřadnice. Hledání v angličtině je typ filtru, který je určen v [syntaxi Filter OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Příklad (filtry geografické souřadnice): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky menší než 5 kilometrů od daného bodu (určené jako souřadnice zeměpisné šířky a délky). Přidáním **$Count**můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice.

Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci "najít blízko mě" nebo používá navigaci v mapě. Nejedná se však o fulltextové vyhledávání. Pokud máte požadavky uživatelů na hledání města nebo země nebo oblasti podle jména, přidejte do souřadnic také pole obsahující názvy měst nebo země nebo oblasti.

## <a name="takeaways"></a>Poznatky

V tomto kurzu se seznámíte s rychlým úvodem Azure Search používání Azure Portal.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce **importem dat** . Dozvěděli jste se o [indexerech](search-indexer-overview.md)a také o základní pracovní postup pro návrh indexu, včetně [podporovaných úprav publikovaného indexu](https://docs.microsoft.com/rest/api/searchservice/update-index).

Pomocí **Průzkumníka vyhledávání** v Azure Portal jste se seznámili se základní syntaxí dotazů prostřednictvím praktických příkladů, které ukázaly klíčové funkce, jako jsou filtry, zvýrazňování přístupů, přibližné vyhledávání a geografická hledání.

Zjistili jste také, jak na portálu najít indexy, indexery a zdroje dat. Vzhledem k novému zdroji dat v budoucnu můžete pomocí portálu rychle ověřit jeho definice nebo kolekce polí s minimálním úsilím.

## <a name="clean-up"></a>Vyčistit

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Další Azure Search můžete prozkoumat pomocí programových nástrojů:

* [Vytvoření indexu pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Vytvoření indexu pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Vytvoření indexu pomocí post nebo Fiddler a rozhraní REST API Azure Search](search-get-started-postman.md)
