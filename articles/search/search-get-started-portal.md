---
title: Vytvoření indexu vyhledávání v Azure Portal
titleSuffix: Azure Cognitive Search
description: Vytvoření, načtení a dotazování prvního indexu vyhledávání pomocí Průvodce importem dat v Azure Portal. V tomto rychlém startu se pro ukázková data používá fiktivní datová sada hotelu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1be165bfe7cca44e8a928933c3c8fe926ad7d4c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694831"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání v Azure Portal

Vytvořte svůj první index vyhledávání pomocí průvodce **importem dat** a vestavěný ukázkový zdroj dat skládající se z fiktivních dat o hotelu. Průvodce vás provede vytvořením indexu hledání (Hotely-Sample-index), abyste mohli psát zajímavé dotazy během několika minut. 

I když nepoužijete možnosti v tomto rychlém startu, průvodce obsahuje stránku pro obohacení AI, takže můžete extrahovat text a strukturu z obrazových souborů a nestrukturovaného textu. Podobný návod, který zahrnuje obohacení AI, najdete v tématu [rychlý Start: vytvoření rozpoznávání dovednosti](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Požadavky

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání (libovolná úroveň, Libovolná oblast). [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

### <a name="check-for-space"></a>Kontrola místa

Mnoho zákazníků začíná s bezplatnou službou. Úroveň Free je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden.

Podívejte se na stránku Přehled služby a zjistěte, kolik indexů, indexerů a datových zdrojů už máte. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Seznam indexů, indexerů a zdrojů dat":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy provádějí iterace [*indexu*](search-what-is-an-index.md), který obsahuje data s možností vyhledávání, metadata a další konstrukce, které optimalizují určité chování vyhledávání.

Pro tento kurz používáme vestavěnou ukázkovou datovou sadu, kterou lze procházet pomocí [*indexeru*](search-indexer-overview.md) prostřednictvím [Průvodce **importem dat**](search-import-data-portal.md). Indexer je prohledávací modul pro určitý zdroj, který dokáže číst metadata a obsah z podporovaných zdrojů dat Azure. Obvykle se indexer používá programově, ale na portálu můžete k nim přistupovat pomocí průvodce **importem dat** . 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 – spuštění Průvodce importem dat a vytvoření zdroje dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

1. [Vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a na stránce Přehled klikněte na tlačítko **importovat data** na panelu příkazů a vytvořte a naplňte index vyhledávání.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Příkaz pro import dat":::

1. V průvodci klikněte na **připojit k datům**  >  **Ukázka**  >  **hotelů – ukázka**. Tento zdroj dat je integrovaný. Pokud jste vytvořili vlastní zdroj dat, budete muset zadat název, typ a informace o připojení. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Výběr ukázkové datové sady":::

1. Pokračujte na další stránku.

### <a name="step-2---skip-the-enrich-content-page"></a>Krok 2 – přeskočení stránky "obohacení obsahu"

Průvodce podporuje vytvoření [kanálu rozšíření AI](cognitive-search-concept-intro.md) pro zahrnutí algoritmů Cognitive Services AI do indexování. 

Tento krok prozatím přeskočíme a přejdete přímo na, abyste mohli **přizpůsobit cílový index**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Vynechání kroku kognitivních dovedností":::

> [!TIP]
> Můžete si projít příkladem indexování AI v [rychlém](cognitive-search-quickstart-blob.md) startu nebo [kurzu](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 – konfigurace indexu

Pro integrovaný index ukázek hotelů je pro vás definováno výchozí schéma indexu. S výjimkou několika příkladů rozšířeného filtru se dotazy v dokumentaci a ukázky, které cílí na index hotelu, spustí v této definici indexu:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Index vygenerovaných hotelů":::

V případě cvičení založeného na kódu je vytvoření indexu dokončeno před načtením dat. Průvodce importem dat tyto kroky dekondenzuje tak, že generuje základní index pro libovolný zdroj dat, který může procházet. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat. Kromě toho můžete určit analyzátory jazyka nebo moduly pro návrhy, pokud požadujete automatické dokončování nebo navrhované dotazy.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole.

+ **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. Můžete označit jednotlivá pole jako vypnutá omezení pro výsledky hledání zrušením zaškrtnutí tohoto políčka, například u polí používaných pouze ve výrazech filtru.
+ **Klíč** je jedinečný identifikátor dokumentu. Vždycky se jedná o řetězec, který je povinný.
+ **Filtrovatelné**, seřaditelné a **plošky** **určují, zda** jsou pole používána ve filtru, řazení nebo v navigační struktuře s omezujícími vlastnostmi.
+ **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Požadavky na úložiště se neliší v důsledku vašeho výběru. Například pokud nastavíte **atribut získat** u více polí, požadavky na úložiště se nevrátí.

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. *Řetězce* jsou atributy s možností **získat a** **prohledávatelné**. *Celá čísla jsou typu* **s možnou** hodnotou, **filtrovatelné**, **seřaditelné** a **plošky**.

1. Přijměte výchozí hodnoty.

   Pokud průvodce znovu spustíte podruhé pomocí stávajícího zdroje dat hotelů, index nebude nakonfigurovaný s výchozími atributy. V budoucích importech bude nutné vybrat atributy ručně. 

1. Pokračujte na další stránku.

### <a name="step-4---configure-indexer"></a>Krok 4 – Konfigurace indexeru

Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru.

Tento objekt definuje spustitelný proces. Můžete ji umístit do opakujícího se plánu, ale teď použijte výchozí možnost ke spuštění indexeru hned hned.

Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="Indexer hotelů":::

## <a name="monitor-progress"></a>Průběh monitorování

Průvodce by vás měl přenést do seznamu indexerů, kde můžete monitorovat průběh. V případě samoobslužné navigace přejděte na stránku Přehled a klikněte na kartu **indexery** .

Aby portál mohl aktualizovat stránku, může trvat několik minut, ale v seznamu byste měli vidět nově vytvořený indexer se stavem "probíhá" nebo "úspěch" spolu s počtem indexovaných dokumentů.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Zpráva indexeru o průběhu":::

## <a name="view-the-index"></a>Zobrazení indexu

Stránka Přehled služby obsahuje odkazy na prostředky vytvořené ve službě Azure Kognitivní hledání.  Pokud chcete zobrazit právě vytvořený index, klikněte na **indexy** ze seznamu odkazů. 

Počkejte, až se stránka portálu aktualizuje. Po několika minutách by se měl zobrazit index s počtem dokumentů a velikostí úložiště.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Seznam indexů na řídicím panelu služby":::

V tomto seznamu můžete kliknout na index s *ukázkami hotelů* , který jste právě vytvořili, a zobrazit schéma indexu. a volitelně můžete přidat nová pole. 

Karta **pole** zobrazuje schéma indexu. Pokud píšete dotazy a potřebujete ověřit, zda je pole filtrovatelné nebo seřaditelné, zobrazí tato karta tyto atributy.

Posuňte se do dolní části seznamu a zadejte nové pole. I když můžete vždy vytvořit nové pole, ve většině případů nelze změnit existující pole. Existující pole mají ve vyhledávací službě fyzickou reprezentaci, takže nejsou ani v kódu. Chcete-li v podstatě změnit existující pole, vytvořte nový index, který vyřadí původní.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="Ukázková definice indexu":::

Kdykoli lze přidat další konstrukce, jako jsou bodovací profily a možnosti CORS.

Vyhraďte si chvilku na prostudování možností definice indexu, abyste dobře chápali, co všechno můžete při návrhu indexu upravovat, a co naopak ne. Možnosti zobrazené šedě naznačují, že se určitá hodnota nedá upravit nebo odstranit. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Dotaz pomocí Průzkumníka vyhledávání

Teď už byste měli mít vyhledávací index, který je připravený na dotazování pomocí integrované stránky dotazů [**Průzkumník služby Hledání**](search-explorer.md). Ta obsahuje vyhledávací pole, ve kterém si můžete otestovat libovolné řetězce dotazů.

**Průzkumník služby Search** je vybaven pouze pro zpracování [požadavků REST API](/rest/api/searchservice/search-documents), ale přijímá syntaxi pro [jednoduchou syntaxi dotazu](/rest/api/searchservice/simple-query-syntax-in-azure-search) i [úplný analyzátor dotazů Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)a navíc všechny parametry hledání, které jsou k dispozici v [dokumentu hledání REST API](/rest/api/searchservice/search-documents#bkmk_examples) operace.

> [!TIP]
> Následující kroky jsou popsané v 6m08s na video s [přehledem Azure kognitivní hledání](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Příkaz průzkumníka služby Search":::

1. V rozevíracím seznamu **index** vyberte  *hotely-Sample-index*. Klikněte na rozevírací seznam **verze rozhraní API** , abyste viděli, která rozhraní REST API jsou k dispozici. Pro následující dotazy použijte všeobecně dostupnou verzi (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Příkazy rozhraní API a index":::

1. Do panelu hledání vložte následující řetězce dotazu a klikněte na tlačítko **Hledat**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Řetězec dotazu a tlačítko Hledat":::

## <a name="example-queries"></a>Ukázky dotazů

Můžete zadat podmínky a fráze, podobně jako na to, co můžete dělat v Bingu nebo Google Search, nebo plně určené výrazy dotazu. Výsledky se vrátí jako podrobné dokumenty JSON.

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-query-searchspa"></a>Příklad (dotaz na řetězec): `search=spa`

+ Parametr **Search** se používá k zadání klíčového slova pro fulltextové vyhledávání. v tomto případě vrátí data hotelu pro ty, které obsahují *Spa* , do libovolného vyhledávacího pole v dokumentu.

+ **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. To je úmyslné; viditelnost celého dokumentu je důležitá pro účely vývoje, zejména při testování. Pokud chcete uživatelské prostředí zlepšit, je potřeba napsat kód, který [zpracuje výsledky hledání](search-pagination-page-layout.md) a vybere z nich důležité elementy.

+ Dokumenty se skládají ze všech polí, která mají v indexu označení Zobrazitelné. Chcete-li zobrazit atributy indexu na portálu, klikněte na možnost *hotely – ukázka* v seznamu **indexy** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Příklad (parametrizovaný dotaz): `search=spa&$count=true&$top=10`

+ **&** Symbol slouží k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí.

+ Parametr **$Count = true** vrátí celkový počet všech vrácených dokumentů. Tato hodnota se zobrazí v horní části výsledků hledání. Monitorováním změn hlášených parametrem **$count=true** můžete ověřovat filtrovací dotazy. Menší počet výsledků naznačuje, že filtr funguje.

+ **$Top = 10** vrátí nejvyšší seřazený 10 dokumentů z celkového počtu. Ve výchozím nastavení Azure Kognitivní hledání vrátí prvních 50 nejlepších shod. Pomocí parametru **$top** můžete tento počet navýšit nebo snížit.

### <a name="filter-the-query"></a><a name="filter-query"></a>Filtrování dotazu

Filtry se do požadavků hledání zahrnou po připojení parametru **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Příklad (filtrovaný): `search=beach&$filter=Rating gt 4`

+ Parametr **$filter** vrací výsledky odpovídající kritériím, která jste zadali. V tomto případě hodnocení větší než 4.

+ Syntaxe parametru Filter je založená na konstruktech jazyka OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a>Omezení vlastností dotazu

Součástí požadavků hledání jsou filtry omezující vlastnost. Pomocí parametru facet můžete vrátit celkový počet dokumentů odpovídajících hodnotě omezující vlastnosti, kterou zadáte.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Příklad (s omezujícími vlastnostmi a zmenšením rozsahu): `search=*&facet=Category&$top=2`

+ Parametr **search=*** znamená prázdné vyhledávání. Prázdné vyhledávání prohledává všechno. Jedním z důvodů odeslání prázdného dotazu je použití filtru nebo omezující vlastnosti na kompletní sadu dokumentů. Například chcete, aby se navigační struktura omezující vlastnosti sestávat ze všech hotelů v indexu.
+ Parametr **facet** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrací kategorie a počet. V takovém případě jsou kategorie založené na poli, které se pohodlně označuje jako *kategorie*. V Azure Kognitivní hledání neexistuje žádná agregace, ale je možné přibližnou agregaci prostřednictvím `facet` , která poskytuje počet dokumentů v každé kategorii.

+ Parametr **$top=2** vrací dva dokumenty a ilustruje, že parametr `top` můžete použít ke snížení i navýšení počtu výsledků.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Příklad (omezující vlastnost číselných hodnot): `search=spa&facet=Rating`

+ Tento dotaz je omezující vlastnost pro hodnocení na základě textu, který slouží k vyhledávání *hesla*. Termín *hodnocení* lze zadat jako omezující vlastnost, protože pole je označeno jakoelné, filtrovatelné a plošky v indexu a hodnoty, které obsahuje (číslo, 1 až 5), jsou vhodné pro kategorizaci výpisů do skupin.

+ Kategorizovat je možné pouze filtrovatelná pole. Ve výsledcích je možné vrátit pouze zobrazitelná pole.

+ Pole *hodnocení* je plovoucí desetinná čárka dvojitá přesnost a seskupení bude podle přesné hodnoty. Další informace o seskupení podle intervalu (například hodnocení 3 hvězdičky, hodnocení 4 hvězdičkami atd.) najdete v tématu [implementace omezujících možností navigace v Azure kognitivní hledání](./search-faceted-navigation.md#filter-based-on-a-range).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Zvýraznění výsledků hledání

Zvýrazňování shod označuje formátování textu odpovídajícího klíčovému slovu (za předpokladu, že se v konkrétním poli našly shody). Pokud je hledaný termín schovaný v popisu, můžete pomocí zvýrazňování shod usnadnit jeho nalezení.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Příklad (zvýraznění): `search=beach&highlight=Description`

+ V tomto příkladu je ve formátovaných *slovech* snazší místo v poli Popis.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Příklad (lingvistická analýza): `search=beaches&highlight=Description`

+ Fulltextové vyhledávání rozpozná základní variace ve wordových formulářích. V takovém případě výsledky hledání obsahují zvýrazněný text "pláž", v případě hotelů, který má toto slovo v jejich prohledávatelné pole, v reakci na klíčové slovo "pláže". Ve výsledcích se díky lingvistické analýze mohou zobrazit různé tvary téhož slova. 

+ Azure Kognitivní hledání podporuje analyzátory 56 od Lucene i od Microsoftu. Výchozím nastavením používaným službou Azure Kognitivní hledání je standardní analyzátor Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Zkouška vyhledávání přibližných shod

Ve výchozím nastavení nesprávně napsané výrazy dotazu, jako je například *Seatle* pro "Seattle", nevrátí shody v typickém hledání. Následující příklad nevrátí žádné výsledky.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Příklad (chybně zadaný termín bez zpracování): `search=seatle`

Ke zpracování chybně napsaných slov můžete využít vyhledávání přibližných shod. Vyhledávání přibližných shod se povolí v případě, že použijete úplnou syntaxi dotazů Lucene. K tomu je potřeba provést dvě věci: nastavit v dotazu **queryType=full** a připojit k řetězci dotazu znak **~**.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Příklad (chybně zadaný termín se zpracováním): `search=seatle~&queryType=full`

Tento příklad nyní vrátí dokumenty, které obsahují shodné položky "Praha".

Pokud parametr **queryType** není zadaný, použije se výchozí jednoduchý analyzátor dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat celou syntaxi.

Vyhledávání přibližných shod a vyhledávání pomocí zástupných znaků mají vliv na výstup hledání. U těchto formátů dotazů se neprovádí lingvistická analýza. Než začnete používat hledání přibližné a zástupných znaků, přečtěte si, [Jak funguje fulltextové vyhledávání v Azure kognitivní hledání](search-lucene-query-architecture.md#stage-2-lexical-analysis) a vyhledejte část o výjimkách lexikálních analýz.

Další informace o scénářích dotazů povolených úplným analyzátorem dotazů najdete [v tématu Syntaxe dotazů Lucene v Azure kognitivní hledání](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a>Vyzkoušení geoprostorového hledání

 Geoprostorové hledání je podporované prostřednictvím [datového typu edm.GeographyPoint](/rest/api/searchservice/supported-data-types) v polích, které obsahují souřadnice. Geoprostorové hledání je typ filtru určený v [syntaxi jazyka OData pro filtry](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Příklad (filtry geografických souřadnic): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Tento vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky od daného bodu (zadaného pomocí zeměpisné šířky a délky) vzdálené méně než 5 kilometrů. Přidáním parametru **$count** můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice.

Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci typu „najít poblíž“ nebo používá navigaci podle map. Není to ale fulltextové vyhledávání. Pokud máte požadavky uživatelů na hledání města nebo země nebo oblasti podle jména, přidejte do souřadnic také pole obsahující názvy měst nebo země nebo oblasti.

## <a name="takeaways"></a>Shrnutí

V tomto kurzu najdete rychlý Úvod k Azure Kognitivní hledání pomocí Azure Portal.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce pro **Import dat**. Dozvěděli jste se o [indexerech](search-indexer-overview.md) a také o základních pracovních postupech návrhu indexu, včetně [podporovaných úprav publikovaného indexu](/rest/api/searchservice/update-index).

V **průzkumníku služby Search** na webu Azure Portal jste se prostřednictvím praktických příkladů klíčových funkcí, jako jsou filtry, zvýrazňování shod, vyhledávání přibližných shod a geografické vyhledávání, seznámili se syntaxí dotazů.

Zjistili jste také, jak na portálu najít indexy, indexery a zdroje dat. Pomocí portálu můžete v budoucnu u nových zdrojů dat s minimálním úsilím rychle zkontrolovat jejich definice nebo kolekce polí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Pomocí Průvodce portálem vygenerujte webovou aplikaci připravenou k použití, která běží v prohlížeči. Tento průvodce můžete vyzkoušet na malém indexu, který jste právě vytvořili, nebo můžete použít některou z vestavěných ukázkových datových sad, které vám pomůžou využít rozsáhlejší možnosti vyhledávání.

> [!div class="nextstepaction"]
> [Vytvoření ukázkové aplikace na portálu](search-create-app-portal.md)