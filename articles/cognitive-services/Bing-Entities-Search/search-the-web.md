---
title: Co je vyhledávání Entity Bing? | Dokumenty Microsoft
description: Naučte se používat rozhraní API služby Bing Entity Search vyhledávání na webu pro entity a místech.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: f1b87c07d5b56307fd6b3fc68999598aeab6eb82
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343536"
---
# <a name="what-is-bing-entity-search"></a>Co je vyhledávání Entity Bing?

Rozhraní API služby Bing Entity Search odešle do služby Bing vyhledávací dotaz a získá výsledky, které zahrnují entity a místech. Místní výsledky obsahovat restaurace, hotelů nebo jiné místní podniky. Bing vrátí míst, pokud dotaz Určuje název místní firmy nebo požádá o typu podniku (například restaurace okolí). Bing vrací entity, pokud dotaz určuje známých osob, míst (ubytovací atrakce, stavy, zemích atd.) nebo věcí.

## <a name="suggesting--using-search-terms"></a>Návrhy & pomocí hledaná slova

Pokud zadáte vyhledávací pole, kde uživatel zadá své hledaný termín, použijte [rozhraní API pro automatické návrhy v Bingu](../bing-autosuggest/get-suggested-search-terms.md) lepší. Rozhraní API vrátí navrhované dotaz řetězce založený na částečné vyhledávání podmínky jako typy uživatelů.

Poté, co uživatel zadá své hledaný termín, adresa URL kódování termín před nastavením [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) parametr dotazu. Například, pokud uživatel zadá *Marcus Appel*, nastavte `q` k *Marcus + Appel* nebo *Marcus % 20Appel*.

