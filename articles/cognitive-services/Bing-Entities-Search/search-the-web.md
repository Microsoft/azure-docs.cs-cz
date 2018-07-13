---
title: Co je Vyhledávání entit Bingu? | Microsoft Docs
description: Další informace o použití rozhraní API pro vyhledávání entit Bingu pro vyhledávání na webu pro entity a místa.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 275430bc6ee8f935978243e61f68713974648189
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008106"
---
# <a name="what-is-bing-entity-search"></a>Co je Vyhledávání entit Bingu?

Rozhraní API Bingu pro vyhledávání entit odešle dotaz vyhledávání na Bingu a načte výsledky, které zahrnují entit a místa. Místo výsledky obsahovat restaurace, hotelu nebo jiné místní firmy. Bing vrátí místa, pokud dotaz Určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz specifikuje dobře známé lidí, míst (turistických zajímavostí, státy, země atd.) nebo věcí.

## <a name="suggesting--using-search-terms"></a>Navrhování a používání hledaných termínů

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Jakmile uživatel zadá hledaný termín, před nastavením parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) zakódujte termín s použitím kódování URL. Například, pokud uživatel zadá *Marcus Appel*, nastavte `q` k *Marcus + Appel* nebo *Marcus % 20Appel*.

Pokud hledaný výraz obsahuje chybu pravopisu, obsahuje odpovědi na vyhledávání [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objektu. Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Žádost o entity

Příklad žádosti, naleznete v tématu [vytváření první žádosti o](./quick-start.md).

## <a name="the-response"></a>Odpověď

Odpověď obsahuje [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objektu. Pokud Bingu najde entity nebo místo, které se týkají, obsahuje objekt `entities` pole, `places` pole nebo obojí. V opačném případě objektu odpovědi neobsahuje buď pole.
> [!NOTE]
> Entity odpovědí podporují různé trhy, ale odpověď míst podporuje pouze nám obchodní umístění. 

`entities` Pole je [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) objekt, který obsahuje seznam [Entity](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objekty (najdete v článku `value` pole). Seznam může obsahovat jednu entitu dominantní či více entit odstraňování mnohoznačností. 

Dominantní entita je entita, která Bingu se řídí zásadou je pouze entity, která splňuje požadavek (nedochází k nejednoznačnosti ohledně toho, která splňuje entita požadavku). Pokud více entit může vyhověli žádosti, seznam obsahuje více než jednu entitu odstraňování mnohoznačností. Například pokud požadavek používá obecný název filmu franšízová, pravděpodobně seznam obsahuje entity odstraňování mnohoznačností. Ale pokud žádost o konkrétní softwarový z franšízová, pravděpodobně seznam obsahuje dominantní jednu entitu.

Entity obsahují dobře známé osobnosti například zpěváci objektů actor, sportovců z celého světa, modelů, např.; místa a památek například Rainier připojení nebo Lincoln Památný; a věci, jako je například název banánů, goldendoodle, knihy nebo video. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole obsahuje pomocné parametry, které určují typ entity. Například, pokud je osoba, videa, aby nebo přitažlivosti. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Následující příklad zobrazuje odpověď, která obsahuje entitu dominantní a odstraňování mnohoznačností.

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

Obsahuje entitu `name`, `description`, a `image` pole. Tato pole se zobrazí v prostředí pro uživatele, musíte je atribut. `contractualRules` Pole obsahuje seznam Poděkování, které je nutné použít. Smluvní pravidlo identifikuje pole, které se vztahuje na přidělení. Informace o použití attribution najdete v tématu [Attribution](#data-attribution).

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

Při zobrazení informací o entitách (název, popis a obrázek), musíte taky použít adresu URL v `webSearchUrl` pole pro vyhledávání Bingu výsledky stránky obsahující tuto entitu.


`places` Je pole [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) objekt, který obsahuje seznam [místo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objekty (najdete v článku `value` pole). Seznam obsahuje jeden nebo více místních entit, které odpovídají požadavku.

Místa zahrnují restaurace, hotely nebo místní firmy. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole obsahuje pomocné parametry, které určují typ místní entity. Seznam obsahuje seznam pomocných parametrů, jako je místo, LocalBusiness, restauraci. Každý po sobě jdoucích pomocný parametr jako pole způsobí zúžení typu entity. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Entity odpovědí podporují různé trhy, ale odpověď míst podporuje pouze nám obchodní umístění. 

Entity místní podporou dotazů jako například *restaurace poblíž* vyžadují podle umístění uživatele poskytnou přesné výsledky. Své žádosti vždy používejte X-Search-umístění a hlavičky X-MSEdge ClientIP k určení umístění uživatele. Když se Bingu domnívá, dotaz je výhodná podle umístění uživatele, nastaví `askUserForLocation` pole [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) k **true**. 

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

Místo výsledek obsahuje název na místě, adresa, telefonní číslo a adresa URL webu entity. Při zobrazení informací o entitách, musíte taky použít adresu URL v `webSearchUrl` pole pro vyhledávání Bingu výsledky stránky obsahující tuto entitu.

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
> Jste nebo třetích stran vaším jménem nemusí použít, zachovat, ukládat, ukládat do mezipaměti, sdílet, nebo distribuovat všechna data z entity rozhraní API pro účely testování, vývoje, školení, distribuci nebo zpřístupnění službám jiných společností než Microsoft nebo funkce.  

## <a name="data-attribution"></a>Přidělení dat

Odpovědi rozhraní API Bingu pro Entity obsahují informace, které vlastní třetími stranami. Budete muset zajistit, aby že využití je vhodná, například při dodržení licence creative commons, které využívají ke svému činnost koncového uživatele.

Pokud odpověď nebo výsledek obsahuje `contractualRules`, `attributions`, nebo `provider` polí, musí atribut data. Pokud odpověď nesmí obsahovat žádný z těchto polí, není třeba žádná přiřazení. V případě, že odpověď obsahuje `contractualRules` pole a `attributions` a/nebo `provider` polí, musíte použít smluvní pravidla pro atribut data.

Následující příklad ukazuje entita, která obsahuje smluvní pravidlo MediaAttribution a bitovou kopii, která zahrnuje `provider` pole. Pravidlo MediaAttribution označuje obrázek, který jako cíl pravidla, takže by ignorovat na obrázku `provider` pole a místo toho použít pravidlo MediaAttribution k označení autorství.  

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

Pokud obsahuje smluvní pravidlo `targetPropertyName` pole, se pravidlo vztahuje pouze na cílové pole. V opačném případě se pravidlo vztahuje na nadřazený objekt, který obsahuje `contractualRules` pole.

V následujícím příkladu `LinkAttribution` pravidlo obsahuje `targetPropertyName` pole, aby se pravidlo vztahuje `description` pole. Pro pravidla, které se vztahují na konkrétní pole musí obsahovat řádek bezprostředně následuje cílových dat, která obsahuje hypertextový odkaz na webu poskytovatele. Například kterému budou připsány popis, přidejte řádek bezprostředně následující text, který obsahuje hypertextový odkaz na data na webu poskytovatele, v tomto případě vytvořit odkaz na contoso.com.

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

Pokud obsahuje seznam pravidel smluvní [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) pravidlo, je třeba zobrazit oznámení na řádek bezprostředně za obsah, který se vztahuje licence na. `LicenseAttribution` Pravidlo používá `targetPropertyName` pole pro vlastnost, která se vztahuje licence k identifikaci.

Následující příklad zobrazuje příklad, který zahrnuje `LicenseAttribution` pravidlo.

![Přiřazení licencí](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Licence Všimněte si, že zobrazení musí obsahovat hypertextový odkaz na web, který obsahuje informace o licenci. Obvykle vytvořit název licence hypertextový odkaz. Například, pokud je oznámení **Text v rámci licence kopie SA** a kopie SA je název licence, byste vytvořit kopie SA hypertextový odkaz.

### <a name="link-and-text-attribution"></a>Přiřazení odkazu a text

[LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) a [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) pravidla se obvykle používají k identifikaci zprostředkovatele data. `targetPropertyName` Pole určuje pole, které se pravidlo vztahuje.

Pro atribut zprostředkovatele, přidejte řádek bezprostředně následující obsah, který se použije Poděkování (třeba na cílové pole). Označuje, že zprostředkovatelů je zdroj dat by měly být jasně popsány řádku. Například "Data z: contoso.com". Pro `LinkAttribution` pravidla, je nutné vytvořit hypertextový odkaz na webu poskytovatele.

Následující příklad zobrazuje příklad, který zahrnuje `LinkAttribution` a `TextAttribution` pravidla.

![Attribution text odkazu](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Attribution média

Pokud entita obsahuje bitovou kopii a je zobrazit, je nutné zadat odkaz pomocí kliknutí na webu poskytovatele. Pokud obsahuje entitu [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) pravidla, použijte adresu URL pravidla pro vytvoření odkazu pomocí kliknutí. V opačném případě použijte adresu URL do image zahrnout `provider` pole pro vytvoření odkazu pomocí kliknutí.

Ukazuje následující příklad, který zahrnuje obrazu `provider` pole a smluvní pravidla. Tento příklad zahrnuje smluvní pravidlo, a proto můžete ignorovat na obrázku `provider` pole a použít `MediaAttribution` pravidlo.

![Attribution média](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Hledání nebo role experience systému jako v hledání

Stejně jako s rozhraním API webové vyhledávání Bingu, rozhraní API Bingu pro vyhledávání entit se použít jenom jako výsledek dotazu s přímým přístupem uživatele nebo vyhledávání, nebo jako výsledek akce v rámci aplikace nebo prostředí, které logicky může být interpretován jako požadavek hledání uživatele. Pro ilustraci se následující je několik příkladů přijatelné vyhledávání nebo hledání podobných prostředí.

- Uživatel zadá dotaz přímo do vyhledávacího pole v aplikaci
- Uživatel vybere určitý text nebo obrázek a požadavky "Další informace o" nebo "Další informace"
- Uživatel požádá robota vyhledávání o určitém tématu
- Uživatel dwells na určitý objekt nebo entity ve scénáři typ vizuálního vyhledávání

Pokud si nejste jistí, jestli vaše prostředí může být považována za hledání podobných prostředí, doporučujeme zkontrolovat, zda se společností Microsoft.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít s vaší první žádost, naleznete v tématu [provádění svůj první požadavek](./quick-start.md).

Seznamte se s [API pro vyhledávání entit Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odkaz. Odkaz obsahuje hlavičky a parametry dotazu, které používáte k vyžádání výsledky hledání. Obsahují také definice objektů odpovědi. 

Pokud chcete zlepšit uživatelské prostředí vyhledávacího pole, přečtěte si o [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Zatímco uživatel zadává termín dotazu, můžete zavoláním tohoto rozhraní API získat relevantní termíny dotazů, které použili jiní uživatelé.

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./use-display-requirements.md), abyste neporušili žádná pravidla používání výsledků hledání.