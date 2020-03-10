---
title: Jazyková podpora – rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: 'Seznam přirozených jazyků, které rozhraní API pro analýzu textu podporuje. V tomto článku se dozvíte, které jazyky jsou pro každou operaci podporované: analýza mínění, extrakce klíčových frází, rozpoznávání jazyka a rozpoznávání entit.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942659"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Podpora jazyků a oblastí pro rozhraní API pro analýzu textu

V tomto článku se dozvíte, které jazyky jsou pro každou operaci podporované: analýza mínění, extrakce klíčových frází, detekce jazyka a rozpoznávání pojmenovaných entit.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní API pro analýzu textu může detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků.  Rozpoznávání jazyka vrátí "skript" jazyka. Například pro frázi "I mám pes" se vrátí `en` místo `en-US`. Jediným speciálním případem je čínština, kde funkce pro detekci jazyka vrátí `zh_CHS` nebo `zh_CHT`, pokud může určit skript s uvedeným textem. V situacích, kdy konkrétní skript nelze identifikovat pro čínský dokument, vrátí `zh`jednoduše.

Nezveřejňujeme vám přesný seznam jazyků pro tuto funkci, ale dokáže detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků. 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které se nedají detekovat, je `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analýza mínění, Extrakce klíčových frází a rozpoznávání pojmenovaných entit

V případě analýzy mínění, extrakce klíčových frází a rozpoznávání entit je seznam podporovaných jazyků pokročilejší, protože analyzátory jsou upřesněny tak, aby vyhovovaly jazykovým pravidlům dalších jazyků. V případě rozpoznávání pojmenovaných entit v2 je podpora pro úplnou sadu [typů entit](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aktuálně omezena na následující jazyky: 
* Angličtina
* Čínština (zjednodušená)
* Francouzština
* Němčina
* Španělština

Pro ostatní jazyky jsou vraceny pouze `Person`, `Location` a `Organization` pojmenované entity.

## <a name="language-list-and-status"></a>Seznam a stav jazyka

Jazyková podpora je zpočátku zavedená ve verzi Preview a je nezávislá na stavu (GA), nezávisle na sobě navzájem a na celkové dostupnosti služby Analýza textu. Je možné, že jazyky zůstanou ve verzi Preview, i když rozhraní API pro analýzu textu přechody na všeobecně dostupné.

> [!NOTE]
> Podrobnou jazykovou podporu pro funkci rozpoznávání pojmenovaných entit (NER) v3 Public Preview najdete v tématu [typy pojmenovaných entit](named-entity-types.md).

| Jazyk              | Kód jazyka | Mínění | Klíčové fráze | Rozpoznávání pojmenovaných entit | Propojení entit |       Poznámky:        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabština                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Čeština                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Čínština (zjednodušená)    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                | `zh` také přijaty                   |
| Čínština (tradiční)   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| dánština                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Nizozemština                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Angličtina               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Finština               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Francouzština                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Němčina                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Řečtina                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Maďarština             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italština               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japonština              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Korejština                |     `ko`      |   ✔ \*\*  |      ✔      |           ✔ \*           |                |                    |
| Norština (Bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                | `nb` také přijaty                   |
| Polština                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Portugalština (Portugalsko) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` také přijaty |
| Portugalština (Brazílie)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Ruština               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Španělština               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| švédština               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turečtina               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

Podpora jazyka \* je ve verzi Preview.

\** k dispozici také v [Analýza mínění V3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) a/nebo ve verzi Public Preview pro [rozpoznávání entit V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) .

## <a name="see-also"></a>Viz také

[Stránka dokumentace Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
