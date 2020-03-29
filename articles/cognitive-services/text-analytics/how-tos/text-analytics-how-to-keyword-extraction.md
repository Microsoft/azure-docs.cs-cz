---
title: Extrakce klíčových frází pomocí rozhraní REST API analýzy textu
titleSuffix: Azure Cognitive Services
description: Jak extrahovat klíčové fráze pomocí rozhraní API REST analýzy textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429023"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Příklad: Jak extrahovat klíčové fráze pomocí analýzy textu

[Rozhraní API pro extrakci klíčových frází](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) vyhodnotí nestrukturovaný text a pro každý dokument JSON vrátí seznam klíčových frází.

Tato funkce je užitečná v případě, kdy potřebujete rychle identifikovat hlavní body v kolekci dokumentů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný), vrátí služba hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Text Analytics také poskytuje linuxovou image kontejneru Dockerpro extrakci klíčových frází, takže můžete [nainstalovat a spustit kontejner Text Analytics](text-analytics-how-to-install-containers.md) v blízkosti vašich dat.

## <a name="preparation"></a>Příprava

Extrakce klíčových frází funguje nejlépe, když mu dáte větší množství textu, na které se dá pracovat. To je opak emblémů, která lépe funguje u menších částek textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Musíte mít dokumenty JSON v tomto formátu: ID, text, jazyk

Velikost dokumentu musí být 5 120 nebo méně znaků na dokument a můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti. Následující příklad ilustruje obsah, který byste mohli odeslat za účelem extrakce klíčových frází.

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

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Informace o definici požadavku naleznete [v tématu Jak volat rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tento požadavek: [Rozhraní API klíčových frází](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Nastavte koncový bod HTTP pro extrakci klíčových frází pomocí prostředku Analýzy textu v Azure nebo pomocí [kontejneru instanci Textové analýzy](text-analytics-how-to-install-containers.md). Musíte zahrnout `/text/analytics/v2.1/keyPhrases` do adresy URL. Například: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Nastavte hlavičku požadavku tak, aby zahrnovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace Analýzy textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu nebo za sekundu, naleznete v části [Omezení dat](../overview.md#data-limits) v přehledu .

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi. Pořadí vrácených klíčových frází je určeno interně modelem.

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

Jak již bylo uvedeno, analyzátor vyhledá a zahodí nepodstatná slova a zachová jednotlivé termíny nebo fráze, které se zdají být předmětem nebo předmětem věty.

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro extrakci klíčových frází pomocí analýzy textu v kognitivních službách. Souhrn:

+ [Rozhraní API pro extrakci klíčových frází](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku obsahují ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/keyphrases` a používá individuální [přístupový klíč a koncový bod](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), který je platný pro dané předplatné.
+ Výstup odpovědi, který se skládá z klíčových slov a frází pro každé ID dokumentu, lze streamovat do libovolné aplikace, která přijímá JSON, včetně Microsoft Office Excel a Power BI, abychom jmenovali jen některé.

## <a name="see-also"></a>Viz také

 [Přehled analýzy textu](../overview.md) [Nejčastější dotazy (NEJČASTĚJŠÍ DOTAZY)](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
