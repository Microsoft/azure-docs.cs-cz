---
title: Postup modelu komplexní datové typy ve službě Azure Search | Microsoft Docs
description: Vnořené nebo hierarchické datové struktury můžete modelován v indexu Azure Search pomocí plochou řádků a typu dat kolekce.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 81298bedd43a89ea948753dffc5f80248f5429ca
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799069"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Postup modelu komplexní datové typy ve službě Azure Search
Externích datových sad, které používá k naplnění indexu Azure Search někdy obsahovat hierarchické nebo vnořená používání dílčích struktur, které nejsou v tabulkovém řádků přehledně rozdělení. Příkladem takových struktury může zahrnovat více umístění a telefonních čísel pro jednoho zákazníka, více barvy a velikosti pro jednu SKU, více autorů jeden knihy a tak dále. V modelování podmínky, můžete se setkat těchto struktur, označuje jako *komplexními datovými typy*, *složené datové typy*, *složené datové typy*, nebo *agregace datové typy*, a další.

Komplexní datové typy nejsou podporovány nativně ve službě Azure Search, ale Principy řešení zahrnuje dvoustupňový proces sloučení strukturu a potom pomocí **kolekce** datový typ pro-rekonstruovat pro vnitřní strukturu. Podle postupu popsaného v tomto článku umožňuje obsah má proběhnout, Fasetové, filtrovat a seřadit.

## <a name="example-of-a-complex-data-structure"></a>Příklad rozšířené datové struktury
Dotyčné údaje se obvykle nachází jako sada dokumentů XML nebo JSON, nebo jako položky v úložišti NoSQL například Azure Cosmos DB. Strukturálně výzvy byl vyvolán s více podřízené položky, které je třeba vyhledávat a filtrovat.  Jako výchozí bod pro znázornění alternativní řešení proveďte následující dokument JSON, který obsahuje sadu kontakty jako příklad:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Při pole s názvem "id", "název" a "společnost" lze snadno mapovat 1: 1 jako pole v indexu Azure Search, pole, umístění, obsahuje pole umístění, i skupinu ID umístění, jakož i popisy umístění. Vzhledem k tomu, že Azure Search nemá datový typ, který to podporuje, potřebujeme jiný způsob, jak to modelu ve službě Azure Search. 

> [!NOTE]
> Tento postup je popsán také zařízení Kirk Evans v příspěvku blogu [indexování DB Cosmos Azure s Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), zobrazující techniku nazývaný "sloučení dat", kterým byste měli pole s názvem `locationsID` a `locationsDescription` které jsou obě [kolekce](https://msdn.microsoft.com/library/azure/dn798938.aspx) (nebo pole řetězců).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Část 1: Narovnání pole na jednotlivých polí
K vytvoření indexu Azure Search, která bude vyhovovat tuto datovou sadu, vytvoření jednotlivých polí pro vnořené podkladní: `locationsID` a `locationsDescription` s typem dat [kolekce](https://msdn.microsoft.com/library/azure/dn798938.aspx) (nebo pole řetězců). V těchto polích by do indexu hodnoty '1' a (2) `locationsID` do pole pro John Smith a hodnoty "3" a "4" `locationsID` pole pro Jen Campbell.  

Vaše data v rámci Azure Search bude vypadat takto: 

![Ukázková data, 2 řádků](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Část 2: Přidání kolekci polí v definici indexu
Ve schématu indexu může vypadat podobně jako tento příklad definice pole.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Ověření chování vyhledávání a případně rozšířit index
Za předpokladu, že jste vytvořili index a načíst data, můžete prověřit řešení ověření při provádění dotazu hledání podle datové sady. Každý **kolekce** pole by mělo být **prohledávatelné**, **filtrovatelných** a **facetable**. Nyní byste měli mít ke spouštění dotazů jako:

* Najít všichni uživatelé, kteří pracují v ústředí"Adventureworks".
* Získáte počet lidí, kteří pracují v Home Office.  
* Osob, které fungují na Home Office zobrazit jaké ostatních kancelářích fungují společně s počet uživatelů v každé lokalitě.  

Kde tato technika spadá rozdělovat je, když je potřeba udělat hledání, které kombinuje id umístění jak popis umístění. Příklad:

* Najít všechny osoby, které mají Home Office a mají ID umístění na 4.  

Pokud si Vzpomínáte, původní obsah hledá takto:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Ale nyní, když budeme mít oddělené data do samostatných polí, jsme nijak starostí, protože pokud Home Office pro Jen Campbell má vztah k `locationsID 3` nebo `locationsID 4`.  

Chcete-li zpracovávat tento případ, definujte jiné pole v indexu, které se kombinuje všechna data do jedné kolekce.  Pro náš příklad jsme bude volat v tomto poli `locationsCombined` a jsme dojde k oddělení obsah s `||` však můžete zvolit jakékoli oddělovač, který se domníváte, že by jedinečnou sadu znaků pro obsah. Příklad: 

![Ukázková data, 2 řádky s oddělovačem](./media/search-howto-complex-data-types/sample-data-2.png)

Pomocí této `locationsCombined` pole, jsme teď zvládne i další dotazy, například:

* Zobrazí počet uživatelů, kteří pracovat na 'domovské úřadu, s umístěním Id "4".  
* Vyhledejte lidem, kteří pracují v Home Office s umístěním Id "4". 

## <a name="limitations"></a>Omezení
Tato metoda je užitečná pro různé scénáře, ale není možné v každém případě ho.  Příklad:

1. Pokud nemáte sadu statických polí v komplexního datového typu a se žádný způsob, jak všechny možné typy mapování na jedno pole. 
2. Aktualizace vnořených objektů vyžaduje další práci k určení toho, co přesně je aktualizovat v indexu Azure Search

## <a name="sample-code"></a>Ukázka kódu
Můžete zobrazit příklad o tom, jak index komplexní JSON datové sady Azure Search a provádět spoustu dotazy přes tuto datovou sadu v této [úložiště GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Další krok
[Hlas pro nativní podpora pro komplexní datové typy](https://feedback.azure.com/forums/263029-azure-search) na UserVoice hledání Azure stránky a zadání žádné další, které chcete nám ke zvážení při výběru implementace funkce. Vám může také oslovení mi přímo na Twitteru v @liamca.

