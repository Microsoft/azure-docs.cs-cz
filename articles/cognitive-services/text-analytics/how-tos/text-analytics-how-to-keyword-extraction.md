---
title: Extrakce klíčových frází pomocí REST API pro analýzu textu | Dokumentace Microsoftu
description: Postup extrakce klíčových frází pomocí rozhraní REST API ze služeb Azure Cognitive services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: bbf72847dd9d9a29bf1f2fa0574b83194d07a5c6
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245605"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Příklad: Postup extrakce klíčových frází pomocí analýzy textu

[Rozhraní API pro extrakci klíčových frází](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) vyhodnotí nestrukturovaný text a pro každý dokument JSON vrátí seznam klíčových frází. 

Tato funkce je užitečná v případě, kdy potřebujete rychle identifikovat hlavní body v kolekci dokumentů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný), vrátí služba hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

V současné době se extrakce klíčových frází podporuje v angličtině, němčině, španělštině a japonštině. Další jazyky jsou ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Rozhraní text Analytics také poskytuje Dockeru založených na Linuxu image kontejneru pro extrakci klíčových frází, tak, aby se [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="preparation"></a>Příprava

Extrakce klíčových frází funguje nejlépe, když je jí větší množství textu pro práci na. Toto je opačné z analýzy subjektivního hodnocení, které vrací lepší výsledky na menší množství textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Musíte mít dokumenty JSON v tomto formátu: ID, text, kód jazyka.

Dokument nesmí obsahovat více než 5 000 znaků a v každé kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující příklad ilustruje obsah, který byste mohli odeslat za účelem extrakce klíčových frází.

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

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro klíčové fráze](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Nastavení koncového bodu HTTP pro extrakci klíčových frází pomocí prostředek pro analýzu textu v Azure nebo instance [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí obsahovat prostředek `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu žádosti zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Služba přijme maximálně 100 žádostí za minutu. Každá žádost může mít maximální velikost 1 MB.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Zde je uveden příklad výstupu pro extrakci klíčových frází:

```json
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

Jak jsme uvedli, analyzátor vyhledá a zahodí nepodstatná slova a zachová jednotlivé termíny nebo fráze, které se jeví jako podmět nebo předmět věty. 

## <a name="summary"></a>Souhrn

V tomto článku jste se seznámili s koncepty a pracovním postupem extrakce klíčových frází pomocí funkce Analýza textu ve službě Cognitive Services. Souhrn:

+ [Rozhraní API pro extrakci klíčových frází](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON obsahují v textu žádosti ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/keyphrases` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), který je platný pro dané předplatné.
+ Výstup odpovědi, který je tvořen klíčovými slovy a frázemi pro jednotlivá ID dokumentů, lze streamovat do libovolné aplikace, která podporuje JSON, včetně například Excelu a Power BI.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní API pro analýzu textu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
