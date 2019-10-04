---
title: Vytvoření indexu Azure Search v Azure Portal-Azure Search
description: Naučte se vytvořit index pro Azure Search s použitím integrovaného návrháře indexů portálu.
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: heidist
ms.openlocfilehash: 4abef5a3030643d4c7b91d2911f350190972f1eb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937269"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Vytvoření indexu Azure Search na portálu

Azure Search obsahuje integrovaného návrháře indexů na portálu, který je užitečný pro vytváření prototypů nebo vytvoření [indexu vyhledávání](search-what-is-an-index.md) hostovaného v rámci služby Azure Search. Nástroj se používá pro konstrukci schématu. Když definici uložíte, prázdný index se v Azure Search plně vyjádří. Způsob, jakým se dá s prohledávatelným obsahem načítat, je až na vás.

Návrhář indexů je pouze jeden přístup pro vytvoření indexu. Případně můžete index vytvořit a načíst pomocí [Průvodce importem dat](search-get-started-portal.md). Průvodce funguje pouze s indexy, které vytváří sám sebe. Prostřednictvím kódu programu můžete vytvořit index prostřednictvím rozhraní [.NET](search-create-index-dotnet.md) nebo rozhraní [REST](search-create-index-rest-api.md) API.

## <a name="start-index-designer"></a>Spustit návrháře indexů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **všechny služby** na panelu odkazů můžete vyhledat existující "vyhledávací služby" v aktuálním předplatném. 

2. Klikněte na odkaz **Přidat index** na panelu příkazů v horní části stránky.

   ![Přidat odkaz na index na panelu příkazů](media/search-create-index-portal/add-index.png "Přidat odkaz na index na panelu příkazů")

3. Pojmenujte index Azure Search. Na názvy indexů se odkazuje při operacích indexování a dotazování. Název indexu se bude součástí adresy URL koncového bodu používaného pro připojení k indexu a odeslání požadavků HTTP v REST API Azure Search.

   * Začněte písmenem.
   * Používejte jenom malá písmena, číslice nebo pomlčky ("-").
   * Omezte název na 60 znaků.

## <a name="add-fields"></a>Přidat pole

Složení indexů zahrnuje *kolekci polí* definující hledaná data v indexu. Kolekce pole zcela určuje strukturu dokumentů, které odesíláte samostatně. Kolekce polí obsahuje povinná a volitelná pole s názvem a typem s atributy indexu, které určují, jak lze pole použít.

