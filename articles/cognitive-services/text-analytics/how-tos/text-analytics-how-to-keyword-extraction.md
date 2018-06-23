---
title: 'Postupy: klíče frázi extrakce v textu Analytics REST API (kognitivní služeb pro Microsoft v Azure) | Microsoft Docs'
description: Jak z něho extrahovat klíče frází pomocí Text Analytics REST API v kognitivní služby společnosti Microsoft na platformě Azure v tomto kurzu návod.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342648"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Extrahování klíčových frází v Analýza textu

[Klíč frázi extrakce API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) vyhodnotí nestrukturovaných text a pro každý dokument JSON vrátí seznam klíčů frází. 

Tato možnost je užitečná, pokud potřebujete rychle identifikovat hlavní body v kolekci dokumentů. Například daného vstupního textu "jídlo byla chutný a nebyly k dispozici vynikající pracovníci", služba vrátí hlavní body čtených: "jídlo" a "vynikající zaměstnanci".

V současné době podporuje klíč frázi extrakce angličtina, němčina, španělština a japonštině. Další jazyky jsou ve verzi preview. Další informace najdete v tématu [podporované jazyky](../text-analytics-supported-languages.md).

## <a name="preparation"></a>Příprava

Rozbalení klíče frázi funguje nejlíp, když poskytují větší bloky textu práci. Toto je opačné z postojích analýzy, které provádí lépe menší bloky textu. K dosažení nejlepších výsledků z obou operace, zvažte změnu struktury vstupy odpovídajícím způsobem.

Dokumenty JSON musí mít v tomto formátu: id, text, jazyk

Velikost dokumentu musí být v části 5 000 znaků na jednu dokumentu, a může mít až 1 000 položek (ID) na kolekci. Kolekce je odeslán v textu požadavku. V následujícím příkladu je obrázek obsahu, který může odeslat pro extrakci klíče frázi.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>Krok 1: Struktury požadavku

Podrobnosti o definice žádosti lze nalézt v [jak volat rozhraní API Analytics Text](text-analytics-how-to-call-api.md). Pro usnadnění práce se revidovat následující body:

+ Vytvoření **POST** požadavku. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [frází klíč rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Nastavte koncový bod HTTP pro extrakci klíče frázi. Musí zahrnovat `/keyphrases` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Nastavte hlavičku požadavku zahrnout přístupový klíč pro operace Analýza textu. Další informace najdete v tématu [jak najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V těle žádosti zadejte kolekci dokumentů JSON, který jste připravili pro tuto analýzu

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **rozhraní API testování konzoly** v [dokumentace](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) a struktury požadavek POST do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat požadavek

Po přijetí žádosti o provedení analýzy. Služba přijímá až 100 požadavky za minutu. Každý požadavek může být maximálně 1 MB.

Odvolat, zda je služba bezstavové. Žádná data se ukládají ve vašem účtu. Výsledky jsou vráceny okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všech požadavků POST vrátí JSON formátu odpovědi ID a zjistila vlastnosti.

Výstup se vrátí okamžitě. Stream výsledky do aplikace, která přijímá JSON nebo uložte si výstup do souboru na lokálním systému a následně ho naimportovat do aplikace, která umožňuje řazení, vyhledávání a manipulovat s daty.

Další je uveden příklad výstupu pro extrakci klíče frázi:

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Jak jsme uvedli, nástroje analyzer vyhledá a zahodí slova není nezbytné a udržuje jeden termín nebo fráze, které se zobrazují jako předmět nebo objekt věty. 

## <a name="summary"></a>Souhrn

V tomto článku jste se dozvěděli, koncepty a pracovní postup pro extrakci klíče frázi pomocí Analýza textu v kognitivní služby. Shrnutí:

+ [Klíč frázi extrakce rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují id, text a jazyk kódu.
+ Je požadavek POST `/keyphrases` koncový bod, pomocí přizpůsobené [přístup klíč a koncový bod](text-analytics-how-to-access-key.md) , je platný pro vaše předplatné.
+ Odpověď výstupu, který obsahuje klíčová slova a slovní spojení pro každý dokument ID, Streamovat k jakékoli aplikaci, která přijímá formát JSON, včetně aplikace Excel a Power BI a další.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)</br>
 [Stránka produktu Analýza textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu rozhraní API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
