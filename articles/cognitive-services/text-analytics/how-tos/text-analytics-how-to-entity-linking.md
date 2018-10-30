---
title: Rozpoznávání entit pomocí rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávání entit pomocí REST API pro analýzu textu.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: e81428d5bdffb65b5e61a7aba7496da275f249a5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230552"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Použití s názvem rozpoznávání entit v rozhraní Text Analytics (Náhled)

[Rozhraní API pro rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) převezme nestrukturovaného textu a pro každý dokument JSON vrátí seznam jednoznačně rozlišit entit s odkazy na další informace na webu (Wikipedia a Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Propojování entit a rozpoznávání pojmenovaných entit

Rozhraní Text Analytics `entities` koncový bod supprts s názvem rozpoznávání entit (NER) a propojování entit.

### <a name="entity-linking"></a>Entity Linking
Propojování entit je schopnost identifikovat a rozpoznat identity entity v textu (například určující, zda "Mars" je používán jako globální úrovni nebo Roman i war). Tento proces vyžaduje přítomnost který rozpoznáno entity jsou propojeny – Wikipedia slouží jako znalostní báze pro základní znalosti `entities` koncový bod pro analýzu textu.

V rozhraní Text Analytics [verze 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)pouze propojování entit je k dispozici.

### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)
Rozpoznávání entit s názvem (NER) je schopnost určit různé entity v textu a uspořádejte je do kategorií na předem definované třídy. Níže jsou uvedeny podporované tříd entit.

V rozhraní Text Analytics [verze 2.1-Preview](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), propojování entit a rozpoznávání pojmenovaných entit (NER) jsou k dispozici.

### <a name="language-support"></a>Podpora jazyků

Použití rozhraní entity linking v různých jazycích vyžaduje použití odpovídající znalostní báze v každém jazyce. Pro rozhraní entity linking v rozhraní Text Analytics, to znamená, že každý jazyk, který je podporován `entities` koncový bod bude propojovat odpovídající Wikipedia souhrnu v daném jazyce. Protože velikost hromadných datech se pohybuje mezi jazyky, očekává se, že rozhraní entity linking odvolání funkce se bude lišit.

## <a name="supported-types-for-named-entity-recognition"></a>Podporované typy pro rozpoznávání pojmenovaných entit

| Typ  | Podtyp | Příklad: |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | NENÍ K DISPOZICI\*         | "Jan", "Billem Gatesem"     |
| Umístění      | NENÍ K DISPOZICI\*         | "Redmond, Washington", "Paříž"  |
| Organizace  | NENÍ K DISPOZICI\*         | "Microsoft".   |
| Množství      | Číslo        | "6", "šest"     | 
| Množství      | Procentuální podíl    | "50 %", "50 %"| 
| Množství      | Pořadí       | "2", "druhé"     | 
| Množství      | NumberRange   | "4 až 8"     | 
| Množství      | Věk           | "90 den starý", "30 let"    | 
| Množství      | Měna      | "$10.99"     | 
| Množství      | Dimenze     | "10 mil", "40 cm"     | 
| Množství      | Teplota   | "32 stupňů"    |
| DateTime      | NENÍ K DISPOZICI\*         | "6:30 odp. 4. února 2012"      | 
| DateTime      | Datum          | ". Května 2. 2017", "05/02/2017"   | 
| Datum a čas     | Čas          | "8: 00", "8:00"  | 
| DateTime      | DateRange     | "Května 2. na 5. května"    | 
| DateTime      | timeRange     | "18: 00 do 19: 00"     | 
| DateTime      | Doba trvání      | "1 minutu a 45 sekundách"   | 
| DateTime      | Nastavit           | "čtvrtek"     | 
| DateTime      | Časové pásmo      |    | 
| zprostředkovatele identity           | NENÍ K DISPOZICI\*         | http://www.bing.com    |
| Email         | NENÍ K DISPOZICI\*         | support@contoso.com |
\* V závislosti na vstupním a extrahované entit, může vynechat některé entity `SubType`.



## <a name="preparation"></a>Příprava

Musíte mít dokumenty JSON v tomto formátu: ID, text, kód jazyka.

Seznam aktuálně podporovaných jazyků najdete v části [tento seznam](../text-analytics-supported-languages.md).

Dokument nesmí obsahovat více než 5 000 znaků a v každé kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. V následujícím příkladu je ilustraci obsah, který může odeslat za účelem vytváření odkazů entit.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tuto žádost: [rozhraní API služby Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Nastavení koncového bodu HTTP pro extrakci entity. Musí obsahovat prostředek `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu žádosti zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

Analýza se provede po přijetí žádosti. Služba přijme maximálně 100 žádostí za minutu. Každá žádost může mít maximální velikost 1 MB.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Příklad výstupu pro rozhraní entity linking se zobrazí následující:

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro propojování entit pomocí analýzy textu ve službě Cognitive Services. Souhrn:

+ [Entity rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON obsahují v textu žádosti ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/entities` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), které jsou platné pro dané předplatné.
+ Výstup odezvy, který se skládá z propojené entity (včetně jistotu, že skóre, posuny a webové odkazy pro každý dokument ID) lze použít v jakékoli aplikace

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní API pro analýzu textu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