1. Přidejte pole pro úplné zadání dokumentů, které nahrajete, a nastavte [datový typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pro každé z nich. Například pokud se dokumenty skládají z *hotelu-ID*, *hotelového názvu*, *adresy*, *města*a *oblasti*, vytvořte odpovídající pole pro každou z nich v indexu. Nápovědu k nastavení atributů najdete [v části Průvodce návrhem v níže uvedené části](#design) .

1. Pokud jsou příchozí data hierarchicky uspořádaná, vaše schéma by mělo zahrnovat [komplexní typy](search-howto-complex-data-types.md) , které reprezentují vnořené struktury. Integrovaná Ukázková sada dat, hotely, znázorňuje komplexní typy pomocí adresy (obsahuje několik dílčích polí), které mají vztah 1:1 s každým hotelem, a prostorově komplexní kolekci, kde je k jednotlivým hotelům přidruženo více místností. 

1. Zadejte *klíčové* pole typu EDM. String. Klíčové pole je povinné pro každý index Azure Search a musí to být řetězec. Hodnoty pro toto pole musí jednoznačně identifikovat každý dokument. Ve výchozím nastavení se pole nazývá *ID* , ale můžete ho přejmenovat, pokud řetězec splňuje [pravidla pojmenování](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Například pokud vaše pole kolekce obsahují *hotely-ID*, je vhodné si pro svůj klíč vybrat. 

1. Nastavte atributy pro každé pole. Návrhář indexu vylučuje všechny atributy, které jsou pro datový typ neplatné, ale nenavrhuje, co zahrnout. Přečtěte si pokyny v následující části, abyste zjistili, jaké jsou atributy pro.

    Dokumentace k rozhraní Azure Search API obsahuje příklady kódu, které obsahují jednoduchý index *hotelů* . Na následujícím snímku obrazovky vidíte definici indexu včetně analyzátoru francouzského jazyka zadaného během definice indexu, který můžete znovu vytvořit jako cvičení na portálu.

    ![](media/search-create-index-portal/field-definitions.png "Ukázka ukázkového indexu hotelů") v hotely

1. Po dokončení klikněte na **vytvořit** , aby se tento index uložil a vytvořil.

<a name="design"></a>

## <a name="set-attributes"></a>Nastavit atributy

I když můžete kdykoli přidat nová pole, existující definice polí jsou v době životnosti indexu zamčené. Z tohoto důvodu vývojáři obvykle používají portál pro vytváření jednoduchých indexů, testování nápadů nebo použití stránek portálu k vyhledání nastavení. Častější iterace při návrhu indexu je efektivnější, pokud budete postupovat podle přístupu založeného na kódu, abyste mohli index snadno sestavit.

Analyzátory a moduly pro návrhy jsou přidruženy k polím před uložením indexu. Nezapomeňte přidat analyzátory jazyka nebo moduly pro návrhy do definice indexu při jejím vytváření.

Pole řetězců jsou často označena jako **prohledávatelné** **a**získatelné. Mezi pole, která se používají k zúžení výsledků hledání, patří **seřaditelné**, **filtrovatelné**a **plošky**.

Atributy pole určují, jak se pole používá, například to, jestli se používá při fulltextovém vyhledávání, omezující navigaci, operacích řazení a tak dále. V následující tabulce jsou popsány jednotlivé atributy.

|Atribut|Popis|  
|---------------|-----------------|  
|**prohledávatelná**|Fulltextově prohledávatelné, podléhající lexikální analýze, jako je dělení slov při indexování. Pokud nastavíte prohledávatelné pole na hodnotu, třeba "Slunečné Day", interně se rozdělí na jednotlivé tokeny "Slunečné" a "Day". Podrobnosti najdete v tématu [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|**Filterable**|Odkazováno v **$filterch** dotazech. Filtrovatelné pole typu `Edm.String` nebo `Collection(Edm.String)` nezpůsobují zalamování slov, takže porovnání jsou pouze pro přesné shody. Pokud například nastavíte takové pole f na "Slunečné Day", `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` bude. |  
|**seřaditelné**|Ve výchozím nastavení systém seřadí výsledky podle skóre, ale můžete nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nelze **Seřadit**. |  
|**kategorizovatelné**|Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet přístupů podle kategorie (například hotely v určitém městě). Tuto možnost nelze použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String`, která lze **filtrovat**, **seřaditelné**nebo **plošky** , mohou mít délku maximálně 32 kilobajtů. Podrobnosti najdete v tématu [vytvoření indexu (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**zkrat**|Jedinečný identifikátor dokumentů v indexu. Jako klíčové pole musí být zvolené přesně jedno pole a musí být typu `Edm.String`.|  
|**Retrievable**|Určuje, zda může být pole vráceno ve výsledku hledání. To je užitečné v případě, že chcete použít pole (například *ziskové marže*) jako filtr, řazení nebo mechanismus bodování, ale nechcete, aby bylo pole viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole `key`.|  

## <a name="next-steps"></a>Další kroky

Po vytvoření indexu Azure Search můžete přejít k dalšímu kroku: [nahrání prohledávatelných dat do indexu](search-what-is-data-import.md).

Alternativně můžete také pořizovat [hlubší pohled na indexy](search-what-is-an-index.md). Kromě kolekce polí index také určuje analyzátory, moduly pro návrhy, profily vyhodnocování a nastavení CORS. Portál poskytuje stránky s kartami k definování nejběžnějších prvků: pole, analyzátory a moduly pro návrhy. Chcete-li vytvořit nebo upravit jiné prvky, můžete použít sadu REST API nebo .NET SDK.

## <a name="see-also"></a>Viz také:

 [Jak funguje úplné hledání textu](search-lucene-query-architecture.md)  
 [Služba Search REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

