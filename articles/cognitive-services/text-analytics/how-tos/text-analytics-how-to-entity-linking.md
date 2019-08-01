---
title: Použití rozpoznávání entit s rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat entity pomocí Analýza textu REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: ea7d3f56aa512b8f5998d710451ff3b37659ca13
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697849"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

[Rozhraní API pro rozpoznávání pojmenovaných entit](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) přebírá nestrukturovaný text a pro každý dokument JSON vrátí seznam nejednoznačných entit s odkazy na Další informace na webu (Wikipedii a Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Propojování entit a rozpoznávání pojmenovaných entit

Koncový bod analýza textu `entities` ' podporuje rozpoznávání pojmenovaných entit (ner) a propojení entit.

### <a name="entity-linking"></a>Entity Linking
Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda je služba Mars používána jako globálním nebo jako Římská jsou of War). Tento proces vyžaduje přítomnost znalostní báze, ke které jsou propojené entity připojené – Wikipedii se používá jako znalostní báze pro `entities` koncový bod analýza textu.

### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)
Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd. Podporované třídy entit jsou uvedeny níže.

V Analýza textu [verze 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)jsou pro několik jazyků k dispozici jak propojení entit, tak rozpoznávání pojmenovaných entit (ner). Další informace najdete v článku věnovaném [jazykové podpoře](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

### <a name="language-support"></a>Podpora jazyků

Použití propojení entit v různých jazycích vyžaduje použití odpovídající znalostní báze v jednotlivých jazycích. V případě propojení entit v analýza textu to znamená, že každý jazyk podporovaný `entities` koncovým bodem odkazuje na odpovídající corpus Wikipedii v daném jazyce. Vzhledem k tomu, že velikost Corpora se v různých jazycích liší, očekává se, že se projeví i odvolání funkce propojení entit.

## <a name="supported-types-for-named-entity-recognition"></a>Podporované typy pro rozpoznávání pojmenovaných entit

| type  | SubType | Příklad |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | NENÍ K DISPOZICI\*         | Jan, vyúčtování Branch     |
| Location      | NENÍ K DISPOZICI\*         | "Redmond, Washington", "Paříž"  |
| Organizace  | NENÍ K DISPOZICI\*         | "Microsoft"   |
| Množství      | Číslo        | "6", "šest"     |
| Množství      | Procento    | "50 %", "padesát procent"|
| Množství      | Pořadí       | "2.", "druhý"     |
| Množství      | Číselný rozsah   | "4 až 8"     |
| Množství      | Věk           | "90 den starý", "30 let starý"    |
| Množství      | Currency      | "10,99 USD"     |
| Množství      | Dimenze     | "10 mil", "40 cm"     |
| Množství      | Teplota   | "32 stupňů"    |
| Datetime      | NENÍ K DISPOZICI\*         | "4. února 2012 – 18:30"      |
| Datetime      | Date          | "2. května 2017", "2. 5. 2017"   |
| Datetime      | Time          | "8:00", "8:00"  |
| Datetime      | Rozsah dat     | "2. května až 5. května"    |
| Datetime      | Časový rozsah     | "18:00 až 19:00"     |
| Datetime      | Trvání      | "1 minuta a 45 sekund"   |
| Datetime      | Sada           | "každé úterý"     |
| Datetime      | časové pásmo      |    |
| URL           | NENÍ K DISPOZICI\*         | "https:\//www.Bing.com"    |
| Email         | NENÍ K DISPOZICI\*         | "support@contoso.com" |

\*V závislosti na vstupních a extrahovaných entitách můžou některé entity vynechat `SubType`.  Všechny uvedené podporované typy entit jsou k dispozici pouze pro anglické, zjednodušené, francouzské, německé a španělské jazyky.



## <a name="preparation"></a>Příprava

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk

V aktuálně podporovaných jazycích se podívejte na [Tento seznam](../text-analytics-supported-languages.md).

Velikost dokumentu musí být 5 120 znaků v jednom dokumentu a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující příklad je příkladem obsahu, který můžete odeslat na konec propojování entit.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Strukturování žádosti

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro entity](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Nastavte koncový bod HTTP pro extrakci entity. Musí obsahovat prostředek `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu žádosti zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundy, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Příklad výstupu pro propojení entit se zobrazí jako další:

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

V tomto článku jste zjistili koncepty a pracovní postupy pro propojení entit pomocí Analýza textu v Cognitive Services. Souhrn:

+ [Rozhraní API pro entity](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/entities` a používá individuální [přístupový klíč a koncový bod](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), které jsou platné pro dané předplatné.
+ Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní API pro analýzu textu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
* [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)
