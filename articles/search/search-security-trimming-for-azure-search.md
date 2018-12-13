---
title: Filtry zabezpečení pro oříznutí výsledky – Azure Search
description: Řízení přístupu u obsahu Azure Search pomocí filtrů zabezpečení a identity uživatelů.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 84147b250ea17df9af67cc8a9025cdf6ec59a705
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314223"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpečení pro oříznutí výsledky ve službě Azure Search

Můžete použít filtry zabezpečení mají být odebrány výsledky hledání ve službě Azure Search na základě identity uživatele. Tento vyhledávací funkce obvykle vyžaduje porovnání identitu těmi požadavků hledání proti pole obsahující zásady, kteří mají oprávnění k dokumentu. Když je nalezena shoda, uživatel nebo instanční objekt (například skupinu nebo rolí) má přístup k dokumentu.

Jeden ze způsobů, jak zajistit zabezpečení filtrování je složité disjunkce rovnosti výrazů: například `Id eq 'id1' or Id eq 'id2'`a tak dále. Tento přístup je náchylné, obtížné spravovat a v případech, kde seznam obsahuje stovky nebo tisíce hodnoty, může zpomalit doba odezvy dotazů odpovídala tak počet sekund. 

Jednodušší a rychlejší přístup je prostřednictvím `search.in` funkce. Pokud používáte `search.in(Id, 'id1, id2, ...')` namísto výrazu rovnosti, můžete očekávat odpovědi sekunda časy.

Tento článek ukazuje, jak provádět filtrování zabezpečení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření pole, které obsahuje hlavní identifikátory 
> * Vložit nebo aktualizovat stávající dokumenty s příslušnými identifikátory instančního objektu
> * Vydat požadavek hledání s `search.in` `filter`

>[!NOTE]
> Proces načítání identifikátory objektu zabezpečení není zahrnuté v tomto dokumentu. Měli byste ho získat od vašeho zprostředkovatele identity služby.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [předplatného Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [služby Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), a [Azure Search Index](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Vytvoření pole zabezpečení

Dokumenty musí obsahovat pole určující, které skupiny mají přístup. Tyto informace se stane kritéria filtru, kterými dokumenty vybrané nebo zamítnuto sada výsledků vrátila a vydavatele.
Předpokládejme, že máme indexu zabezpečených souborů a každý soubor je přístupný pomocí jiné sady uživatelů.
1. Přidat pole `group_ids` (můžete zvolit libovolný název zde) jako `Collection(Edm.String)`. Ujistěte se, že pole má `filterable` atribut nastaven na `true` tak, aby výsledky hledání jsou filtrovány podle má uživatel přístup. Pokud nastavíte například `group_ids` pole `["group_id1, group_id2"]` pro dokument s `file_name` "secured_file_b", jenom uživatelé, kteří patří do skupiny group_id1"ID" nebo "group_id2" mít přístup k souboru pro čtení.
   Ujistěte se, že pole `retrievable` atribut je nastaven na `false` tak, aby se vrátil jako součást požadavku hledání.
2. Přidejte také `file_id` a `file_name` polí pro účely tohoto příkladu.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Vložení dat do indexu pomocí rozhraní REST API
  
Vydání požadavku HTTP POST do koncového bodu adresy URL vašeho indexu. Text požadavku HTTP je objekt JSON obsahující dokumenty, které se přidají:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

V textu požadavku zadejte obsah dokumentů:

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

Pokud je potřeba aktualizovat existující dokument s seznam skupin, můžete použít `merge` nebo `mergeOrUpload` akce:

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

Úplné podrobnosti o přidání nebo aktualizace dokumentů, můžete si přečíst [upravovat dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Použít filtr zabezpečení

Pokud chcete oříznout dokumentů na základě `group_ids` přístup, by měly vydávat vyhledávací dotaz s `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtru, kde "group_id1 group_id2..." jsou skupiny, do kterých patří vystavitele žádosti o hledání.
Tento filtr odpovídá všechny dokumenty, u kterého `group_ids` pole obsahuje jeden z dané identifikátory.
Úplné podrobnosti o vyhledávání dokumentů pomocí Azure Search, můžete si přečíst [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Všimněte si, že tento příklad ukazuje, jak hledat dokumentů pomocí požadavku POST.

Vydání požadavku HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Zadejte filtr v textu požadavku:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Měli byste získat dokumenty zpět tam, kde `group_ids` obsahuje "group_id1" nebo "group_id2". Jinými slovy získáte dokumentů, ke kterým má vystavitele žádosti o přístup pro čtení.

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

To je, jak můžete filtrovat výsledky na základě identity uživatele a Azure Search `search.in()` funkce. Tato funkce slouží a zajistěte tak předání objektu zabezpečení identifikátory pro žádajícího uživatele k porovnání s hlavní identifikátory, které jsou spojené s každou cílového dokumentu. Když je zpracována žádost o vyhledávání, `search.in` funkce vyfiltruje výsledky hledání, pro které žádný z objektů zabezpečení uživatele nemá oprávnění ke čtení. Hlavní identifikátory může představovat věci, jako jsou skupiny zabezpečení, role nebo dokonce i vlastní identitu uživatele.
 
## <a name="see-also"></a>Další informace najdete v tématech

+ [Aktivní řízení přístupu na základě identity adresáře pomocí filtrů Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry ve službě Azure Search](search-filters.md)
+ [Řízení přístupu a zabezpečení dat v provozu Azure Search](search-security-overview.md)