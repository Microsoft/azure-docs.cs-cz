---
title: Jak modelování komplexních datových typů – Azure Search
description: Vnořený nebo hierarchické datových struktur můžete modelovat v indexu Azure Search pomocí ploché sady řádků a datový typ kolekce.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311979"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Jak modelování komplexních datových typů ve službě Azure Search
Externích datových sad použitých k naplnění indexu Azure Search někdy obsahovat hierarchické, nebo jsou vnořené používání dílčích struktur, které nenaruší elegantně do tabulkového sady řádků. Příklady těchto struktur může zahrnovat více umístění a telefonní čísla pro jednoho zákazníka, více barvy a velikosti pro jeden SKU, více autoři jednoho adresáře a tak dále. V modelovacích podmínky, může se zobrazit tyto struktury říká *komplexních datových typů*, *složené datové typy*, *složené datové typy*, nebo *agregace datové typy*, pár.

Komplexních datových typů nejsou ve službě Azure Search nativně podporované, ale ověřené řešení zahrnuje dvoustupňový proces sloučení struktuře a následným použitím **kolekce** datový typ ke znovuvytvoření vnitřní struktury. Podle postupu popsaného v tomto článku umožňuje obsah pro hledání, Fasetové, filtrování a řazení.

## <a name="example-of-a-complex-data-structure"></a>Příklad komplexní datové struktury
Dotyčný data se obvykle nachází jako sadu dokumentů XML nebo JSON nebo jako položky v úložiště NoSQL, jako je Azure Cosmos DB. Před obrovskou výzvou – strukturálně, vyplývá z s více podřízených položek, které je potřeba vyhledávat a filtrovat.  Jako výchozí bod pro znázornění řešení proveďte následující dokument JSON, který obsahuje sadu kontakty jako příklad:

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

I když pole s názvem "id", "name" a "společnost" lze snadno mapovat 1: 1 jako pole v indexu Azure Search, pole "umístění" obsahuje celou řadu umístění s obě sady ID umístění, stejně jako popisy umístění. Vzhledem k tomu, že Azure Search nemá datový typ, který to podporuje, budeme potřebovat jiný způsob, jak tento model ve službě Azure Search. 

> [!NOTE]
> Tento postup je popsán také Kirka Evans v blogovém příspěvku [indexování služby Azure Cosmos DB pomocí služby Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), který ukazuje techniky označované jako "sloučení dat", kterým byste měli pole s názvem `locationsID` a `locationsDescription` které jsou obě [kolekce](https://msdn.microsoft.com/library/azure/dn798938.aspx) (nebo pole řetězců).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Část 1: Sloučit pole do jednotlivých polí
K vytvoření indexu Azure Search, který přizpůsobuje tuto datovou sadu, vytvořte jednotlivá pole pro vnořené podkladní: `locationsID` a `locationsDescription` s datovým typem [kolekce](https://msdn.microsoft.com/library/azure/dn798938.aspx) (nebo pole řetězců). V těchto polích by do indexu hodnoty '1' a '2' `locationsID` pole Jan Macek a hodnoty '3' & '4' do `locationsID` Jen Campbell pole.  

Vaše data v rámci Azure Search bude vypadat takto: 

![Ukázková data, 2 řádky](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Část 2: Přidat pole kolekce v definici indexu
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
Za předpokladu, že jste vytvořili indexu a načtení dat, teď můžete otestovat řešení ověření provádění dotazů vyhledávání datové sadě. Každý **kolekce** pole by mělo být **prohledávatelné**, **filterable** a **facetable**. Je třeba možné spouštět dotazy jako:

* Vyhledání všech uživatelů, kteří pracují v sídle"Adventureworks".
* Získáte počet lidí, kteří pracují v domácí kanceláře.  
* Lidí, kteří pracují v domácí kanceláře ukazují, jaký poboček, které budou fungovat spolu s počtem lidí v jednotlivých oblastech.  

Pokud tuto techniku spadá této doby změny nepublikujete je, když je třeba provést hledání, který kombinuje id umístění i popis umístění. Příklad:

* Najít všechny osoby, které mají domácí kanceláře a má ID umístění 4.  

Pokud jste si možná Vzpomínáte, původní obsah vypadal to:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Ale nyní oddělili jsme data do samostatných polích, vidíme, že žádný způsob, že pokud Office Domovská stránka pro Jen Campbell má vztah k `locationsID 3` nebo `locationsID 4`.  

Chcete-li zpracovávat tento případ, definujte další pole v indexu, pro který je kombinací všech dat do jedné kolekce.  V našem příkladu budeme nazývat toto pole `locationsCombined` a jsme rozdělí obsah s `||` však můžete zvolit libovolný oddělovač, který si myslíte, že by jedinečnou sadu znaků pro váš obsah. Příklad: 

![Ukázková data, 2 řádky s oddělovačem](./media/search-howto-complex-data-types/sample-data-2.png)

Použití této funkce `locationsCombined` pole, jsme teď zvládne ještě další dotazy, jako například:

* Zobrazit počet lidí, kteří pracují na "Domovské úřadu" s Id umístění "4".  
* Vyhledat lidem, kteří pracují v domácí kanceláře umístění Id '4'. 

## <a name="limitations"></a>Omezení
Tato technika je užitečná pro řadu scénářů, ale v každém případě neplatí.  Příklad:

1. Pokud nemáte statickou sadu polí v komplexního datového typu a neexistoval způsob, jak mapovat všechny možné typy jedno pole. 
2. Aktualizuje se objekty vyžaduje další úkony a zjistit, co přesně je aktualizovat v indexu Azure Search

## <a name="sample-code"></a>Ukázka kódu
Příklad najdete na tom, jak indexovat složité datové sady JSON do Azure Search a provádět dotazy nad tuto datovou sadu v této [úložiště GitHub se vzorovými](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Další krok
[Hlasujte pro nativní podporu komplexních datových typů](https://feedback.azure.com/forums/263029-azure-search) na Azure Search UserVoice stránce a poskytuje jim další vstupy, byste chtěli nám ke zvážení při výběru implementace funkce. Můžete také kontaktovat mě přímo na Twitteru pod @liamca.

