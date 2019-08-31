---
title: Filtry zabezpečení pro oříznutí výsledků – Azure Search
description: Řízení přístupu k obsahu Azure Search pomocí filtrů zabezpečení a identit uživatelů.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186492"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpečení pro oříznutí výsledků v Azure Search

Filtry zabezpečení můžete použít k vystřihování výsledků hledání v Azure Search na základě identity uživatele. Toto vyhledávací prostředí obvykle vyžaduje porovnání identity, která se požádá o hledání v poli, které obsahuje zásady, které mají oprávnění k dokumentu. Při nalezení shody má uživatel nebo objekt zabezpečení (například skupina nebo role) přístup k tomuto dokumentu.

Jedním ze způsobů, jak dosáhnout filtrování zabezpečení, je složitá disjunkce výrazů rovnosti: `Id eq 'id1' or Id eq 'id2'`například, a tak dále. Tento přístup je náchylný k chybám, obtížně se udržuje a v případech, kdy seznam obsahuje stovky nebo tisíce hodnot, zpomaluje dobu odezvy na dotaz o mnoho sekund. 

Jednodušší a rychlejší přístup je prostřednictvím `search.in` funkce. Pokud použijete `search.in(Id, 'id1, id2, ...')` místo výrazu rovnosti, můžete očekávat dobu odezvy za sekundu.

V tomto článku se dozvíte, jak provést filtrování zabezpečení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření pole obsahujícího hlavní identifikátory 
> * Nabízení nebo aktualizace stávajících dokumentů s příslušnými hlavními identifikátory
> * Vystavení žádosti o `search.in` vyhledávání pomocí`filter`

>[!NOTE]
> V tomto dokumentu se nezabývá proces načítání hlavních identifikátorů. Měli byste ho získat od poskytovatele služby identity.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [službu Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal)a [index Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Vytvořit pole zabezpečení

Dokumenty musí obsahovat pole určující, které skupiny mají přístup. Tyto informace se stávají kritérii filtru, proti kterým jsou vybrané nebo odmítnuté dokumenty ze sady výsledků vrácené vystavitelem.
Řekněme, že máme index zabezpečených souborů a každý soubor je přístupný pro jinou sadu uživatelů.
1. Přidat pole `group_ids` (zde můžete zvolit libovolný název) `Collection(Edm.String)`jako. Ujistěte se, že pole má `filterable` atribut `true` nastaven tak, aby byly výsledky hledání filtrovány podle přístupu uživatele. Pokud například nastavíte `group_ids` pole na `["group_id1, group_id2"]` pro dokument s `file_name` názvem "secured_file_b", budou mít k souboru přístup pro čtení pouze uživatelé, kteří patří do skupin ID "group_id1" nebo "group_id2".
   Ujistěte se, že je `retrievable` atribut pole `false` nastavený tak, aby se nevrátil jako součást požadavku hledání.
2. Přidejte `file_id` také pole `file_name` a pro účely tohoto příkladu.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Vložení dat do indexu pomocí REST API
  
Vydejte požadavek HTTP POST na koncový bod adresy URL vašeho indexu. Tělo požadavku HTTP je objekt JSON, který obsahuje dokumenty, které se mají přidat:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

V textu žádosti zadejte obsah vašich dokumentů:

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

Pokud potřebujete aktualizovat existující dokument se seznamem skupin, můžete použít `merge` akci nebo: `mergeOrUpload`

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

Pokud chcete zobrazit úplné podrobnosti o přidávání nebo aktualizaci dokumentů, můžete si přečíst [dokument upravit](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Použít filtr zabezpečení

Aby bylo možné oříznout dokumenty na základě `group_ids` přístupu, měli byste vydávat vyhledávací dotaz `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` s filtrem, kde ' group_id1, group_id2,... ' jsou skupiny, do kterých patří Vydavatel žádosti o vyhledávání.
Tento filtr odpovídá všem dokumentům, pro `group_ids` které pole obsahuje jeden z daných identifikátorů.
Úplné podrobnosti o prohledávání dokumentů pomocí Azure Search můžete přečíst v [dokumentu hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Všimněte si, že v této ukázce se dozvíte, jak vyhledávat dokumenty pomocí žádosti POST.

Vydejte požadavek HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Zadejte filtr v textu žádosti:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Dokumenty byste měli získat zpátky tam, `group_ids` kde obsahuje buď "group_id1" nebo "group_id2". Jinými slovy získáte dokumenty, na které má Vydavatel požadavků oprávnění ke čtení.

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

To je způsob, jak můžete filtrovat výsledky na základě identity uživatelů a `search.in()` Azure Search funkce. Pomocí této funkce můžete předat základní identifikátory pro žádajícího uživatele, aby odpovídaly identifikátorům zabezpečení, které jsou přidruženy k jednotlivým cílovým dokumentům. Když je zpracován požadavek hledání, `search.in` funkce vyfiltruje výsledky hledání, pro které žádný z objektů zabezpečení uživatele nemá oprávnění ke čtení. Hlavní identifikátory můžou představovat například skupiny zabezpečení, role nebo dokonce vlastní identitu uživatele.
 
## <a name="see-also"></a>Viz také:

+ [Řízení přístupu na základě identity ve službě Active Directory pomocí filtrů Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry v Azure Search](search-filters.md)
+ [Zabezpečení a řízení přístupu k datům v Azure Searchch operacích](search-security-overview.md)