---
title: Filtry zabezpečení pro výsledky oříznutí ve službě Azure Search | Microsoft Docs
description: Řízení přístupu na obsahu Azure Search pomocí filtrů zabezpečení a identity uživatele.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: dd26676b74431566b3631b8a79cd06bcf3022518
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792796"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpečení pro výsledky oříznutí ve službě Azure Search

Můžete použít filtry zabezpečení oříznout výsledky hledání ve službě Azure Search na základě identity uživatele. Toto prostředí vyhledávání obvykle vyžaduje porovnávání identitu kdo požadavky hledání proti pole obsahující se zásadami, kteří mají oprávnění k dokumentu. Pokud je nalezena shoda, uživatele nebo objekt zabezpečení (například skupiny nebo role) má přístup k dokumentu.

Jeden ze způsobů, jak dosáhnout zabezpečení filtrování je prostřednictvím složitá disjunkce výrazů rovnosti: například `Id eq 'id1' or Id eq 'id2'`, a tak dále. Tento přístup je k chybám, obtížné spravovat a v případech, kde seznam obsahuje stovkami nebo tisíci hodnoty, zpomaluje doba odezvy dotaz podle počet sekund. 

Je jednodušší a rychlejší přístup prostřednictvím `search.in` funkce. Pokud používáte `search.in(Id, 'id1, id2, ...')` místo rovnosti výrazu, můžete očekávat dílčí sekundu odpovědi časy.

Tento článek ukazuje, jak provést filtrování zabezpečení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření pole, které obsahuje hlavní identifikátory 
> * Push nebo aktualizovat stávající dokumenty s relevantní identifikátory hlavního
> * Vydat žádost o vyhledávání s `search.in` `filter`

>[!NOTE]
> Proces načítání identifikátory hlavního není zahrnuté v tomto dokumentu. Měli byste ho získat od vašeho poskytovatele služeb identity.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [předplatného Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [služby Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), a [indexu Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Umožňuje vytvořit zabezpečení pole

Dokumenty musí obsahovat pole určující, které skupiny mají přístup. Tyto informace k kritéria filtru, kterými jsou dokumenty vybrané nebo odmítnuté ze sady výsledků vrátí vystavitele.
Předpokládejme, že máme index zabezpečené souborů a každý soubor je přístupný pro jinou sadu uživatelů.
1. Přidat pole `group_ids` (můžete zvolit libovolný název zde) jako `Collection(Edm.String)`. Ověřte, zda má pole `filterable` atribut nastaven na `true` tak, aby výsledky hledání jsou filtrovány podle má uživatel přístup. Například pokud nastavíte `group_ids` do `["group_id1, group_id2"]` pro dokument s `file_name` "secured_file_b", pouze uživatelé, kteří patří do skupiny ID "group_id1" nebo "group_id2" mít přístup k souboru pro čtení.
   Zkontrolujte, že pole `retrievable` je atribut nastaven na `false` tak, aby se nevrátí jako součást požadavku vyhledávání.
2. Také přidat `file_id` a `file_name` pole z důvodu v tomto příkladu.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Vkládání dat do indexu pomocí REST API
  
Vydání požadavku HTTP POST do svého indexu adresu URL koncového bodu. Text žádosti HTTP je objekt JSON obsahující dokumenty, které chcete přidat:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

V těle žádosti zadejte obsah dokumentů:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Pokud je potřeba aktualizovat stávající dokument s seznam skupin, můžete použít `merge` nebo `mergeOrUpload` akce:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Úplné podrobnosti o přidání nebo aktualizace dokumenty, můžete si přečíst [úpravám dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Použijte filtr zabezpečení

Chcete-li trim dokumentů na základě `group_ids` přístup, musí vydejte vyhledávací dotaz s `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtru, kde "group_id1, group_id2,..." jsou skupiny, do kterých patří vystavitele žádost o vyhledávání.
Tento filtr odpovídá všechny dokumenty, pro kterou `group_ids` pole obsahuje jeden z daného identifikátory.
Úplné podrobnosti o vyhledávání dokumentů pomocí Azure Search, můžete si přečíst [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Všimněte si, že tento příklad ukazuje postup vyhledávání dokumentů pomocí požadavek POST.

Vydání požadavku HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Zadejte filtr v těle žádosti:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Měli byste obdržet dokumenty zpět, kde `group_ids` obsahuje "group_id1" nebo "group_id2". Jinými slovy zobrazí se v dokumentech, ke kterým má vystavitele žádost o přístup pro čtení.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Závěr

Toto je, jak můžete filtrovat výsledky podle identity uživatele a Azure Search `search.in()` funkce. Tuto funkci můžete předat hlavní identifikátory pro žádajícího uživatele tak, aby odpovídala hlavní identifikátory spojené s každou cílovém dokumentu. Pokud je žádost o vyhledávání, `search.in` funkce filtruje výsledky hledání, pro které žádný z objekty uživatele nemá přístup pro čtení. Identifikátory hlavního může představovat věcmi, jako jsou skupiny zabezpečení, role nebo i vlastní identitu uživatele.
 
## <a name="see-also"></a>Další informace najdete v tématech

+ [Řízení přístupu na základě identity Active Directory, které jsou pomocí filtrů Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry ve službě Azure Search](search-filters.md)
+ [Řízení přístupu a zabezpečení a data v operacích Azure Search](search-security-overview.md)