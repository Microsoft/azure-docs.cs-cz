---
title: Vytvoření indexu vyhledávání v Azure Portal
titleSuffix: Azure Cognitive Search
description: Naučte se vytvořit index pro Azure Kognitivní hledání pomocí integrovaného návrháře indexů portálu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112847"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Vytvoření indexu služby Azure Kognitivní hledání na portálu

Azure Kognitivní hledání obsahuje integrovaného návrháře indexů na portálu, který je užitečný pro vytváření prototypů nebo vytvoření [indexu vyhledávání](search-what-is-an-index.md) hostovaného ve službě Azure kognitivní hledání. Nástroj se používá pro konstrukci schématu. Když definici uložíte, prázdný index se v Azure Kognitivní hledání plně vyjádří. Způsob, jakým se dá s prohledávatelným obsahem načítat, je až na vás.

Návrhář indexů je pouze jeden přístup pro vytvoření indexu. Případně můžete index vytvořit a načíst pomocí [Průvodce importem dat](search-get-started-portal.md). Průvodce funguje pouze s indexy, které vytváří sám sebe. Prostřednictvím kódu programu můžete vytvořit index prostřednictvím rozhraní [.NET](search-create-index-dotnet.md) nebo rozhraní [REST](search-create-index-rest-api.md) API.

## <a name="start-index-designer"></a>Spustit návrháře indexů

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **Všechny služby** na panelu odkazů můžete vyhledávat stávající „vyhledávací služby“ v rámci aktuálního předplatného. 

2. Klikněte na odkaz **Přidat index** na panelu příkazů v horní části stránky.

   ![Přidat odkaz na index na panelu příkazů](media/search-create-index-portal/add-index.png "Přidat odkaz na index na panelu příkazů")

3. Pojmenujte svůj index služby Azure Kognitivní hledání. Na názvy indexů se odkazuje při operacích indexování a dotazování. Název indexu se bude součástí adresy URL koncového bodu, která se používá pro připojení k indexu a odesílání požadavků HTTP ve službě Azure Kognitivní hledání REST API.

   * Začněte písmenem.
   * Název může obsahovat jenom malá písmena, číslice nebo pomlčky (-).
   * Délka je omezená na 60 znaků.

## <a name="add-fields"></a>Přidat pole

Kompozice indexu obsahuje *kolekci Pole*, která definuje prohledávatelná data v indexu. Kolekce pole zcela určuje strukturu dokumentů, které odesíláte samostatně. Kolekce polí obsahuje povinná a volitelná pole s názvem a typem s atributy indexu, které určují, jak lze pole použít.

