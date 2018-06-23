---
title: Postupy použití propojení Entity v textu Analytics REST API (kognitivní služeb pro Microsoft v Azure) | Microsoft Docs
description: Zjistěte, jak identifikovat a řešit pomocí Text Analytics REST API v kognitivní služby společnosti Microsoft na platformě Azure v tomto kurzu návod entity.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342808"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Jak identifikovat propojené entity v Analýza textu (Preview)

[API propojení Entity](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) trvá nestrukturovaných text a pro každý dokument JSON vrátí seznam hodnot od sebe jednoznačně rozlišeny entity s odkazy na další informace na webu (Web Wikipedia a Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Propojování vs entity. Rozpoznávání pojmenovaných entit

V přirozeném jazyce zpracování, můžete snadno nezaměňovat koncepty propojení entity a pojmenované entity rozpoznávání (NER). Ve verzi preview Analýza textu `entities` koncový bod, jenom entity propojení je podporována.

Entita propojení je schopnost určit a odstranit nejednoznačnost identity entity nalezen text (například určení, zda "Mars" je používán jako planety nebo Roman vyšší moci z war). Tento proces vyžaduje přítomnost základní který rozpoznána entity jsou propojeny – Wikipedia slouží jako znalostní báze pro znalosti `entities` koncový bod Analýza textu.

### <a name="language-support"></a>Podpora jazyků

Použití entity propojení v různých jazycích vyžaduje použití odpovídající znalostní bázi v jednotlivé jazyky. Pro entitu propojení v Analýza textu, to znamená, každý jazyk, který je podporován `entities` koncový bod odkaz na odpovídající Wikipedia souhrnu v daném jazyce. Vzhledem k tomu, že velikost souborů corpora se liší mezi jazyky, očekává se, že propojení pro vyvolání funkce na entity se bude lišit.


## <a name="preparation"></a>Příprava

Dokumenty JSON musí mít v tomto formátu: id, text, jazyk

Aktuálně podporované jazyky, najdete v tématu [tento seznam](../text-analytics-supported-languages.md).

Velikost dokumentu musí být v části 5 000 znaků na jednu dokumentu, a může mít až 1 000 položek (ID) na kolekci. Kolekce je odeslán v textu požadavku. V následujícím příkladu je obrázek obsahu, který může odeslat do serveru linking koncové entity.

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

Podrobnosti o definice žádosti lze nalézt v [jak volat rozhraní API Analytics Text](text-analytics-how-to-call-api.md). Pro usnadnění práce se revidovat následující body:

+ Vytvoření **POST** požadavku. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [Entity propojení rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Nastavte koncový bod HTTP pro extrakci klíče frázi. Musí zahrnovat `/entities` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Nastavte hlavičku požadavku zahrnout přístupový klíč pro operace Analýza textu. Další informace najdete v tématu [jak najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V těle žádosti zadejte kolekci dokumentů JSON, který jste připravili pro tuto analýzu

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **rozhraní API testování konzoly** v [dokumentace](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) a struktury požadavek POST do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat požadavek

Po přijetí žádosti o provedení analýzy. Služba přijímá až 100 požadavky za minutu. Každý požadavek může být maximálně 1 MB.

Odvolat, zda je služba bezstavové. Žádná data se ukládají ve vašem účtu. Výsledky jsou vráceny okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všech požadavků POST vrátí JSON formátu odpovědi ID a zjistila vlastnosti.

Výstup se vrátí okamžitě. Stream výsledky do aplikace, která přijímá JSON nebo uložte si výstup do souboru na lokálním systému a následně ho naimportovat do aplikace, která umožňuje řazení, vyhledávání a manipulovat s daty.

Příklad výstupu pro entitu, propojení se zobrazí další:

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

Pokud je k dispozici, odpověď obsahuje Wikipedia ID, adresa URL Wikipedia a Bing ID pro každé zjištěné entity. Ty lze dále vylepšit aplikace o informace týkající se propojené entity.


## <a name="summary"></a>Souhrn

V tomto článku jste se dozvěděli koncepty a pracovní postup pro entity propojení pomocí Analýza textu v kognitivní služby. Shrnutí:

+ [Rozhraní API propojení entity](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují id, text a jazyk kódu.
+ Je požadavek POST `/entities` koncový bod, pomocí přizpůsobené [přístup klíč a koncový bod](text-analytics-how-to-access-key.md) , je platný pro vaše předplatné.
+ Výstup odezvy, který se skládá z propojených entit (včetně jistotu, že skóre, odsazení a webové odkazy pro každý dokumentu ID) lze použít v jakékoli aplikace

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)</br>
 [Stránka produktu Analýza textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu rozhraní API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
