---
title: Podpora jazyků – Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'Seznam podporovaných rozhraní Text Analytics API přirozeného jazyka. Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání entit.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417312"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Podpora jazyka a oblasti pro rozhraní Text Analytics API

Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání pojmenovaných entit.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní Text Analytics API můžete zjišťovat široké škály jazyků, proměnných typu variant, dialekty a některé jazykové verze nebo místní jazyky.  Rozpoznávání jazyka vrátí "skript" jazyk. Například pro frázi "Mám pes" vrátí `en` místo `en-US`. Pouze zvláštním případem je čínština, kde funkci zjišťování jazyka vrátí `zh_CHS` nebo `zh_CHT` pokud ji určit skript zadaného textu k dispozici. V situacích, kdy nelze identifikovat specifického skriptu pro čínské dokument, ho jednoduše vrátí `zh`.

Nepublikujeme přesný seznam jazyků pro tuto funkci, ale může zjistit široké škály jazyků, proměnných typu variant, dialekty a některé jazykové verze nebo místní jazyky. 

Pokud máte obsah vyjádřené v méně často používané jazyk, můžete zkusit rozpoznávání jazyka, které chcete zobrazit, pokud vrací kód. Odpověď pro jazyky, které nelze zjistit `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analýza mínění, extrakci klíčových frází a rozpoznávání pojmenovaných entit

Seznam podporovaných jazyků je pro analýzu mínění, extrakci klíčových frází a rozpoznávání entit, zvažte, jak analyzátory jsou kontrast pro přizpůsobení jazykových pravidel další jazyky.

## <a name="language-list-and-status"></a>Seznam jazyků a stav

Podpora jazyků je zpočátku nasazeny ve verzi preview, přechod na všeobecně dostupná (GA) stav, nezávisle na sobě a celkové služby pro analýzu textu. Je možné pro jazyky, které zůstává ve verzi preview, i když přechody rozhraní Text Analytics API na obecně k dispozici.

| Jazyk    | Kód jazyka | Mínění | Klíčové fráze | Rozpoznávání pojmenovaných entit |   Poznámky  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| arabština      | `ar`          |           |             | ✔ \*                     | |
| Čeština       | `cs`          |           |             | ✔ \*                     | |
| Čínština (zjednodušená) | `zh-CN`|           |             | ✔ \*        |    |
| dánština      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Holandština       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Angličtina     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finština     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francouzština      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Němčina      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Řečtina       | `el`          | ✔ \*     |             |            |     |
| Maďarština   | `hu`          |           |             |  ✔ \*          |     | 
| italština     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japonština    | `ja`          |          | ✔           |  ✔ \*          |     |
| Korejština      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norština (Bokmal) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| polština      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugalština (Portugalsko) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` také přijetí|
| Portugalština (Brazílie)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| ruština     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Španělština     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| švédština     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| turečtina     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Podpora jazyků je ve verzi preview

\*\* S názvem rozpoznávání entit a [propojování entit](how-tos/text-analytics-how-to-entity-linking.md) jsou k dispozici pro tento jazyk.    

## <a name="see-also"></a>Další informace najdete v tématech

[Cognitive Services dokumentaci stránky](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
