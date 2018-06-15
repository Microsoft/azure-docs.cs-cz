---
title: Příklady dotazů Lucene pro službu Azure Search | Microsoft Docs
description: Syntaxe dotazů Lucene přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšovat skóre termín, regulární výraz vyhledávání a hledání pomocí zástupných znaků.
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: liamca
ms.openlocfilehash: 46e03834cb307ea103a8794616f6f38227881272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190086"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Příklady syntaxe dotazů Lucene pro tvorbu dotazů ve službě Azure Search
Při vytváření dotazů pro službu Azure Search, můžete použít buď výchozí [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo alternativním [analyzátor dotazů Lucene ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Analyzátor dotazů Lucene podporuje složitější konstruktory dotazu, jako jsou dotazy v rámci pole, přibližné vyhledávání, vyhledávání blízkých výrazů, termín zvyšovat skóre a hledání regulárního výrazu.

V tomto článku můžete krokovat příklady při použití úplnou syntaxi demonstrace operace dotazů, které jsou k dispozici.

## <a name="viewing-the-examples-in-jsfiddle"></a>Zobrazení v příkladech v JSFiddle

Všechny příklady v tomto článku jsou spustitelné dotazy, které spouštění předem načtený index vyhledávání v [JSFiddle](https://jsfiddle.net), editoru online kód pro testování skriptu a HTML. 

Abyste je mohli spustit, klikněte pravým tlačítkem na adresy URL příklad dotazu, otevřete JSFiddle v samostatném okně prohlížeče.

> [!NOTE]
> Následující příklady využít indexu vyhledávání, který se skládá z úloh k dispozici v datové sadě poskytované [OpenData New Yorku města](https://nycopendata.socrata.com/) initiative. Tato data by se neměla považovat aktuální nebo dokončení. Index je v izolovaného prostoru služby poskytované společností Microsoft. Není nutné předplatného Azure nebo Azure Search a zkuste to tyto dotazy.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Postup vyvolání úplné analýza Lucene

Zadejte všechny příklady v tomto článku **typ = úplné** parametr, která určuje, že úplnou syntaxí se zpracovává souborem analyzátor dotazů Lucene vyhledávání. 

**Příklad 1** – klikněte pravým tlačítkem na následující fragment dotazu a otevře se v nové stránky prohlížeče, který načte JSFiddle a spustí dotaz:

* [& Typ = úplné & hledání = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

V nové okno prohlížeče jsou zdroj JavaScript a HTML výstupu zobrazí vedle sebe. Skript odkazuje na celý dotaz (ne jenom fragmentu, jak je znázorněno v odkazu). Adresy URL pro každý příklad ukazuje celý dotaz. 

Tento dotaz vrací dokumenty z New Yorku úlohy index (nycjobs načíst izolovaného prostoru služby). Dotaz jako stručný výtah určuje pouze obchodní názvy jsou vráceny. Úplné podkladového dotazu je následující:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**SearchFields** parametr omezí vyhledávání jenom pole název firmy. **Typ** je nastaven na **úplné**, což dává pokyn Azure Search pomocí analyzátoru dotazů Lucene pro tento dotaz.

> [!NOTE]
> Pro informace o zpracování dotazu, viz [jak úplné textové vyhledávání funguje ve službě Azure Search](search-lucene-query-architecture.md). Další informace o parametry hledání najdete v tématu [vyhledávání dokumentů (služby REST API Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operace fielded dotazů
V příkladech v tomto článku můžete upravit zadáním **fieldname:searchterm** konstrukce k definování operace fielded dotazů, kde je pole jednoho slova a hledaný termín je také slovo nebo frázi, volitelně s Logické operátory. Příklady zahrnují následující:

* business_title:(senior NOT junior)
* Stav: ("Brno" a "Nové Jersey")

Nezapomeňte uvést více řetězce v uvozovkách, pokud chcete, aby oba řetězce, který se má vyhodnotit jako jedna entita, jako v tomto případě hledání dvě odlišné města pole umístění. Také se ujistěte, operátor je velkými písmeny, jak můžete vidět s NOT a AND.

Pole uvedené v **fieldname:searchterm** musí být prohledávatelné pole. V tématu [Create Index (služby REST API Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) podrobnosti o tom, jak se používají atributy indexu v definice polí.

**Příklad 2** – klikněte pravým tlačítkem na tento dotaz vyhledává obchodní produkty s senior termín v je, ale ne méně zkušení následující fragment dotazu:

* [& Typ = úplné & hledání = business_title:senior není méně zkušení](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Příklad přibližné vyhledávání
Vyhledá přibližné vyhledávání shody v podmínkách, které mají podobné konstrukce. Za [Lucene dokumentace](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), přibližné vyhledávání jsou založené na [Damerau Levenshtein vzdálenost](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Chcete-li provést přibližné vyhledávání, připojte tilda "~" symbol na konci slovo s volitelný parametr, hodnotu mezi 0 a 2, která určuje vzdálenost upravit. Například "blue ~" nebo "blue ~ 1" by vrátit blue, modré a pojidlem.

**Příklad 3** --klikněte pravým tlačítkem na následující fragment dotazu. Tento dotaz vyhledává úlohy se přidružení termín (kde je je zadáno chybně):

* [& Typ = úplné & hledání = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Přibližné dotazy nejsou [analyzovali](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), což může být překvapivé, pokud očekáváte, rozklad nebo Lematizace. Lexikální analýzy se provádí pouze na dokončení podmínky (dotazu termín nebo frázi dotazu). Typy dotazů s podmínkami nekompletní (předponu dotazu, dotaz zástupný znak, regulární výraz dotazu, přibližné dotazu) se přidají přímo do stromu dotazu obcházení fázi analýzy. Pouze transformace provést podle podmínek neúplné dotazu je předpoklady.
>

## <a name="proximity-search-example"></a>Příklad hledání bezkontaktní komunikace
Hledání blízkosti se používají k vyhledání podmínky, jsou blízko sebe v dokumentu. Vložit tildou "~" symbol na konci zadání následovaný počtem slova, která vytvoření hranice blízkosti. Například "hotelů letiště" ~ 5 najdete podmínky ubytovací a letiště v rámci 5 slova vzájemně v dokumentu.

**Příklad 4** – klikněte pravým tlačítkem na dotaz. Hledání pro úlohy s označením "senior analytik", kde jsou oddělené oddělovačem více než jeden word:

* [& Typ = úplné & hledání = business_title: "senior analytik" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Příklad 5** – zkuste ji znovu odebrat slova mezi termín "senior analytik".

* [& Typ = úplné & hledání = business_title: "senior analytik" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Termín zvyšovat skóre příklady
Termín zvyšovat skóre odkazuje řazení vyšší, pokud obsahuje boosted termín, relativně k dokumenty, které neobsahují termín dokumentu. To se liší od vyhodnocování profily, profily vyhodnocování zvýšení určitá pole, nikoli konkrétní podmínky. Následující příklad pomáhá znázorňují rozdíl.

Vezměte v úvahu vyhodnocování profil, který zvyšuje odpovídá určitá pole, jako například **genre** v příkladu musicstoreindex. Termín zvyšovat skóre může použít pro další zvýšení určité vyhledávání vyšší než jiné podmínky. Například "rock ^ 2 elektronické" bude zvýšení dokumentů, které obsahují hledaný text v **genre** pole vyšší než ostatní prohledávatelné pole v indexu. Kromě toho dokumentů, které obsahují hledaný termín "rock" se určí vyšší než ostatní hledaný termín "elektronického" v důsledku termín nárůst hodnotu (2).

Chcete-li zvýšit termín, použijte pomocí kurzoru, "^", symbol faktor zesílení (číslo) na konci období hledáte. Čím faktor zesílení, více příslušných podmínek bude relativně k jiné podmínky vyhledávání. Ve výchozím nastavení je faktor zesílení 1. I když faktor zesílení musí být kladná, může být menší než 1 (například 0,2).

**Příklad 6** – klikněte pravým tlačítkem na dotaz. Vyhledejte úlohy s označením "počítač analytik", kde vidíte nebyly nalezeny žádné výsledky s počítači slova a analytických ještě analytik úlohy jsou v horní části výsledky.

* [& Typ = úplné & hledání = business_title:computer analytika](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Příklad 7** – zkuste to znovu, tento čas zvyšovat skóre výsledků s počítačem termín přes analytika termín pokud obě slova nejsou k dispozici.

* [& Typ = úplné & hledání = business_title:computer ^ 2 analytika](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Příklad regulární výraz
Hledání regulárního výrazu najde shoda na základě obsahu mezi lomítka "/", v popsáno, jak [Třída RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Příklad 8** – klikněte pravým tlačítkem na dotaz. Vyhledejte úlohy se buď termín vyšší nebo nižší.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

Adresa URL pro tento příklad nebude správně vykreslovat na stránce. Jako alternativní řešení zkopírujte adresu URL níže a vložte jej do adresu URL prohlížeče: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Příklad hledání zástupný znak
Obecně rozpoznaná syntaxe můžete použít pro více (\*) nebo jednoduchého vyhledávání pomocí zástupných znaků znaku (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jeden termín a ne frázi.

**Příklad 9** – klikněte pravým tlačítkem na dotaz. Vyhledejte úlohy, které obsahují předponu "programové', která by obsahovat obchodní názvy s podmínkami programování a programátory v ní.

* [& Typ = úplné & $select = business_title & hledání = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nelze použít * nebo? symbol jako první znak hledání.

## <a name="next-steps"></a>Další postup
Zkuste zadat analyzátor dotazů Lucene ve vašem kódu. Následující odkazy popisují, jak nastavit vyhledávací dotazy pro rozhraní .NET a REST API. Odkazy pomocí jednoduché syntaxe výchozí, budete muset použít naučili v tomto článku k určení **typ**.

* [Dotazování indexu Azure Search pomocí .NET SDK](search-query-dotnet.md)
* [Dotazování indexu Azure Search pomocí REST API](search-query-rest-api.md)

## <a name="see-also"></a>Další informace najdete v tématech

 [Jak úplné textové vyhledávání funguje ve službě Azure Search](search-lucene-query-architecture.md)