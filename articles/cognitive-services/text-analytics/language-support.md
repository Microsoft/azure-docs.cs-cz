---
title: Jazyková podpora – rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: 'Seznam přirozených jazyků podporovaných rozhraním API pro analýzu textu. Tento článek vysvětluje, které jazyky jsou podporovány pro každou operaci: analýza mínění, extrakce klíčových frází, detekce jazyka a rozpoznávání entit.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219277"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Jazyková a regionální podpora rozhraní API pro analýzu textu

Tento článek vysvětluje, které jazyky jsou podporovány pro každou operaci: analýza mínění, extrakce klíčových frází, detekce jazyka a rozpoznávání pojmenovaných entit.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní API pro analýzu textu dokáže rozpoznat širokou škálu jazyků, variant, dialektů a některých regionálních/kulturních jazyků.  Funkce Rozpoznávání jazyka vrátí "skript" jazyka. Například pro frázi "Mám psa" se `en` vrátí `en-US`místo . Jediným zvláštním případem je čínština, kde `zh_CHS` `zh_CHT` se vrátí funkce detekce jazyka nebo pokud může určit skript daný text. V situacích, kdy nelze pro čínský dokument identifikovat `zh`konkrétní skript, se vrátí jednoduše .

Nezveřejňujeme přesný seznam jazyků pro tuto funkci, ale dokáže detekovat širokou škálu jazyků, variant, dialektů a některých regionálních/kulturních jazyků. 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit detekci jazyka a zjistit, zda vrátí kód. Odpověď pro jazyky, které `unknown`nelze zjistit, je .

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analýza mínění, extrakce klíčových frází a rozpoznávání pojmenovaných entit

Pro analýzu mínění, extrakci klíčových frází a rozpoznávání entit je seznam podporovaných jazyků selektivnější, protože analyzátory jsou upřesněny tak, aby vyhovovaly jazykovým pravidlům dalších jazyků. V pojmenované matné entity v2 je podpora pro úplnou sadu [typů entit](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aktuálně omezena na následující jazyky: 
* Angličtina
* Čínština (zjednodušená)
* Francouzština
* Němčina
* Španělština

Pro ostatní `Organization` jazyky jsou vráceny pouze a pojmenované entity. `Person` `Location`

## <a name="language-list-and-status"></a>Jazykový seznam a stav

Jazyková podpora je zpočátku zavedena ve verzi preview, což znamená, že se jedná o obecně dostupný stav (GA), nezávisle na sobě a na službě Text Analytics celkově. Jazyky mohou zůstat ve verzi Preview, i když rozhraní API pro analýzu textu přechází na obecně dostupné.

> [!NOTE]
> Podrobná jazyková podpora pro verzi Public Preview (Rozpoznávání pojmenovaných entit) v3 naleznete [v tématu Pojmenované typy entit](named-entity-types.md).

| Jazyk              | Kód jazyka | Mínění | Klíčové fráze | Rozpoznávání pojmenovaných entit | Propojení entit |       Poznámky        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabština                |     `ar`      |           |             |           ✔\*           |                |                    |
| Čeština                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Čínština (zjednodušená)    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`také přijal                   |
| Čínština (tradiční)   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Dánština                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Nizozemština                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Angličtina               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finština               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francouzština                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Němčina                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Řečtina                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Maďarština             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italština               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japonština              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Korejština                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norština (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`také přijal                   |
| Polština                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| portugalština (Portugalsko) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`také přijal |
| Portugalština (Brazílie)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Ruština               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Španělština               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Švédština               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turečtina               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Jazyková podpora je ve verzi Preview

\** K dispozici také v [analýze mínění v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) a/nebo [rozpoznávání pojmenovaných entit v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) veřejných náhledů.

## <a name="see-also"></a>Viz také

[Stránka dokumentace služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
