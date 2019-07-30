---
title: Extrakce klíčových frází pomocí Analýza textu REST API
titleSuffix: Azure Cognitive Services
description: Postup extrakce klíčových frází pomocí Analýza textu REST API z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: dd3f0c5b82c1898d6e4bbe564556ee26e872dc94
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619676"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Příklad: Extrakce klíčových frází s využitím Analýzy textu

[Rozhraní API pro extrakci klíčových frází](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) vyhodnotí nestrukturovaný text a pro každý dokument JSON vrátí seznam klíčových frází.

Tato funkce je užitečná v případě, kdy potřebujete rychle identifikovat hlavní body v kolekci dokumentů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný), vrátí služba hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Analýza textu taky poskytuje pro extrakci klíčových frází image kontejneru Docker pro Linux, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) blízko k datům.

## <a name="preparation"></a>Příprava

Extrakce klíčových frází funguje nejlépe, když jim dáte větší množství textu, na kterém chcete pracovat. To je opakem analýzy mínění, která je vhodnější pro menší množství textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk

Velikost dokumentu musí být 5 120 nebo méně znaků v jednom dokumentu a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující příklad ilustruje obsah, který byste mohli odeslat za účelem extrakce klíčových frází.

```json
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

## <a name="step-1-structure-the-request"></a>Krok 1: Strukturování žádosti

Informace o definici požadavku naleznete v tématu [způsob volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro klíčové fráze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Nastavte koncový bod HTTP pro extrakci klíčových frází pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Musí zahrnovat `/keyPhrases` prostředek: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu nebo za sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Příklad výstupu pro extrakci klíčových frází je uveden zde:

```json
    {
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
        ],
        "errors": []
    }
```

Jak je uvedeno, analyzátor vyhledá a zahodí nepostradatelná slova a udržuje jednoduché termíny nebo fráze, které se jeví jako předmět nebo předmět věty.

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro extrakci klíčových frází pomocí Analýza textu v Cognitive Services. Souhrn:

+ [Rozhraní API pro extrakci klíčových frází](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/keyphrases` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), které jsou platné pro dané předplatné.
+ Výstup odpovědi, který se skládá z klíčových slov a frází pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON, včetně systém Microsoft Office Excel a Power BI, pro pojmenování.

## <a name="see-also"></a>Viz také:

 [Přehled Analýza textu](../overview.md) Nejčastější dotazy – Nejčastější [dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní API pro analýzu textu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
