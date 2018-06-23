---
title: 'Postupy: postojích analýzy v textu Analytics REST API (kognitivní služeb pro Microsoft v Azure) | Microsoft Docs'
description: Jak zjistit postojích pomocí Text Analytics REST API v kognitivní služby společnosti Microsoft na platformě Azure v tomto kurzu návod.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 12/11/2017
ms.author: heidist
ms.openlocfilehash: 7ffd8bbe47409b459fdd308cd8d670d32f56649b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342649"
---
# <a name="how-to-detect-sentiment-in-text-analytics"></a>K zjištění postojích v Analýza textu

[Postojích Analysis API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) vyhodnotí zadávání textu a vrátí postojích skóre pro každý dokument rozsahu od 0 (záporné) na hodnotu 1 (pozitivní).

Tato možnost je užitečná pro zjišťování kladné a záporné postojích v sociálních sítích, zákaznické recenze a diskusní fóra. Obsah je zadaný; modely a Cvičná data jsou poskytovaný službou.

V současné době podporuje postojích Analysis angličtina, němčina, španělština a francouzštinu. Další jazyky jsou ve verzi preview. Další informace najdete v tématu [podporované jazyky](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Koncepty

Analýza textu strojového učení klasifikační algoritmus a používá ke generování postojích skóre mezi 0 a 1. Skóre blíže 1 znamenat kladné postojích, zatímco skóre blíže 0 označuje záporné postojích. Model je pretrained rozsáhlé subjektu, textu postojích přidružení. V současné době není možné poskytnout vlastní Cvičná data. Model používá kombinaci technik při analýze textu, včetně text zpracování, analýzu část řeči, word umístění a přidružení aplikace word. Další informace o algoritmus najdete v tématu [představení Analýza textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Provedení analýzy postojích na celý dokument a extrahování postojích pro konkrétní entitu v textu. V praxi existuje určitá tendence, že pro vyhodnocování přesnost ke zlepšení při dokumenty obsahují jednu nebo dvě věty spíše než velkou část textu. Během fáze hodnocení objektivity modelu určuje, zda je cílem dokumentu jako celku, nebo obsahuje postojích. A dokumentu, který není většinou cíle nemá průběh k postojích detekce fráze, což vede k.50 skóre, s žádné další zpracování. V další fázi pro dokumenty, budete pokračovat v kanálu, generuje skóre nad nebo pod.50, v závislosti na stupeň postojích zjistil v dokumentu.

## <a name="preparation"></a>Příprava

Analýza postojích vytvoří vyšší výsledek kvality mající menší bloky dat textu při práci. Toto je opačné extrakci klíče frázi, která provádí lépe větší bloky textu. K dosažení nejlepších výsledků z obou operace, zvažte změnu struktury vstupy odpovídajícím způsobem.

Dokumenty JSON musí mít v tomto formátu: id, text, jazyk

Velikost dokumentu musí být v části 5 000 znaků na jednu dokumentu, a může mít až 1 000 položek (ID) na kolekci. Kolekce je odeslán v textu požadavku. Následuje příklad obsahu, který může odeslat pro analýzu postojích.

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

+ Vytvoření **POST** požadavku. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [postojích Analysis API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Nastavte koncový bod HTTP pro extrakci klíče frázi. Musí zahrnovat `/sentiment` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Nastavte hlavičku požadavku zahrnout přístupový klíč pro operace Analýza textu. Další informace najdete v tématu [jak najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V těle žádosti zadejte kolekci dokumentů JSON, který jste připravili pro tuto analýzu.

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **rozhraní API testování konzoly** v [dokumentace](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) a struktury požadavek POST do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat požadavek

Po přijetí žádosti o provedení analýzy. Služba přijímá až 100 požadavky za minutu. Každý požadavek může být maximálně 1 MB.

Odvolat, zda je služba bezstavové. Žádná data se ukládají ve vašem účtu. Výsledky jsou vráceny okamžitě v odpovědi.


## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Analyzátor postojích klasifikuje text jako převážně kladné a záporné, přiřadí skóre v rozsahu od 0 do 1. Hodnoty blízko 0,5 jsou neutrálních nebo neurčitou. Skóre 0,5 označuje neutrality. Pokud řetězec nemůže být analyzován z hlediska postojích nebo nemá žádné postojích, skóre je vždy 0,5 přesně. Pokud předáte ve španělské řetězec s kódem anglickém jazyce, je skóre například 0,5.

Výstup se vrátí okamžitě. Stream výsledky do aplikace, která přijímá JSON nebo uložte si výstup do souboru na lokálním systému a následně ho naimportovat do aplikace, která umožňuje řazení, vyhledávání a manipulovat s daty.

Následující příklad ukazuje odpověď pro kolekce dokumentů v tomto článku.

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>Souhrn

V tomto článku jste se dozvěděli, koncepty a pracovní postup pro analýzu postojích pomocí Analýza textu v kognitivní služby. Shrnutí:

+ [Analýza postojích rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují id, text a jazyk kódu.
+ Je požadavek POST `/sentiment` koncový bod, pomocí přizpůsobené [přístup klíč a koncový bod](text-analytics-how-to-access-key.md) , je platný pro vaše předplatné.
+ Odpověď výstupu, který se skládá z postojích skóre pro každý dokument ID, Streamovat k jakékoli aplikaci, která přijímá formát JSON, včetně aplikace Excel a Power BI a další.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)</br>
 [Stránka produktu Analýza textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozbalte klíče fráze](text-analytics-how-to-keyword-extraction.md)
