---
title: Vytvoření indexu Azure Search na webu Azure portal – Azure Search
description: Informace o vytvoření indexu pro službu Azure Search pomocí návrhářů integrované portálu indexu.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4bba8b41418dadad1b241d60ab0b7aeee4c046d7
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316705"
---
# <a name="how-to-create-an-azure-search-index-using-the-azure-portal"></a>Vytvoření indexu Azure Search pomocí webu Azure portal

Služba Azure Search obsahuje index na integrované návrháře na portálu, které jsou užitečné pro prototypů nebo vytváření [indexu vyhledávání](search-what-is-an-index.md) hostované ve službě Azure Search. Nástroj se používá pro konstrukci schématu. Při ukládání definice indexu prázdný stane plně vyjádřené ve službě Azure Search. Jak ho načíst data pomocí prohledávatelných dat je na vás.

Návrháře indexů je pouze jedním z přístupů pro vytvoření indexu. Prostřednictvím kódu programu, můžete vytvořit index pomocí [.NET](search-create-index-dotnet.md) nebo [REST](search-create-index-rest-api.md) rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a [službu Azure Search](search-create-service-portal.md).

## <a name="open-index-designer-and-name-an-index"></a>Otevřete návrháře indexů a pojmenujte index

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **Všechny služby** na panelu odkazů můžete vyhledávat stávající „vyhledávací služby“ v rámci aktuálního předplatného. 

2.  Klikněte na tlačítko **Přidat index** na panelu příkazů v horní části stránky.

3. Pojmenujte si index Azure Search. Index názvy jsou odkazovány v operace indexování a dotazu. Název indexu se stane součástí adresy URL koncového bodu u připojení k indexu a pro posílání žádostí HTTP v rozhraní REST API služby Azure Search.

   * Začněte písmenem.
   * Název může obsahovat jenom malá písmena, číslice nebo pomlčky (-).
   * Délka je omezená na 60 znaků.

## <a name="define-the-fields-of-your-index"></a>Definujte pole indexu

Kompozice indexu obsahuje *kolekci Pole*, která definuje prohledávatelná data v indexu. Kolekce polí zcela, určuje strukturu dokumentů, které můžete odeslat samostatně. Kolekce pole zahrnuje povinná i nepovinná pole s názvem a typem a s atributy indexu, které určují, jak je možné pole.

1. V okně **Přidat Index** klikněte na **Pole >**, aby se vysunulo okno definice pole. 

2. Přijměte vygenerované pole *key* (klíč) typu Edm.String. Ve výchozím nastavení se pole jmenuje *id*, ale můžete ho přejmenovat, pokud bude řetězec splňovat [pravidla pojmenování](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Pole key je povinné pro každý index Azure Search a musí jít o řetězec.

3. Přidejte pole, která budou plně určovat dokumenty, které nahrajete. Pokud dokumenty obsahují *id*, *hotel name* (název hotelu), *address* (adresa), *city* (město) a *region* (oblast), vytvořte v indexu pro každou položku odpovídající pole. Nápovědu k nastavování atributů najdete [níže v oddíle s pokyny k návrhu](#design).

4. Volitelně přidejte všechna pole, která se používají interně ve výrazech filtru. Atributy pole jdou nastavit tak, aby vyloučily pole z operací hledání.

5. Až to budete mít, klikněte na **OK**, aby se index uložil a vytvořil.

## <a name="tips-for-adding-fields"></a>Tipy k přidávání polí

Vytvoření indexu na portálu je na klávesnici náročné. Pomocí následujícího pracovního postupu minimalizujte počet kroků:

1. Nejdříve vytvořte seznam polí zadáním názvů a nastavením datových typů.

2. Potom použijte zaškrtávací políčka v horní části každého atributu a hromadně povolte nastavení pro všechna pole a potom selektivně zrušte zaškrtnutí políček u několika polí, která to nastavení nevyžadují. Například pole s řetězcem jsou obvykle prohledávatelná. Díky tomu můžete kliknout na **Retrievable** (Zobrazitelné) a **Searchable** (Prohledávatelné), aby se vracely hodnoty pole ve výsledcích hledání a bylo i možné fulltextové vyhledávání v poli. 

<a name="design"></a>

## <a name="design-guidance-for-setting-attributes"></a>Pokyny k nastavení atributů návrhu

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

Analyzátory a moduly pro návrhy jsou přidružené k polím před uložením indexu. Nezapomeňte kliknout na záložku každé stránky a přidat do definice indexu jazykové analyzátory nebo moduly pro návrhy.

Pole řetězců jsou často označená jako  **Searchable** a **Retrievable**.

Pole použitá k zúžení výsledků hledání obsahují **Sortable** (Jde řadit), **Filterable** (Filtrovatelné) a **Facetable** (Kategorizovatelné).

Atributy polí určují, jak se pole používá, například jestli se používá ve fulltextovém vyhledávání, fasetové navigaci, operacích řazení a tak dále. Následující tabulka popisuje každý atribut.

|Atribut|Popis|  
|---------------|-----------------|  
|**searchable**|Fulltextově prohledávatelné, lze provést lexikální analýzu, jako je dělení slov během indexování. Pokud nastavíte prohledávatelné pole na hodnotu jako „slunečný den“, interně se rozdělí na jednotlivé tokeny „slunečný“ a „den“. Podrobnosti najdete v článku [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|**filterable**|Odkazované v dotazech **$filter**. Ve filtrovatelných polích typu `Edm.String` nebo `Collection(Edm.String)` nejdou dělit slova, takže se dají porovnávat jenom na přesné shody. Pokud například nastavíte takové pole f na „sunny day“, `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` ano. |  
|**sortable**|Ve výchozím nastavení systém řadí výsledky podle skóre (bodů), můžete ale nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nejdou nastavit na **sortable**. |  
|**facetable**|Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet nalezených položek podle kategorie (například hotely v konkrétním městě). Tuto možnost nejde použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String`, která jsou **filterable**, **sortable** nebo **facetable**, můžou být dlouhé maximálně 32 kilobajtů. Podrobnosti najdete v článku [Vytvoření indexu (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Jedinečný identifikátor pro dokumenty v indexu. Jako pole key se musí zvolit právě jedno pole a musí být typu `Edm.String`.|  
|**retrievable**|Určuje, jestli může být pole vrácené ve výsledku hledání. To je užitečné, když chcete použít pole (například *zisková marže*) jako filtrovací, řadicí a bodovací mechanismus, ale nechcete, aby pole bylo viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole typu `key`.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Vytvoření indexu hotelů použitého v oddílech příkladů k rozhraní API

Dokumentace k rozhraní API služby Azure Search obsahuje příklady kódu s jednoduchým indexem *hotels* (hotely). Na následujících snímcích obrazovky vidíte definici indexu, včetně jazykového analyzátoru francouzštiny určeného během definice indexu, který si můžete cvičně znovu vytvořit na portálu.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Další postup

Po vytvoření indexu Azure Search můžete přejít na další krok: [nahrání prohledávatelných dat do indexu](search-what-is-data-import.md).

Další možnost je také podívat se blíže na indexy. Kromě kolekce Pole index také určuje analyzátory, moduly pro návrhy, profily vyhodnocování (bodovací profily) a nastavení CORS. Portál poskytuje stránky se záložkami pro definování nejběžnějších elementů: Pole, analyzátory a moduly pro návrhy. K vytvoření nebo úpravě jiných elementů můžete použít rozhraní REST API nebo .NET SDK.

## <a name="see-also"></a>Další informace najdete v tématech

 [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md)  
 [Rozhraní REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

