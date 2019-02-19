---
title: Vytvoření indexu Azure Search na webu Azure portal – Azure Search
description: Informace o vytvoření indexu pro službu Azure Search pomocí návrhářů integrované portálu indexu.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338831"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Vytvoření indexu Azure Search na portálu

Služba Azure Search obsahuje index na integrované návrháře na portálu, které jsou užitečné pro prototypů nebo vytváření [indexu vyhledávání](search-what-is-an-index.md) hostované ve službě Azure Search. Nástroj se používá pro konstrukci schématu. Při ukládání definice indexu prázdný stane plně vyjádřené ve službě Azure Search. Jak ho načíst data pomocí prohledávatelných dat je na vás.

Návrháře indexů je pouze jedním z přístupů pro vytvoření indexu. Prostřednictvím kódu programu, můžete vytvořit index pomocí [.NET](search-create-index-dotnet.md) nebo [REST](search-create-index-rest-api.md) rozhraní API.

## <a name="start-index-designer"></a>Spusťte návrháře indexů

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) a otevřete řídicí panel služby. Kliknutím na **Všechny služby** na panelu odkazů můžete vyhledávat stávající „vyhledávací služby“ v rámci aktuálního předplatného. 

2. Klikněte na tlačítko **přidat index** odkaz na panelu příkazů v horní části stránky.

   ![Přidat index propojení na panelu příkazů](media/search-create-index-portal/add-index.png "index odkaz přidat na panelu příkazů")

3. Pojmenujte si index Azure Search. Index názvy jsou odkazovány v operace indexování a dotazu. Název indexu se stane součástí adresy URL koncového bodu u připojení k indexu a pro posílání žádostí HTTP v rozhraní REST API služby Azure Search.

   * Začněte písmenem.
   * Název může obsahovat jenom malá písmena, číslice nebo pomlčky (-).
   * Délka je omezená na 60 znaků.

## <a name="add-fields"></a>Přidat pole

Kompozice indexu obsahuje *kolekci Pole*, která definuje prohledávatelná data v indexu. Kolekce polí zcela, určuje strukturu dokumentů, které můžete odeslat samostatně. Kolekce pole zahrnuje povinná i nepovinná pole s názvem a typem a s atributy indexu, které určují, jak je možné pole.

1. Přidání polí pro plně určovat dokumenty, odešlete, nastavení [datový typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pro každé z nich. Například, pokud dokumenty obsahují *hotelu id*, *Název hotelu*, *adresu*, *Město*, a *oblasti*, vytvořte odpovídající pole pro každý z nich v indexu. Zkontrolujte [níže v části s pokyny k návrhu](#design) nápovědu k nastavování atributů.

2. Zadejte *klíč* pole typu Edm.String. Hodnoty pro toto pole musí jednoznačně identifikovat každou dokumentu. Ve výchozím nastavení se pole jmenuje *id*, ale můžete ho přejmenovat, pokud bude řetězec splňovat [pravidla pojmenování](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Například, pokud vaše kolekce pole zahrnuje *hotelu id*, vybrali byste, který pro svůj klíč. Pole key je povinné pro každý index Azure Search a musí jít o řetězec.

3. Nastavení atributů u každého pole. Návrháře indexů vyloučí všechny atributy, které jsou neplatné pro datový typ, ale nebude navrhnout co chcete zahrnout. Přečtěte si pokyny v další části, abyste pochopili, jaké atributy jsou.

    Dokumentace k rozhraní API služby Azure Search obsahuje příklady kódu s jednoduchým indexem *hotels* (hotely). Následujícím snímku obrazovky vidíte definici indexu, včetně jazykového analyzátoru francouzštiny určeného během definice indexu, které můžete znovu vytvořit jako praktickém cvičení na portálu.

    ![Ukázka indexu Hotels](media/search-create-index-portal/field-definitions.png "indexu Hotels demo")

4. Až budete hotovi, klikněte na tlačítko **vytvořit** uložte a vytvořte index.

<a name="design"></a>

## <a name="set-attributes"></a>Nastavení atributů

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

Analyzátory a moduly pro návrhy jsou přidružené k polím před uložením indexu. Nezapomeňte přidat jazykové analyzátory nebo moduly pro návrhy do definice indexu při jeho vytváření.

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

## <a name="next-steps"></a>Další postup

Po vytvoření indexu Azure Search můžete přejít na další krok: [nahrání prohledávatelných dat do indexu](search-what-is-data-import.md).

Alternativně můžete také použít [najdete podrobnější přehled indexy](search-what-is-an-index.md). Kromě kolekce Pole index také určuje analyzátory, moduly pro návrhy, profily vyhodnocování (bodovací profily) a nastavení CORS. Portál poskytuje stránky se záložkami pro definování nejběžnějších elementů: Pole, analyzátory a moduly pro návrhy. K vytvoření nebo úpravě jiných elementů můžete použít rozhraní REST API nebo .NET SDK.

## <a name="see-also"></a>Další informace najdete v tématech

 [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md)  
 [Rozhraní REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

