---
title: Filtry zabezpečení pro oříznutí výsledků
titleSuffix: Azure Cognitive Search
description: Řízení přístupu v obsahu Azure Kognitivní hledání pomocí filtrů zabezpečení a identit uživatelů.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794265"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtry zabezpečení pro oříznutí výsledků v Azure Kognitivní hledání

Můžete použít filtry zabezpečení pro zkrácení výsledků hledání v Azure Kognitivní hledání na základě identity uživatele. Toto vyhledávací prostředí obvykle vyžaduje porovnání identity, která se požádá o hledání v poli, které obsahuje zásady, které mají oprávnění k dokumentu. Při nalezení shody má uživatel nebo objekt zabezpečení (například skupina nebo role) přístup k tomuto dokumentu.

Jedním ze způsobů, jak dosáhnout filtrování zabezpečení, je složitá disjunkce výrazů rovnosti: například `Id eq 'id1' or Id eq 'id2'`a tak dále. Tento přístup je náchylný k chybám, obtížně se udržuje a v případech, kdy seznam obsahuje stovky nebo tisíce hodnot, zpomaluje dobu odezvy na dotaz o mnoho sekund. 

Jednodušší a rychlejší přístup je prostřednictvím funkce `search.in`. Použijete-li `search.in(Id, 'id1, id2, ...')` namísto výrazu rovnosti, můžete očekávat dobu odezvy za sekundu.

V tomto článku se dozvíte, jak provést filtrování zabezpečení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření pole obsahujícího hlavní identifikátory 
> * Nabízení nebo aktualizace stávajících dokumentů s příslušnými hlavními identifikátory
> * Vystavení žádosti o vyhledávání pomocí `search.in` `filter`

>[!NOTE]
> V tomto dokumentu se nezabývá proces načítání hlavních identifikátorů. Měli byste ho získat od poskytovatele služby identity.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [službu Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-create-service-portal)a [index Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Vytvořit pole zabezpečení

Dokumenty musí obsahovat pole určující, které skupiny mají přístup. Tyto informace se stávají kritérii filtru, proti kterým jsou vybrané nebo odmítnuté dokumenty ze sady výsledků vrácené vystavitelem.
Řekněme, že máme index zabezpečených souborů a každý soubor je přístupný pro jinou sadu uživatelů.
1. Přidat pole `group_ids` (zde můžete zvolit libovolný název) jako `Collection(Edm.String)`. Ujistěte se, že pole má atribut `filterable` nastaven na `true`, aby se výsledky hledání vyfiltroval podle přístupu uživatele. Pokud například nastavíte pole `group_ids` na `["group_id1, group_id2"]` dokumentu `file_name` "secured_file_b", budou mít přístup pro čtení k souboru pouze uživatelé, kteří patří do skupin ID "group_id1" nebo "group_id2".
   Ujistěte se, že je atribut `retrievable` pole nastavený na `false`, takže se nevrací jako součást požadavku hledání.
2. Do tohoto příkladu přidejte také `file_id` a `file_name` pole.  

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

Pokud potřebujete aktualizovat existující dokument se seznamem skupin, můžete použít akci `merge` nebo `mergeOrUpload`:

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

Aby bylo možné oříznout dokumenty na základě přístupu `group_ids`, měli byste vydávat vyhledávací dotaz s filtrem `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`, kde ' group_id1, group_id2,... '. jsou skupiny, do kterých patří Vydavatel žádosti o vyhledávání.
Tento filtr odpovídá všem dokumentům, pro které `group_ids` pole obsahuje jeden z daných identifikátorů.
Úplné informace o prohledávání dokumentů pomocí Azure Kognitivní hledání najdete v [dokumentu pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).
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

Dokumenty byste měli získat zpátky tam, kde `group_ids` obsahuje buď "group_id1" nebo "group_id2". Jinými slovy získáte dokumenty, na které má Vydavatel požadavků oprávnění ke čtení.

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

To je způsob, jak můžete filtrovat výsledky na základě identity uživatelů a funkce Azure Kognitivní hledání `search.in()`. Pomocí této funkce můžete předat základní identifikátory pro žádajícího uživatele, aby odpovídaly identifikátorům zabezpečení, které jsou přidruženy k jednotlivým cílovým dokumentům. Když je zpracován požadavek na hledání, funkce `search.in` vyfiltruje výsledky hledání, pro které žádný z objektů zabezpečení uživatele nemá oprávnění ke čtení. Hlavní identifikátory můžou představovat například skupiny zabezpečení, role nebo dokonce vlastní identitu uživatele.
 
## <a name="see-also"></a>Další informace najdete v tématech

+ [Řízení přístupu na základě identity ve službě Active Directory s využitím filtrů Azure Kognitivní hledání](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry v Azure Kognitivní hledání](search-filters.md)
+ [Zabezpečení a řízení přístupu k datům v Azure Kognitivní hledáních operacích](search-security-overview.md)