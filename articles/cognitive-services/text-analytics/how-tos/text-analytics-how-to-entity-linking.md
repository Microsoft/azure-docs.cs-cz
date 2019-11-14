---
title: Použití rozpoznávání entit s rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak identifikovat a odstranit identitu entity, která se nachází v textu s Analýza textu REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/12/2019
ms.author: aahi
ms.openlocfilehash: 5933c7ec56ded971e4daf96ea6d4302c04921f2f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031431"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

[Rozhraní API pro rozpoznávání pojmenovaných entit](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) přebírá nestrukturovaný text a pro každý dokument JSON vrátí seznam nejednoznačných entit s odkazy na Další informace na webu (Wikipedii a Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Propojování entit a rozpoznávání pojmenovaných entit

Koncový bod Analýza textu `entities` podporuje rozpoznávání pojmenovaných entit (NER) i propojení entit.

### <a name="entity-linking"></a>Propojení entit
Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda je služba Mars používána jako globálním nebo jako Římská jsou of War). Tento proces vyžaduje přítomnost znalostní báze, ke které jsou propojené entity připojené – Wikipedii se používá jako znalostní báze Analýza textu koncového bodu `entities`.

### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)
Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd nebo typů. 

## <a name="named-entity-recognition-v3-public-preview"></a>Rozpoznávání pojmenovaných entit V3 Public Preview

[Další verze rozpoznávání pojmenovaných entit]( https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) je nyní k dispozici ve verzi Public Preview. Poskytuje aktualizace pro odkazování na entity i pro rozpoznávání pojmenovaných entit. 

:::row:::
    :::column span="":::
        **Funkce**
    :::column-end:::
    ::: column span="":::
        **Popis** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Rozšířené typy a podtypy entit
    :::column-end:::
    :::column span="":::
     Rozšířená klasifikace a detekce pro několik pojmenovaných typů entit
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Samostatné koncové body požadavku 
    :::column-end:::
    :::column span="":::
        Samostatné koncové body pro posílání NER entit a žádostí o připojení.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parametr
    :::column-end:::
    :::column span="":::
        Volitelný parametr pro výběr verze Analýza textuho modelu. V současné době je k dispozici pouze výchozí model.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Typy entit

Rozpoznávání pojmenovaných entit V3 poskytuje rozšířené zjišťování napříč více typy. V současné době může NER V3 rozpoznat následující kategorie entit. Podrobný seznam podporovaných entit a jazyků naleznete v článku [pojmenované typy entit](../named-entity-types.md) .

* Obecné
* Osobní údaje 

### <a name="request-endpoints"></a>Koncové body požadavku

Rozpoznávání pojmenovaných entit V3 používá samostatné koncové body pro žádosti NER a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

NER
* Obecné entity – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entity osobních informací – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Správa verzí modelů

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Podporované typy pro rozpoznávání pojmenovaných entit v2

> [!NOTE]
> Následující entity jsou podporovány funkcí rozpoznávání pojmenovaných entit (NER) verze 2. [Ner V3](#named-entity-recognition-v3-public-preview) je ve verzi Public Preview a významně rozšiřuje počet a hloubku entit rozpoznaných v textu.   

| Typ  | SubType | Příklad |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | Není k dispozici\*         | Jan, vyúčtování Branch     |
| Umístění      | Není k dispozici\*         | "Redmond, Washington", "Paříž"  |
| Organizace  | Není k dispozici\*         | "Microsoft"   |
| Množství      | Číslo        | "6", "šest"     |
| Množství      | Procento    | "50 %", "padesát procent"|
| Množství      | Pořadí       | "2.", "druhý"     |
| Množství      | Věk           | "90 den starý", "30 let starý"    |
| Množství      | Měna      | "10,99 USD"     |
| Množství      | Dimenze     | "10 mil", "40 cm"     |
| Množství      | Teplota   | "32 stupňů"    |
| Datum a čas      | Není k dispozici\*         | "4. února 2012 – 18:30"      |
| Datum a čas      | Datum          | "2. května 2017", "2. 5. 2017"   |
| Datum a čas      | Čas          | "8:00", "8:00"  |
| Datum a čas      | Rozsah dat     | "2. května až 5. května"    |
| Datum a čas      | Časový rozsah     | "18:00 až 19:00"     |
| Datum a čas      | Doba trvání      | "1 minuta a 45 sekund"   |
| Datum a čas      | Nastavit           | "každé úterý"     |
| Adresa URL           | Není k dispozici\*         | "https:\//www.bing.com"    |
| Email         | Není k dispozici\*         | support@contoso.com |

\* v závislosti na vstupních a extrahovaných entitách, mohou některé entity vynechat `SubType`.  Všechny uvedené podporované typy entit jsou k dispozici pouze pro jazyky anglické, zjednodušené, francouzštiny, němčiny a španělštiny.

### <a name="language-support"></a>Podpora jazyků

Použití propojení entit v různých jazycích vyžaduje použití odpovídající znalostní báze v jednotlivých jazycích. V případě propojení entit v Analýza textu to znamená, že každý jazyk podporovaný koncovým bodem `entities` bude odkazovat na odpovídající corpus Wikipedii v daném jazyce. Vzhledem k tomu, že velikost Corpora se v různých jazycích liší, očekává se, že se projeví i odvolání funkce propojení entit. Další informace najdete v článku věnovaném [jazykové podpoře](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

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

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Přečtěte si dokumentaci k rozhraní API pro tento požadavek: [entity API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Nastavte koncový bod HTTP pro extrakci klíčových frází pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Je nutné zahrnout `/text/analytics/v2.1/entities`. Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu žádosti zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

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
+ Žádost POST je určená pro koncový bod `/entities` a používá individuální [přístupový klíč a koncový bod](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), který je platný pro dané předplatné.
+ Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní API pro analýzu textu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
* [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)
