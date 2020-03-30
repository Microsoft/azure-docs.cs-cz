---
title: Vytvoření indexu vyhledávání na webu Azure Portal
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak vytvořit index pro Azure Cognitive Search pomocí integrovaného návrháře indexů portálu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112847"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Vytvoření indexu Azure Cognitive Search na portálu

Azure Cognitive Search obsahuje předdefinovaný návrhář indexu na portálu užitečný pro prototypy nebo vytvoření [vyhledávacího indexu](search-what-is-an-index.md) hostovaného ve vaší službě Azure Cognitive Search. Nástroj se používá pro konstrukci schématu. Při uložení definice prázdný index se plně vyjádřené v Azure Cognitive Search. Jak jej načtete s prohledávatelným obsahem, je jen na vás.

Návrhář indexu je pouze jeden přístup pro vytvoření indexu. Případně můžete vytvořit i načíst index pomocí [Průvodce importem dat](search-get-started-portal.md). Průvodce pracuje pouze s indexy, které vytvoří sám. Programově můžete vytvořit index pomocí rozhraní API [.NET](search-create-index-dotnet.md) nebo [REST.](search-create-index-rest-api.md)

## <a name="start-index-designer"></a>Spustit návrháře indexu

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **Všechny služby** na panelu odkazů můžete vyhledávat stávající „vyhledávací služby“ v rámci aktuálního předplatného. 

2. Klepněte na odkaz **Přidat rejstřík** v panelu příkazů v horní části stránky.

   ![Přidání odkazu rejstříku na panel upříkazů](media/search-create-index-portal/add-index.png "Přidání odkazu rejstříku na panel upříkazů")

3. Pojmenujte svůj index Azure Cognitive Search. Názvy indexů jsou odkazovány v operacích indexování a dotazů. Název indexu se stane součástí adresy URL koncového bodu používané ho při připojení k indexu a pro odesílání požadavků HTTP v rozhraní REST ROZHRANÍ AZURE Cognitive Search.

   * Začněte písmenem.
   * Název může obsahovat jenom malá písmena, číslice nebo pomlčky (-).
   * Délka je omezená na 60 znaků.

## <a name="add-fields"></a>Přidání polí

Kompozice indexu obsahuje *kolekci Pole*, která definuje prohledávatelná data v indexu. Kolekce polí celkem určuje strukturu dokumentů, které nahrajete samostatně. Kolekce Fields obsahuje povinná a volitelná pole s názvem a zadali s atributy indexu, které určují, jak lze pole použít.

1. Přidejte pole pro úplné určení dokumentů, které budete nahrát, nastavení [datového typu](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pro každý z nich. Pokud se například dokumenty skládají z *názvu hotelu*, *názvu hotelu*, *adresy*, *města*a *oblasti*, vytvořte odpovídající pole pro každý z nich v indexu. Nápovědu k nastavení atributů naleznete [v pokynech k návrhu v následující části.](#design)

1. Pokud jsou příchozí data hierarchické povahy, schéma by mělo obsahovat [složité typy](search-howto-complex-data-types.md) představující vnořené struktury. Vestavěná ukázková datová sada Hotels ilustruje složité typy pomocí adresy (obsahuje více dílčích polí), která má s každým hotelem vztah 1:1, a kolekci komplexů Rooms, kde je ke každému hotelu přidruženo více pokojů. 

1. Zadejte *klíčové* pole typu Edm.String. Klíčové pole je povinné pro každý index Azure Cognitive Search a musí to být řetězec. Hodnoty pro toto pole musí jednoznačně identifikovat každý dokument. Ve výchozím nastavení se pole jmenuje *id*, ale můžete ho přejmenovat, pokud bude řetězec splňovat [pravidla pojmenování](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Pokud například kolekce polí obsahuje *hotel-id*, zvolíte ji pro klíč. 

1. Nastavte atributy v každém poli. Návrhář indexu vyloučí všechny atributy, které jsou pro datový typ neplatné, ale nenavrhuje, co má být zahrnuto. V pokynech v další části porozumíte tomu, k čemu jsou atributy určeny.

    Dokumentace k rozhraní API azure cognitive search obsahuje příklady kódu s jednoduchým indexem *hotelů.* Na následujícím snímku obrazovky můžete vidět definici indexu, včetně analyzátoru francouzského jazyka určeného během definice indexu, který můžete znovu vytvořit jako cvičné cvičení na portálu.

    ![Demo index hotelů](media/search-create-index-portal/field-definitions.png "Demo index hotelů")

1. Po dokončení klikněte na **Vytvořit,** abyste index uložili a vytvořili.

<a name="design"></a>

## <a name="set-attributes"></a>Nastavit atributy

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

Analyzátory a moduly pro návrhy jsou přidružené k polím před uložením indexu. Nezapomeňte přidat analyzátory jazyka nebo návrhy do definice indexu při jeho vytváření.

Pole řetězců jsou často označená jako ** Searchable** a **Retrievable**. Pole použitá k zúžení výsledků hledání obsahují **Sortable** (Jde řadit), **Filterable** (Filtrovatelné) a **Facetable** (Kategorizovatelné).

Atributy polí určují, jak se pole používá, například jestli se používá ve fulltextovém vyhledávání, fasetové navigaci, operacích řazení a tak dále. Následující tabulka popisuje každý atribut.

|Atribut|Popis|  
|---------------|-----------------|  
|**Prohledávatelné**|Fulltextově prohledávatelné, lze provést lexikální analýzu, jako je dělení slov během indexování. Pokud nastavíte prohledávatelné pole na hodnotu jako „slunečný den“, interně se rozdělí na jednotlivé tokeny „slunečný“ a „den“. Podrobnosti najdete v článku [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|**Filtrovatelné**|Odkazované v dotazech **$filter**. Ve filtrovatelných polích typu `Edm.String` nebo `Collection(Edm.String)` nejdou dělit slova, takže se dají porovnávat jenom na přesné shody. Pokud například nastavíte takové pole f na „sunny day“, `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` ano. |  
|**Sortable**|Ve výchozím nastavení systém řadí výsledky podle skóre (bodů), můžete ale nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nejdou nastavit na **sortable**. |  
|**facetable**|Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet nalezených položek podle kategorie (například hotely v konkrétním městě). Tuto možnost nejde použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String`, která jsou **filterable**, **sortable** nebo **facetable**, můžou být dlouhé maximálně 32 kilobajtů. Podrobnosti najdete v článku [Vytvoření indexu (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Jedinečný identifikátor pro dokumenty v indexu. Jako pole key se musí zvolit právě jedno pole a musí být typu `Edm.String`.|  
|**retrievable**|Určuje, jestli může být pole vrácené ve výsledku hledání. To je užitečné, když chcete použít pole (například *zisková marže*) jako filtrovací, řadicí a bodovací mechanismus, ale nechcete, aby pole bylo viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole typu `key`.|  

## <a name="next-steps"></a>Další kroky

Po vytvoření indexu Azure Cognitive Search můžete přejít k dalšímu kroku: [nahrát prohledávatelná data do indexu](search-what-is-data-import.md).

Případně můžete také provést [hlubší pohled na indexy](search-what-is-an-index.md). Kromě kolekce Pole index také určuje analyzátory, moduly pro návrhy, profily vyhodnocování (bodovací profily) a nastavení CORS. Portál poskytuje stránky se záložkami pro definování nejběžnějších elementů: polí, analyzátorů a modulů pro návrhy. K vytvoření nebo úpravě jiných elementů můžete použít rozhraní REST API nebo .NET SDK.

## <a name="see-also"></a>Viz také

 [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md)  
 [Rozhraní REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

