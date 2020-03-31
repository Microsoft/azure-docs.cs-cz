---
title: Bezpečnostní filtry pro oříznutí výsledků
titleSuffix: Azure Cognitive Search
description: Řízení přístupu k obsahu Azure Cognitive Search pomocí filtrů zabezpečení a identit uživatelů.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794265"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtry zabezpečení pro oříznutí výsledků v Azure Cognitive Search

Filtry zabezpečení můžete použít k oříznutí výsledků hledání ve službě Azure Cognitive Search na základě identity uživatele. Toto prostředí vyhledávání obecně vyžaduje porovnání identity toho, kdo požaduje vyhledávání, s polem obsahujícím zásady, které mají oprávnění k dokumentu. Při nalezení shody má uživatel nebo objekt zabezpečení (například skupina nebo role) přístup k tomuto dokumentu.

Jedním ze způsobů, jak dosáhnout filtrování zabezpečení, je komplikované zrušení výrazů rovnosti: například `Id eq 'id1' or Id eq 'id2'`, a tak dále. Tento přístup je náchylný k chybám, obtížně udržovatelné a v případech, kdy seznam obsahuje stovky nebo tisíce hodnot, zpomaluje dobu odezvy dotazu o mnoho sekund. 

Jednodušší a rychlejší přístup `search.in` je prostřednictvím funkce. Pokud použijete `search.in(Id, 'id1, id2, ...')` místo výrazu rovnosti, můžete očekávat, že doba odezvy sub-second.

Tento článek ukazuje, jak provést filtrování zabezpečení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření pole obsahujícího hlavní identifikátory 
> * Nabízení nebo aktualizace existujících dokumentů s příslušnými hlavními identifikátory
> * Vydat žádost o `search.in` hledání`filter`

>[!NOTE]
> Proces načítání hlavních identifikátorů není v tomto dokumentu popsán. Měli byste si ji od svého poskytovatele služby identity.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [službu Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-create-service-portal)a index [kognitivního vyhledávání Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Vytvořit pole zabezpečení

Dokumenty musí obsahovat pole určující, ke kterým skupinám má přístup. Tyto informace se stanou kritérii filtru, proti kterým jsou dokumenty vybrány nebo odmítnuty ze sady výsledků vrácené vystavitte.
Předpokládejme, že máme index zabezpečených souborů a každý soubor je přístupný jinou sadou uživatelů.
1. Přidat `group_ids` pole (zde můžete zvolit libovolný `Collection(Edm.String)`název) jako . Ujistěte se, `filterable` že pole `true` má atribut nastavený tak, aby byly výsledky hledání filtrovány na základě přístupu, který má uživatel. Pokud například nastavíte `group_ids` `["group_id1, group_id2"]` pole na `file_name` dokument s "secured_file_b", přístup ke souboru mají pouze uživatelé, kteří patří do ID skupiny "group_id1" nebo "group_id2".
   Ujistěte se, `retrievable` že atribut `false` pole je nastaven tak, aby nebyl vrácen jako součást požadavku hledání.
2. Také `file_id` přidat `file_name` a pole v zájmu tohoto příkladu.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Odesílání dat do indexu pomocí rozhraní REST API
  
Vydejte požadavek HTTP POST do koncového bodu adresy URL indexu. Tělo požadavku HTTP je objekt JSON obsahující dokumenty, které mají být přidány:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

V textu žádosti zadejte obsah dokumentů:

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

Pokud potřebujete aktualizovat existující dokument se seznamem skupin, `merge` můžete `mergeOrUpload` použít akci nebo:

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

Podrobné informace o přidávání nebo aktualizaci dokumentů naleznete v části [Úpravy dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Použití filtru zabezpečení

Chcete-li oříznout `group_ids` dokumenty na základě přístupu, `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` měli byste vydat vyhledávací dotaz s filtrem, kde "group_id1, group_id2,...' jsou skupiny, do kterých patří vystavitel požadavku na vyhledávání.
Tento filtr odpovídá všem `group_ids` dokladům, pro které pole obsahuje jeden z daných identifikátorů.
Podrobné informace o hledání dokumentů pomocí Azure Cognitive Search najdete v dokumentu [hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Všimněte si, že tato ukázka ukazuje, jak vyhledávat dokumenty pomocí požadavku POST.

Vydejte požadavek HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Zadejte filtr v těle požadavku:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Dokumenty byste měli získat `group_ids` zpět tam, kde obsahuje buď "group_id1" nebo "group_id2". Jinými slovy získáte dokumenty, ke kterým má vydavatel požadavku přístup pro čtení.

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

Tímto způsobem můžete filtrovat výsledky na základě `search.in()` identity uživatele a funkce Azure Cognitive Search. Tuto funkci můžete použít k předání identifikátorů v zásadě pro žádající ho uživatele, aby odpovídaly hlavní identifikátory spojené s každým cílovým dokumentem. Při zpracování požadavku na `search.in` hledání funkce filtruje výsledky hledání, pro které žádný z objektů uživatele přístup pro čtení. Hlavní identifikátory mohou představovat věci, jako jsou skupiny zabezpečení, role nebo dokonce vlastní identitu uživatele.
 
## <a name="see-also"></a>Viz také

+ [Řízení přístupu založené na identitách služby Active Directory pomocí filtrů Azure Cognitive Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry v Azure Cognitive Search](search-filters.md)
+ [Zabezpečení dat a řízení přístupu v operacích Azure Cognitive Search](search-security-overview.md)