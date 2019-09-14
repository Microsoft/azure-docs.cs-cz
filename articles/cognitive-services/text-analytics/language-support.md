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
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: e14cb56db6e45fe86ecd5e73766c47d29dd23fa1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984231"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Podpora jazyků a oblastí pro rozhraní API pro analýzu textu

V tomto článku se dozvíte, které jazyky jsou pro každou operaci podporované: analýza mínění, extrakce klíčových frází, detekce jazyka a rozpoznávání pojmenovaných entit.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní API pro analýzu textu může detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků.  Rozpoznávání jazyka vrátí "skript" jazyka. Například pro frázi "Mám k dispozici pes" se vrátí `en` `en-US`místo. Jediným zvláštním případem je čínština, kde se vrátí `zh_CHS` funkce pro detekci jazyka, nebo `zh_CHT` Pokud se může určit skript, který daný text zadal. V situacích, kdy se konkrétní skript nedá identifikovat pro čínský dokument, vrátí se jednoduše `zh`.

Nezveřejňujeme vám přesný seznam jazyků pro tuto funkci, ale dokáže detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků. 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které nelze zjistit, je `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analýza mínění, Extrakce klíčových frází a rozpoznávání pojmenovaných entit

V případě analýzy mínění, extrakce klíčových frází a rozpoznávání entit je seznam podporovaných jazyků pokročilejší, protože analyzátory jsou upřesněny tak, aby vyhovovaly jazykovým pravidlům dalších jazyků. Podpora pro úplnou sadu [typů entit](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) je aktuálně omezená na následující jazyky: 
* Angličtina
* Čínština (zjednodušená)
* Francouzština
* Němčina
* Španělština

Pro ostatní jazyky jsou `Organization` vraceny pouze entity s názvem a.`Location` `Person`

## <a name="language-list-and-status"></a>Seznam a stav jazyka

Jazyková podpora je zpočátku zavedená ve verzi Preview a je nezávislá na stavu (GA), nezávisle na sobě navzájem a na celkové dostupnosti služby Analýza textu. Je možné, že jazyky zůstanou ve verzi Preview, i když rozhraní API pro analýzu textu přechody na všeobecně dostupné.

| Jazyk    | Kód jazyka | Mínění | Klíčové fráze | Rozpoznávání pojmenovaných entit |   Poznámky  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| arabština      | `ar`          |           |             | ✔ \*                     | |
| Čeština       | `cs`          |           |             | ✔ \*                     | |
| Čínština (zjednodušená) | `zh-CN`| ✔ \***     |             | ✔         |    |
| dánština      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Holandština       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Angličtina     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finština     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francouzština      | `fr`          | ✔        | ✔           |  ✔            |     |
| Němčina      | `de`          | ✔ \*     | ✔           |  ✔           |     |
| Řečtina       | `el`          | ✔ \*     |             |            |     |
| Maďarština   | `hu`          |           |             |  ✔ \*          |     | 
| italština     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japonština    | `ja`          | ✔ \***         | ✔           |  ✔ \*          |     |
| Korejština      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norština (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| polština      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugalština (Portugalsko) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`také přijato|
| Portugalština (Brazílie)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| ruština     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Španělština     | `es`          | ✔        | ✔           |   ✔ \*\*      |     | 
| švédština     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| turečtina     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\*Podpora jazyků je ve verzi Preview.

\*\*[Rozpoznávání pojmenovaných entit](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) a [propojení entit](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) jsou k dispozici pro tento jazyk.  

\** * K dispozici v [Analýza mínění V3 Public Preview](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview)

## <a name="see-also"></a>Viz také:

[Stránka dokumentace Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
