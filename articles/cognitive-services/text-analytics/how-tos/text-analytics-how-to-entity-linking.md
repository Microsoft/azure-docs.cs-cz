---
title: Použití rozhraní entity linking pomocí rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak identifikovat a vyřešit entit pomocí REST API pro analýzu textu.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: ad2168806f9ddd124faf66cdb5a0f51ed13dfadc
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604735"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Zjištění propojených entit v rozhraní Text Analytics (Náhled)

[Rozhraní API služby Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) převezme nestrukturovaného textu a pro každý dokument JSON vrátí seznam jednoznačně rozlišit entit s odkazy na další informace na webu (Wikipedia a Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Entity Linking vs. Rozpoznávání pojmenovaných entit

Při zpracování přirozeného jazyka, lze snadno zaměnitelná koncepty propojování entit a rozpoznávání pojmenovaných entit (NER). Ve verzi preview pro analýzu textu `entities` koncový bod, jenom rozhraní entity linking se nepodporuje.

Rozhraní entity linking je schopnost identifikovat a rozpoznat identity entity v textu (například určující, zda "Mars" je používán jako globální úrovni nebo Roman i war). Tento proces vyžaduje přítomnost který rozpoznáno entity jsou propojeny – Wikipedia slouží jako znalostní báze pro základní znalosti `entities` koncový bod pro analýzu textu.

### <a name="language-support"></a>Podpora jazyků

Použití rozhraní entity linking v různých jazycích vyžaduje použití odpovídající znalostní báze v každém jazyce. Pro rozhraní entity linking v rozhraní Text Analytics, to znamená, že každý jazyk, který je podporován `entities` koncový bod bude propojovat odpovídající Wikipedia souhrnu v daném jazyce. Protože velikost hromadných datech se pohybuje mezi jazyky, očekává se, že rozhraní entity linking odvolání funkce se bude lišit.


## <a name="preparation"></a>Příprava

Dokumenty JSON musí mít v tomto formátu: id, text, jazyk

Seznam aktuálně podporovaných jazyků najdete v tématu [tento seznam](../text-analytics-supported-languages.md).

Velikost dokumentu musí být v jednom dokumentu v části 5 000 znaků a může mít až 1 000 položek (ID) na kolekci. Kolekce je v textu požadavku odeslán. V následujícím příkladu je ilustraci obsah, který může odeslat za účelem vytváření odkazů entit.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Krok 1: Struktury požadavku

Podrobnosti o definice požadavku najdete v [volání rozhraní Text Analytics API](text-analytics-how-to-call-api.md). Pro usnadnění práce jsou revidovat následující body:

+ Vytvoření **příspěvek** požadavku. Projděte si dokumentaci k rozhraní API pro tuto žádost: [rozhraní API služby Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Nastavení koncového bodu HTTP pro extrakci klíčových frází. Musí zahrnovat `/entities` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Nastavte hlavičku požadavku zahrnout přístupový klíč pro operace pro analýzu textu. Další informace najdete v tématu [jak najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, který jste připravili pro tuto analýzu

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **testovací rozhraní API konzoly** v [dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktury žádost a PUBLIKUJE je do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

Analýza je provedena při přijetí požadavku. Služba přijímá až 100 požadavků za minutu. Každý požadavek může být maximálně 1 MB.

Připomínáme, že je Bezstavová služba. Žádná data uložená ve vašem účtu. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všech požadavků POST vrátit odpověď s ID ve formátu JSON a byly zjištěny vlastnosti.

Výstup se vrátí okamžitě. Můžete Streamovat výsledky do aplikace, která přijímá JSON nebo uložte výstup do souboru v místním systému a následně ji naimportovat do aplikace, která umožňuje řazení, hledání a manipulaci s daty.

Příklad výstupu pro rozhraní entity linking se zobrazí následující:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Pokud je k dispozici, odpověď obsahuje Wikipedia ID, adresu URL Wikipedie a Bing ID pro každou zjištěnou entitu. Ty je použít k dalšímu vylepšení vaší aplikace s informacemi o propojené entitě.


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro propojování entit pomocí analýzy textu ve službě Cognitive Services. V souhrnu:

+ [Rozhraní API služby entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují id, text a jazyka kódu.
+ Je požadavek POST `/entities` koncový bod, pomocí firemního [přístup ke key a koncového bodu](text-analytics-how-to-access-key.md) , který je platný pro vaše předplatné.
+ Výstup odezvy, který se skládá z propojené entity (včetně jistotu, že skóre, posuny a webové odkazy pro každý dokument ID) lze použít v jakékoli aplikace

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Stránka produktu text Analytics](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
