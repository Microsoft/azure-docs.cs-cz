---
title: Vyhledávání entit pomocí rozhraní API Bingu pro vyhledávání entit
titlesuffix: Azure Cognitive Services
description: Použití rozhraní API Bingu pro vyhledávání entit extrahovat a vyhledávat entity a místa z vyhledávacích dotazů.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b1d46d125d390f8612c5708e1964e0626acde343
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757443"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Vyhledávání entit pomocí rozhraní API Bingu pro Entity

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Navrhnout hledané výrazy s rozhraním API pro automatické návrhy Bingu

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Jakmile uživatel zadá hledaný termín, před nastavením parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) zakódujte termín s použitím kódování URL. Například, pokud uživatel zadá *Marcus Appel*, nastavte `q` k *Marcus + Appel* nebo *Marcus % 20Appel*.

Pokud hledaný výraz obsahuje chybu pravopisu, odpověď na vyhledávání obsahuje objekt[QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext). Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Odpověď rozhraní API pro vyhledávání entit Bingu

Obsahuje odpověď rozhraní API [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objektu. Pokud Bing najde relevantní entitu nebo místo, obsahuje objekt pole `entities`, pole `places` nebo obojí. V opačném případě objekt odpovědi neobsahuje žádné pole.
> [!NOTE]
> Odpovědi na entity podporují různé trhy, ale odpovědi na místa podporují pouze obchodní místa v USA. 

Pole `entities` je objekt [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference), který obsahuje seznam objektů[Entity](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) (viz pole `value`). Seznam může obsahovat jednu dominantní entitu nebo více nejednoznačných entit nebo obojí. 

Dominantní entity je vrácena, když Bingu se řídí zásadou bude pouze entity, která splňuje požadavek (nedochází k nejednoznačnosti ohledně toho, která splňuje entita požadavku). Pokud může žádosti vyhovět více entit, seznam obsahuje více než jednu nejednoznačnou entitu. Například pokud požadavek používá obecný název filmové franšízy, seznam pravděpodobně obsahuje nejednoznačné entity. Pokud však požadavek specifikuje konkrétní název franšízy, seznam pravděpodobně obsahuje jednu dominantní entitu.

Entity obsahují dobře známé osobnosti jako jsou zpěváci, herci, sportovci, modelky atd. Také obsahují místa a památky jako je Mount Rainier nebo Lincoln Memorial a věci jako banán, goldendoodle, kniha nebo název filmu. Pole [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) obsahuje pomocné parametry, které určují typ entity. Například, jestli se jedná o osobu, film, zvíře nebo atrakci. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Následující příklad zobrazuje odpověď, která obsahuje dominantní a nejednoznačnou entitu.

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

Entita obsahuje pole `name`, `description` a `image`. Pokud zobrazíte tato pole ve svém uživatelském prostředí, musíte jim přiřadit zdroj. Pole `contractualRules` obsahuje seznam atribucí, které musíte použít. Smluvní pravidlo identifikuje pole, na které se vztahuje pravidlo pro atribuci. Informace uplatnění atribuce najdete v tématu [Atribuce](#data-attribution).

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

Při zobrazení informací o entitách (název, popis a obrázek), musíte taky použít adresu URL v poli `webSearchUrl`, abyste propojili entitu se stránkou výsledků vyhledávání Bingu, které danou entitu obsahují.

## <a name="find-places"></a>Najít místa

`places` Je pole [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) objekt, který obsahuje seznam [místo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objekty (naleznete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types) Další informace). Seznam obsahuje jeden nebo více místních entit, které vyhovují požadavku.

Místa zahrnují restaurace, hotely nebo místní firmy. Pole [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) obsahuje pomocné parametry, které určují typ místní entity. Seznam obsahuje seznam pomocných parametrů, jako je místo, místní firma nebo restaurace. Každý další použitý pomocný parametr zužuje možnosti typu entity. Seznam možných typů najdete v tématu [typy entit](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Odpovědi na entity podporují různé trhy, ale odpovědi na místa podporují pouze obchodní místa v USA. 

Dotazy na místní entity v okolí jako například *restaurace poblíž* vyžadují zadání polohy uživatele, aby mohly poskytnout přesné výsledky. K určení polohy uživatele byste měli pro své žádosti vždy využívat záhlaví X-Search-Location a X-MSEdge ClientIP. Pokud se Bing domnívá, že je pro dotaz výhodné určení polohy uživatele, nastaví `askUserForLocation` pole [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) na hodnotu **true**(pravda). 

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

Výsledek místa obsahuje jméno místa, adresu, telefonní číslo a adresu URL na web dané entity. Při zobrazení informací o entitách musíte taky použít adresu URL v poli `webSearchUrl`, abyste propojili entitu se stránkou výsledků vyhledávání Bingu, které danou entitu obsahují.

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
> Vy nebo třetí strana jednající vaším jménem nesmí používat, uchovávat, ukládat, ukládat do mezipaměti, sdílet, nebo distribuovat jakákoliv data z rozhraní API entit pro účely testování, vývoje, školení, distribuce nebo zpřístupnění službám nebo funkcím jiných společností než Microsoft.  

## <a name="data-attribution"></a>Atribuce dat

Odpovědi rozhraní API Bingu pro entity obsahují informace vlastněné třetími stranami. Je vaší zodpovědností zajistit odpovídající použití, například tím, že budete v souladu s licencemi Creative Commons, na které můžou vaši uživatelé spoléhat.

Pokud odpověď nebo výsledek obsahuje pole `contractualRules`, `attributions`, nebo `provider`, musíte provést atribuci dat. Pokud odpověď neobsahuje žádné z těchto polí, není atribuce potřeba. Pokud odpověď obsahuje pole `contractualRules` a pole `attributions` nebo `provider`, musíte použít smluvní pravidla pro atribuci dat.

Následující příklad ukazuje entitu, která obsahuje smluvní pravidlo MediaAttribution a Image zahrnující pole`provider`. Pravidlo MediaAttribution označuje image jako cíl pravidla, takže v takovém případě ignorujte pole `provider` image a místo toho použijte k atribuci pravidlo MediaAttribution.  

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

Pokud smluvní pravidlo obsahuje pole `targetPropertyName`, pak se pravidlo vztahuje pouze na cílové pole. V opačném případě se pravidlo vztahuje na nadřazený objekt, který obsahuje pole `contractualRules`.

V následujícím příkladu pravidlo`LinkAttribution` obsahuje pole `targetPropertyName`, takže se pravidlo vztahuje na pole `description`. Pro pravidla, která se vztahují na určitá pole, musíte vložit řádek bezprostředně za cílová data, který bude obsahovat hypertextový odkaz na web zprostředkovatele. Pokud budete například provádět atribuci popisu, vložte bezprostředně za text popisu řádek, který bude obsahovat hypertextový odkaz na data na webu zprostředkovatele, v tomto případě vytvořte odkaz na contoso.com.

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

### <a name="license-attribution"></a>Atribuce licencí

Pokud obsahuje seznam smluvních pravidel pravidlo [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), musíte uvést poznámku na řádek bezprostředně za obsah, na který se licence vztahuje. Pravidlo`LicenseAttribution` používá pole `targetPropertyName` k identifikaci služby, na kterou se licence vztahuje.

Následující příklad ukazuje použití pravidla`LicenseAttribution`.

![Atribuce licencí](../media/cognitive-services-bing-entities-api/licenseattribution.png)

Poznámky k licencím, které uvádíte, musí obsahovat hypertextový odkaz na web obsahující informace o dané licenci. Obvykle vytvoříte z názvu licence hypertextový odkaz. Pokud je například poznámka **Text je pod licencí CC-BY-SA** a CC-BY-SA je název licence, uděláte z CC-BY-SA hypertextový odkaz.

### <a name="link-and-text-attribution"></a>Atribuce odkazu a textu

Pravidla [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) a [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) se obvykle používají k identifikaci zprostředkovatele dat. Pole `targetPropertyName` určuje pole, na které se pravidlo vztahuje.

Pro určení zdroje zprostředkovatelů, vložte řádek bezprostředně za obsah,na který se určení zdroje vztahuje (například za cílové pole). Řádek by měl jasně označovat, že zdrojem dat jsou zprostředkovatelé. Například „Data z: contoso.com“. Pro pravidla `LinkAttribution` musíte vytvořit hypertextový odkaz na web zprostředkovatele.

Následující příklad ukazuje použití pravidel `LinkAttribution` a `TextAttribution`.

![Atribuce odkazu a textu](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Atribuce médií

Pokud entita obsahuje image a vy ji zobrazujete, musíte zadat 	klikací odkaz na web zprostředkovatele. Pokud entita obsahuje pravidlo[MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution), použijte k vytvoření klikacího odkazu adresu URL pravidla. V opačném případě použijte k vytvoření klikacího odkazu adresu URL, která je v poli `provider` dané image.

Zde je příklad, který obsahuje pole `provider` image a smluvní pravidla. Tento příklad zahrnuje smluvní pravidlo, a proto budete ignorovat pole `provider` image a použijete pravidlo `MediaAttribution`.

![Atribuce médií](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Vyhledávací a vyhledávání podobná prostředí

Stejně jako u rozhraní API Bingu pro vyhledávání na webu, rozhraní API Bingu pro vyhledávání entit je možné použít jenom jako výsledek přímého dotazu nebo vyhledávání uživatele, nebo jako výsledek akce v rámci aplikace nebo prostředí, která může být logicky interpretovaná jako požadavek vyhledávání uživatele. Pro ilustraci jsou tady uvedené některé příklady přijatelných vyhledávacích a vyhledávání podobných prostředí.

- Uživatel zadá dotaz přímo do vyhledávacího pole v aplikaci
- Uživatel vybere určitý text nebo obrázek a zadá požadavek „více informací“ nebo „další informace“
- Uživatel se zeptá vyhledávacího robota na určité téma
- Uživatel spočívá na určitém objektu nebo entitě při použití vizuálního typu vyhledávání

Pokud si nejste jistí, jestli vaše prostředí může být považované za vyhledávání podobné prostředí, doporučujeme, abyste se obrátili na společnost Microsoft.

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další postup

* Vyzkoušejte [rychlý Start](../quickstarts/csharp.md) začít vyhledávat entity, které rozhraní API Bingu pro vyhledávání entit.