1. Přidejte pole pro úplné zadání dokumentů, které nahrajete, a nastavte [datový typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pro každé z nich. Například pokud se dokumenty skládají z *hotelu-ID*, *hotelového názvu*, *adresy*, *města*a *oblasti*, vytvořte odpovídající pole pro každou z nich v indexu. Nápovědu k nastavení atributů najdete [v části Průvodce návrhem v níže uvedené části](#design) .

1. Pokud jsou příchozí data hierarchicky uspořádaná, vaše schéma by mělo zahrnovat [komplexní typy](search-howto-complex-data-types.md) , které reprezentují vnořené struktury. Integrovaná Ukázková sada dat, hotely, znázorňuje komplexní typy pomocí adresy (obsahuje několik dílčích polí), které mají vztah 1:1 s každým hotelem, a prostorově komplexní kolekci, kde je k jednotlivým hotelům přidruženo více místností. 

1. Zadejte *klíčové* pole typu EDM. String. Klíčové pole je povinné pro každý index Azure Kognitivní hledání a musí to být řetězec. Hodnoty pro toto pole musí jednoznačně identifikovat každý dokument. Ve výchozím nastavení se pole jmenuje *id*, ale můžete ho přejmenovat, pokud bude řetězec splňovat [pravidla pojmenování](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Například pokud vaše pole kolekce obsahují *hotely-ID*, je vhodné si pro svůj klíč vybrat. 

1. Nastavte atributy pro každé pole. Návrhář indexu vylučuje všechny atributy, které jsou pro datový typ neplatné, ale nenavrhuje, co zahrnout. Přečtěte si pokyny v následující části, abyste zjistili, jaké jsou atributy pro.

    Dokumentace k rozhraní API pro Azure Kognitivní hledání obsahuje příklady kódu, které obsahují jednoduchý index *hotelů* . Na následujícím snímku obrazovky vidíte definici indexu včetně analyzátoru francouzského jazyka zadaného během definice indexu, který můžete znovu vytvořit jako cvičení na portálu.

    ![Ukázkový index hotelů](media/search-create-index-portal/field-definitions.png "Ukázkový index hotelů")

1. Po dokončení klikněte na **vytvořit** , aby se tento index uložil a vytvořil.

<a name="design"></a>

## <a name="set-attributes"></a>Nastavit atributy

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

Analyzátory a moduly pro návrhy jsou přidružené k polím před uložením indexu. Nezapomeňte přidat analyzátory jazyka nebo moduly pro návrhy do definice indexu při jejím vytváření.

Pole řetězců jsou často označená jako  **Searchable** a **Retrievable**. Pole použitá k zúžení výsledků hledání obsahují **Sortable** (Jde řadit), **Filterable** (Filtrovatelné) a **Facetable** (Kategorizovatelné).

Atributy polí určují, jak se pole používá, například jestli se používá ve fulltextovém vyhledávání, fasetové navigaci, operacích řazení a tak dále. Následující tabulka popisuje každý atribut.

|Atribut|Popis|  
|---------------|-----------------|  
|**searchable**|Fulltextově prohledávatelné, lze provést lexikální analýzu, jako je dělení slov během indexování. Pokud nastavíte prohledávatelné pole na hodnotu jako „slunečný den“, interně se rozdělí na jednotlivé tokeny „slunečný“ a „den“. Podrobnosti najdete v článku [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|**filterable**|Odkazované v dotazech **$filter**. Ve filtrovatelných polích typu `Edm.String` nebo `Collection(Edm.String)` nejdou dělit slova, takže se dají porovnávat jenom na přesné shody. Pokud například nastavíte takové pole f na „sunny day“, `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` ano. |  
|**sortable**|Ve výchozím nastavení systém řadí výsledky podle skóre (bodů), můžete ale nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nejdou nastavit na **sortable**. |  
|**facetable**|Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet nalezených položek podle kategorie (například hotely v konkrétním městě). Tuto možnost nejde použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String`, která jsou **filterable**, **sortable** nebo **facetable**, můžou být dlouhé maximálně 32 kilobajtů. Podrobnosti najdete v článku [Vytvoření indexu (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Jedinečný identifikátor pro dokumenty v indexu. Jako pole key se musí zvolit právě jedno pole a musí být typu `Edm.String`.|  
|**retrievable**|Určuje, jestli může být pole vrácené ve výsledku hledání. To je užitečné, když chcete použít pole (například *zisková marže*) jako filtrovací, řadicí a bodovací mechanismus, ale nechcete, aby pole bylo viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole typu `key`.|  

## <a name="next-steps"></a>Další kroky

Po vytvoření indexu služby Azure Kognitivní hledání můžete přejít na další krok: [nahrání prohledávatelných dat do indexu](search-what-is-data-import.md).

Alternativně můžete také pořizovat [hlubší pohled na indexy](search-what-is-an-index.md). Kromě kolekce Pole index také určuje analyzátory, moduly pro návrhy, profily vyhodnocování (bodovací profily) a nastavení CORS. Portál poskytuje stránky se záložkami pro definování nejběžnějších elementů: polí, analyzátorů a modulů pro návrhy. K vytvoření nebo úpravě jiných elementů můžete použít rozhraní REST API nebo .NET SDK.

## <a name="see-also"></a>Viz také

 [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md)  
 [Rozhraní REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

