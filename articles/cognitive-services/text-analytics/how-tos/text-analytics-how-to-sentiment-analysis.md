---
title: Analýza subjektivního hodnocení s postupy v REST API pro analýzu textu (Microsoft Cognitive Services v Azure) | Dokumentace Microsoftu
description: Jak rozpoznávání mínění pomocí REST API pro analýzu textu ve službě Microsoft Cognitive Services v Azure v tomto kurzu návodu.
services: cognitive-services
author: HeidiSteen
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 4c5b55a5e11bddd6eac8155c2e49ccbd80d3164e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856372"
---
# <a name="example-how-to-detect-sentiment-in-text-analytics"></a>Příklad: Jak rozpoznávání mínění v rozhraní Text Analytics

[Rozhraní API pro analýzu mínění](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) vyhodnocuje textový vstup a vrací skóre mínění pro každý dokument v rozsahu 0 (negativní) až 1 (pozitivní).

Tato možnost je užitečná pro rozpoznání pozitivního a negativního mínění v sociálních médiích, zákaznických recenzích a diskuzních fórech. Obsah pochází od vás, modely a trénovací data poskytuje služba.

Pro analýzu mínění se momentálně podporuje angličtina, němčina, španělština a francouzština. Další jazyky jsou ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Rozhraní text Analytics také poskytuje Dockeru založených na Linuxu image kontejneru pro analýzu mínění, což vám umožní [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="concepts"></a>Koncepty

Analýza textu vygeneruje pomocí algoritmu pro klasifikaci strojového učení skóre mínění v rozsahu 0 až 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Model je předem natrénovaný pomocí velkého počtu textů s přidruženími mínění. V současné době není možné dodat vlastní trénovací data. Při analýze textu model používá různé postupy, například zpracování textu, analýzu částí řeči, rozmístění slov a asociace slov. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analýza mínění se provádí pro celý dokument, ne jako extrakce mínění u konkrétní entity v textu. Dá se říct, že v praxi bývá skóre přesnější, když dokumenty obsahují jednu nebo dvě věty, a ne rozsáhlý blok textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je převážně objektivní, nepřechází do fáze rozpoznávání mínění, dále se nezpracovává a jeho výsledné skóre bude 0,5. Pro dokumenty pokračující do další fáze se vygeneruje skóre vyšší nebo nižší než 0,5 v závislosti na míře subjektivního mínění rozpoznané v daném dokumentu.

## <a name="preparation"></a>Příprava

Výsledky analýzy mínění jsou kvalitnější pro menší bloky textu. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Musíte mít dokumenty JSON v tomto formátu: ID, text, kód jazyka.

Dokument nesmí obsahovat více než 5 000 znaků a v každé kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následuje příklad obsahu, který můžete odeslat pro analýzu mínění.

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

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro analýzu mínění](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Nastavení koncového bodu HTTP pro analýzu mínění, pomocí prostředek pro analýzu textu v Azure nebo instance [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí obsahovat prostředek `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Služba přijme maximálně 100 žádostí za minutu. Každá žádost může mít maximální velikost 1 MB.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Analýza mínění klasifikuje text jako převážně pozitivní nebo negativní a přiřadí mu skóre v rozsahu 0 až 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud u řetězce nelze analyzovat mínění nebo v něm žádné mínění není, bude skóre vždycky přesně 0,5. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Následující příklad ukazuje odpověď pro kolekci dokumentů v tomto článku.

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

V tomto článku jste se seznámili s koncepty a pracovním postupem analýzy mínění pomocí funkce Analýza textu ve službě Cognitive Services. Souhrn:

+ [Rozhraní API pro analýzu mínění](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON obsahují v textu žádosti ID, text a kód jazyka.
+ Žádost POST je určená pro koncový bod `/sentiment` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), který je platný pro dané předplatné.
+ Výstup odpovědi, který tvoří skóre mínění pro jednotlivá ID dokumentu, lze streamovat do libovolné aplikace, která přijímá JSON, včetně například Excelu a Power BI.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)
