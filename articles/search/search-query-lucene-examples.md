---
title: Příklady dotazů Lucene pro službu Azure Search | Dokumentace Microsoftu
description: Syntaxe dotazů Lucene pro vyhledávání přibližných shod, vyhledávání blízkých výrazů, zvyšování skóre termínu, hledání regulárního výrazu a hledání pomocí zástupných znaků.
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: liamca
ms.openlocfilehash: 24fa427ad67a953020370a16b4d156c82a0a1cf6
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036662"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Příklady syntaxe dotazů Lucene pro tvorbu dotazů ve službě Azure Search
Při vytváření dotazů pro službu Azure Search, můžete použít buď výchozí [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo alternativním [analyzátor dotazů Lucene ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Analyzátor dotazů Lucene podporuje složitější dotazu konstrukce, jako jsou dotazy v rámci pole, vyhledávání přibližných shod, vyhledávání blízkých výrazů, zvyšování skóre termínu a regulární výraz hledání.

V tomto článku můžete krokovat příklady demonstrující operace dotazu jsou dostupné při používání úplnou syntaxi.

## <a name="viewing-the-examples-in-jsfiddle"></a>Zobrazení v příkladech v JSFiddle

Všechny příklady v tomto článku jsou spustitelné dotazy, které spustily už načtené index vyhledávání v [JSFiddle](https://jsfiddle.net), editor kódu online pro testování skript a HTML. 

K jejich spuštění, klikněte pravým tlačítkem na ukázkových adresách URL dotazu otevřete JSFiddle v samostatném okně prohlížeče.

> [!NOTE]
> Následující příklady využívají indexu vyhledávání skládající se z úlohy nejsou k dispozici na základě datové poskytované [města New York OpenData](https://nycopendata.socrata.com/) iniciativy. Tato data by neměly být zahrnuté v aktuální nebo dokončení. Index je v sandboxu služby od Microsoftu. Není nutné předplatného Azure nebo Azure Search vyzkoušet tyto dotazy.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Vyvolání úplné analýze Lucene

Zadejte všechny příklady v tomto článku **queryType = full** parametr určující, zda analyzátor dotazů Lucene zpracovává úplnou syntaxi vyhledávání. 

**Příklad 1** --klikněte pravým tlačítkem na následující fragment dotazu a otevře se v nové stránky prohlížeče, který načte JSFiddle a spustí dotaz:

* [& queryType = full & hledání = *](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

V novém okně prohlížeče se zdroje jazyka JavaScript a HTML výstup zobrazí vedle sebe. Skript odkazuje na celý dotaz (nejen fragmentu kódu, jak je znázorněno v odkazu). Celý dotaz se zobrazí v adresách URL pro každý příklad. 

Tento dotaz vrací dokumenty z indexu úloh New York City (nycjobs načten v sandboxu služby). Dotaz určuje pro zkrácení, pouze obchodní názvy jsou vráceny. Úplný dotaz vypadá takto:

    https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**SearchFields** parametr omezuje vyhledávání na jenom pole název firmy. **QueryType** je nastavena na **úplné**, které instruuje Azure Search pro účely tohoto dotazu analyzátor dotazů Lucene.

> [!NOTE]
> Další informace o zpracování dotazů, najdete v článku [jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md). Další informace o parametry hledání, najdete v části [hledání dokumentů (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operace fielded dotazu
V příkladech v tomto článku můžete upravit tak, že zadáte **fieldname:searchterm** konstrukce k definování operace fielded dotazu, kde je pole jednoho slova, a hledaný termín je také jedno slovo nebo slovní spojení, volitelně s Logické operátory. Mezi příklady patří následující:

* business_title:(senior NOT junior)
* Stav: ("New York" a "Nové Jersey")

Nezapomeňte vložit více řetězce v uvozovkách, pokud chcete, aby oba řetězce, který se má vyhodnotit jako jedna entita, stejně jako v tomto případě vyhledávání různých měst v poli umístění. Také se ujistěte, operátor, který je velkými písmeny, jak vidíte s NOT a AND.

V zadané pole **fieldname:searchterm** musí být prohledávatelné pole. Zobrazit [vytvoření indexu (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) podrobnosti o použití atributů indexu v definicích polí.

**Příklad 2** --klikněte pravým tlačítkem na tento dotaz vyhledává obchodní produkty s vedoucí termín v nich, ale ne méně zkušení fragment kódu následující dotaz:

* [& queryType = full & hledání = business_title:senior NOT méně zkušení](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Příklad vyhledávání přibližných shod
Přibližné vyhledávání vyhledá odpovídající položky v podmínkách, které mají podobné konstrukce. Za [Lucene dokumentaci](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), vyhledávání přibližných shod, které jsou založeny na [Damerau Levenshtein vzdálenost](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Chcete-li provést vyhledávání přibližných shod, přidejte tilda `~` symbolu na konci jednoho slova s volitelný parametr, hodnotu mezi 0 a 2, která určuje vzdálenost úpravy. Například `blue~` nebo `blue~1` vracel spojovací, modrá a blues.

**Příklad 3** --klikněte pravým tlačítkem na následující fragment dotazu. Tento dotaz vyhledává úlohy se termín přidružení (kde to je zadáno chybně):

* [& queryType = full & Hledat = business_title:asosiate ~](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Přibližné dotazy nejsou [analyzovat](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), což může být překvapivé, pokud očekáváte, že vyplývající nebo lemmatizátor. Lexikální analýzu se provádí pouze v dokončení podmínky (termín dotazu nebo dotaz fráze). Typy dotazů s neúplné podmínky (předponu dotazu, dotaz zástupný znak, regulární výraz dotazu, fuzzy dotazu) jsou přidány přímo do stromu dotazu obcházení fázi analýzy. Pouze transformace provedené na neúplný dotaz podmínky je předpoklady.
>

## <a name="proximity-search-example"></a>Příklad vyhledávání blízkých výrazů
Vyhledávání blízkých výrazů se používají k vyhledání podmínky, která jsou blízko sebe v dokumentu. Vložit tildou "~" symbolu na konci věty následovaný počtem slova, která vytvořit hranici blízkosti. Například "hotelu letiště" přibližně 5 najdou podmínky hotelu a letiště v rámci 5 slov v dokumentu.

**Příklad 4** --klikněte pravým tlačítkem na dotaz. Hledat pro úlohy s označením "vedoucí analytik", kde jsou oddělené oddělovačem více než jedno slovo:

* [& queryType = full & hledání = business_title: "vedoucí analytik" ~ 1](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Příklad 5** – zkuste to znovu odebrání slova mezi termín "vedoucí analytik".

* [& queryType = full & hledání = business_title: "vedoucí analytik" ~ 0](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Příklady zvýšení skóre termínu
Zvyšování skóre termínu odkazuje na hodnocení vyšší, pokud obsahuje Posílený termín, vzhledem k dokumentům, které neobsahují termín dokumentu. Tím se liší od bodovací profily, profily vyhodnocování zvýšení určité pole, nikoli konkrétní podmínky. Následující příklad ilustruje znázorňují rozdíl.

Vezměte v úvahu bodovací profil, který zvyšuje odpovídá určité pole, jako například **žánr** v příkladu musicstoreindex. Zvyšování skóre termínu může použít pro další zvýšení určité hledání podmínkami vyšším než jiné. Například "rock ^ elektronické 2" se zvýšit dokumenty, které obsahují hledaný text v **žánr** vyšší než další prohledávatelná pole v indexu pole. Kromě toho dokumenty, které obsahují hledaný termín "rock" bude provedeno řazení vyšší než "elektronického" výsledkem hodnota boost období (2) hledaný termín.

Zvyšte termín, použijte blikající kurzor, "^", symbol s faktorem zesílení (číslo) na konci se hledaný termín. Čím vyšší faktor zesílení, více odpovídajících termín, budou relativní vůči jiné podmínky hledání. Ve výchozím nastavení je faktor zesílení 1. I když faktor zesílení musí být kladná, může být menší než 1 (například 0,2).

**Příklad 6** --klikněte pravým tlačítkem na dotaz. Hledat úlohy s označením "počítač analytik" kde vidíme nebyly nalezeny žádné výsledky obsahující slova počítače i analytik, ale analytické úlohy jsou v horní části výsledků.

* [& queryType = full & hledání = business_title:computer analytika](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Příklad 7** – zkuste to znovu, tato doba zvýšení skóre výsledků s počítačem termín přes analytik termín Pokud oba slova neexistují.

* [& queryType = full & hledání = business_title:computer ^ 2 analytika](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Příklad regulárního výrazu
Hledání regulárního výrazu najde shodu na základě obsahu mezi lomítka "/", jako zdokumentované v [Třída RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Příklad 8** --klikněte pravým tlačítkem na dotaz. Hledat úlohy s oběma termín vyšší nebo nižší.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

Adresa URL pro účely tohoto příkladu nebude vykreslovat správně, na stránce. Jako alternativní řešení zkopírujte níže uvedenou adresu URL a vložte do adresy URL prohlížeče: `https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Příklad prohledávání zástupných znaků
Obecně rozpoznaná syntaxe můžete použít pro více (\*) nebo jednotné hledání zástupný znak (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů se jeden výraz a ne frázi.

**Příklad 9** --klikněte pravým tlačítkem na dotaz. Hledat úlohy, které obsahují předponu "ProgID' bude to zahrnovat obchodní názvy s podmínkami, programování a programátor v ní.

* [& queryType = full & $select = business_title & hledání = business_title:prog*](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nelze použít * nebo? symbol jako první znak vyhledávání.

## <a name="next-steps"></a>Další postup
Zkuste zadat analyzátor dotazů Lucene ve vašem kódu. Následující odkazy popisují, jak nastavit vyhledávacích dotazů pro .NET a rozhraní REST API. Odkazy používat jednoduchá syntaxe výchozí, budete muset použít, co jste se naučili v tomto článku k určení **queryType**.

* [Dotazování indexu Azure Search pomocí .NET SDK](search-query-dotnet.md)
* [Dotazování indexu Azure Search pomocí rozhraní REST API](search-query-rest-api.md)

## <a name="see-also"></a>Další informace najdete v tématech

 [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