Pokud hledaný termín obsahuje chybu pravopis, hledání odpověď obsahuje [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objektu. Objekt zobrazuje původní pravopis a opravené pravopis, který Bing používá pro vyhledávání.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Požaduje entity

Požadavek příklad najdete v části [provedení první požadavek](./quick-start.md).

## <a name="the-response"></a>Odpověď

Obsahuje odpovědi [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objektu. Pokud Bing vyhledá entitu nebo místě, které se týkají, objekt zahrnuje `entities` pole, `places` pole, nebo obojí. Objekt odpovědi, jinak hodnota nezahrnuje buď pole.

`entities` Pole je [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) objekt, který obsahuje seznam [Entity](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objekty (najdete v článku `value` pole). Seznam může obsahovat jednu entitu dominantní, více entit rozlišení více tras nebo obojí. 

Dominantní entity je entita, která Bing dochází k závěru, je pouze entity, který splňuje požadavek (nedochází k nejednoznačnosti, která splňuje entity žádosti). Pokud se několik entit by vyhovovaly žádosti o, seznam obsahuje více než jedna entita rozlišení více tras. Například pokud požadavek používá obecný název franšíza film, seznamu pravděpodobně obsahuje entity rozlišení více tras. Ale pokud žádost o konkrétní softwarový produkt z franšíza, pravděpodobně seznam obsahuje dominantní jedné entity.

Entity obsahují známé osobnosti zpívají, aktéři, athletes, modelů, např.; místech a zajímavá například Rainier připojení nebo Lincoln Památný; a další věci, jako je například název banánů goldendoodle, adresáře nebo film. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole obsahuje pomocné parametry, které identifikují typu entity. Například pokud je osoba, videa, zvíře nebo přitažlivosti. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Následující obrázek znázorňuje odpovědi, která zahrnuje entity dominantní a rozlišení více tras.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Zahrnuje entity `name`, `description`, a `image` pole. Při zobrazení těchto polí v činnost koncového uživatele, musíte je atribut. `contractualRules` Obsahuje přehled držitelů, které musíte aplikovat. Smluvními pravidlo identifikuje pole, které se vztahují na přidělení. Informace o použití uvedení najdete v tématu [uvedení](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Při zobrazení informací o entitě (název, popis a bitové kopie), musíte také použít adresu URL v `webSearchUrl` pole, které chcete propojit hledání Bing výsledků stránky, která obsahuje entity.


`places` Pole [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) objekt, který obsahuje seznam [místní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objekty (najdete v článku `value` pole). Seznam obsahuje jeden nebo více místní entit, které odpovídají požadavku.

Místech zahrnovat restaurace, hotels nebo místní podniky. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole obsahuje pomocné parametry, které identifikují typ místní entity. Seznam obsahuje seznam odkazů na jako místní, LocalBusiness, restaurace. Každý následných pomocný parametr v poli zúží typu entity. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```

Místní entity podporou dotazy, jako *restaurace okolní* vyžadují umístění uživatele zajistit přesné výsledky. Své žádosti měli vždycky používat X umístění vyhledávání a hlavičky X-MSEdge-když Pokud chcete zadat umístění uživatele. Pokud Bing se domnívá, dotaz by těžit z umístění uživatele, nastaví `askUserForLocation` pole z [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) k **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Výsledek místní zahrnuje název na místě, adresu, telefonní číslo a adresu URL webu entity. Při zobrazení informací o entitě, musíte také použít adresu URL v `webSearchUrl` pole, které chcete propojit hledání Bing výsledků stránky, která obsahuje entity.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Můžete nebo třetích stran vaším jménem nemusí používat, zachovat, ukládat, mezipaměti, sdílet, nebo distribuovat všechna data z rozhraní API entity pro účely testování, vývoj, školení, distribuci nebo zpřístupnění jakoukoli službu, jiných společností než Microsoft nebo funkci.  

## <a name="data-attribution"></a>Uvedení dat

Rozhraní API pro Bing Entity odpovědí obsahují informace ve vlastnictví třetích stran. Odpovídáte Ujistěte se, že používání je vhodné, například při dodržení licence creative commons, které může vaše uživatelské prostředí závisí na.

Pokud odpověď nebo výsledek obsahuje `contractualRules`, `attributions`, nebo `provider` polí, musí atributů data. Pokud odpověď nesmí obsahovat žádný z těchto polí, není potřeba žádná porušení. Pokud odpověď obsahuje `contractualRules` pole a `attributions` nebo `provider` pole, musíte použít smluvními pravidla do atribut data.

Následující příklad ukazuje, entita, která obsahuje pravidlo smluvními MediaAttribution a obsahující bitovou kopii `provider` pole. Pravidlo MediaAttribution identifikuje bitovou kopii jako cíl pravidla, takže by ignorovat obrázku `provider` pole a místo toho použít pravidlo MediaAttribution zajistit porušení.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Pokud obsahuje smluvními pravidlo `targetPropertyName` pole, toto pravidlo se vztahuje pouze na cílové pole. Toto pravidlo vztahuje, jinak hodnota nadřazeného objektu, který obsahuje `contractualRules` pole.

V následujícím příkladu `LinkAttribution` pravidlo obsahuje `targetPropertyName` pole, aby se pravidlo vztahuje `description` pole. Pro pravidla, která se vztahují na určitá pole je nutné zahrnout řádek hned za cílových dat, který obsahuje hypertextový odkaz na webu poskytovatele. Například do atribut popis, zahrnují řádek hned následující text, který obsahuje hypertextový odkaz na data, se na poskytovatele webu v tomto případě vytvořit odkaz na contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Přiřazení licencí

Pokud obsahuje seznam smluvními pravidel [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) pravidlo, je třeba zobrazit upozornění na řádek bezprostředně za obsah, který licence, které se vztahují na. `LicenseAttribution` Pravidlo používá `targetPropertyName` pro identifikaci vlastnosti, která licence, které se vztahují na pole.

Zobrazuje následující příklad, který zahrnuje `LicenseAttribution` pravidlo.

![Přiřazení licencí](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Všimněte si licence, že je zobrazit musí obsahovat hypertextový odkaz na web, který obsahuje informace o licenci. Obvykle provedete název licence hypertextový odkaz. Například, pokud je upozornění. **Text v rámci licence kopie SA** a kopie SA je název licence, provedete by kopie SA hypertextový odkaz.

### <a name="link-and-text-attribution"></a>Uvedení odkaz a text.

[LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) a [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) pravidla jsou obvykle používány k identifikaci zprostředkovatele dat. `targetPropertyName` Pole identifikuje pole, které se pravidlo vztahuje.

Do atributu zprostředkovatele, zahrnují řádku bezprostředně za obsah, který práv použít (třeba na cílové pole). Na řádku by měl být označen jasně označuje, že zprostředkovatele je zdroj dat. Například "Data z: contoso.com". Pro `LinkAttribution` pravidla, musíte vytvořit hypertextový odkaz na webu poskytovatele.

Zobrazuje následující příklad, který zahrnuje `LinkAttribution` a `TextAttribution` pravidla.

![Uvedení text odkazu](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Uvedení média

Pokud je entita obsahuje bitovou kopii a zobrazit ji, je nutné zadat odkaz kliknutí na webu poskytovatele. Pokud obsahuje entity [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) pravidla, použijte adresu URL pravidla pro vytvoření odkazu kliknutí. Jinak použijte adresu URL zahrnuté do bitové kopie `provider` pole pro vytvoření odkazu kliknutí.

Zobrazuje následující příklad, který zahrnuje obrázek na `provider` pole a smluvními pravidla. V příkladu zahrnuje smluvními pravidlo, a proto můžete ignorovat obrázku `provider` pole a použít `MediaAttribution` pravidlo.

![Uvedení média](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Hledání nebo prostředí připomínající vyhledávání

Stejně jako s rozhraním API pro Bing webové vyhledávání, rozhraní API služby Bing Entity Search lze použít pouze v důsledku dotaz přímé uživatele nebo vyhledávání nebo v důsledku akce v rámci aplikace nebo prostředí, které lze logicky interpretovat jako požadavek hledání uživatele. Pro účely obrázku jsou některé příklady přijatelné hledání nebo prostředí hledání jako následující.

- Uživatel zadá dotaz přímo do vyhledávacího pole v aplikaci
- Uživatel vybere určitý text nebo bitové kopie a požadavky "Další informace" nebo "Další informace"
- Uživatel požádá o příslušné téma robota vyhledávání
- Uživatel dwells na určitý objekt nebo entity ve scénáři typ visual vyhledávání

Pokud si nejste jisti, zda prostředí lze považovat za hledání jako prostředí, se doporučuje zkontrolovat se společností Microsoft.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít s první žádost, najdete v části [provedení vaše první požadavek](./quick-start.md).

Seznamte se s [rozhraní API služby Bing Entity Search v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odkaz. Odkaz obsahuje záhlaví a parametry dotazu, které používáte k vyžádání výsledky hledání. Zahrnuje také definice objektů odpovědi. 

Pro zlepšení činnost koncového uživatele při hledání pole, najdete v části [rozhraní API pro automatické návrhy v Bingu](../bing-autosuggest/get-suggested-search-terms.md). Jako uživatel zadá své termín dotazu, můžete volat toto rozhraní API získat relevantní dotazu podmínky, které byly používány třetími stranami.

Nezapomeňte si přečíst [požadavky zobrazení a použití Bing](./use-display-requirements.md) tak, že nemáte žádné z pravidel o používání výsledky hledání rozdělit.