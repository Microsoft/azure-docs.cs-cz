---
title: Podpora jazyků – Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'Seznam podporovaných rozhraní Text Analytics API přirozeného jazyka. Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází a rozpoznání jazyka.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 09/25/2018
ms.author: ashmaka
ms.openlocfilehash: e9f466ac6bce98a6a9f2d79a443c9602ca40bb26
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435447"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Podpora jazyka a oblasti pro rozhraní Text Analytics API

Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází a rozpoznání jazyka.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní Text Analytics API můžete zjišťovat až 120 jazyků. Rozpoznávání jazyka vrátí "skript" jazyk. Například pro frázi "Mám pes" vrátí `en` místo `en-US`. Pouze zvláštním případem je čínština, kde funkci zjišťování jazyka vrátí `zh_CHS` nebo `zh_CHT` pokud ji určit skript zadaného textu k dispozici. V situacích, kdy nelze identifikovat specifického skriptu pro čínské dokument, ho jednoduše vrátí `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Analýza mínění, extrakci klíčových frází a propojování entit

Seznam podporovaných jazyků je pro analýzu mínění, extrakci klíčových frází a propojování entit, zvažte, jak analyzátory jsou kontrast pro přizpůsobení jazykových pravidel další jazyky.

## <a name="language-list-and-status"></a>Seznam jazyků a stav

Podpora jazyků je zpočátku nasazeny ve verzi preview, přechod na všeobecně dostupná (GA) stav, nezávisle na sobě a celkové služby pro analýzu textu. Je možné pro jazyky, které zůstává ve verzi preview, i když přechody rozhraní Text Analytics API na obecně k dispozici.

| Jazyk    | Kód jazyka | Mínění | Klíčové fráze | Entity Linking |   Poznámky  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| dánština      | `da`          | ✔ \*     | ✔           |             |     |
| Holandština       | `nl`          | ✔ \*     | ✔          |             |     |
| Angličtina     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finština     | `fi`          | ✔ \*     | ✔           |             |     |
| Francouzština      | `fr`          | ✔        | ✔           |             |     |
| Němčina      | `de`          | ✔ \*     | ✔           |            |     |
| Řečtina       | `el`          | ✔ \*     |             |            |     |
| italština     | `it`          | ✔ \*     | ✔           |             |     |
| Japonština    | `ja`          |          | ✔           |            |     |
| Korejština      | `ko`          |          | ✔           |            |     |
| Norština (Bokmal) | `no`          | ✔ \*     |  ✔          |             |     |
| polština      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugalština (Portugalsko) | `pt-PT`| ✔        |  ✔          |       |`pt` také přijetí|
| Portugalština (Brazílie)   | `pt-BR`|          |  ✔   |         |     |
| ruština     | `ru`          | ✔ \*     | ✔           |             |     |
| Španělština     | `es`          | ✔        | ✔           |     |     |
| švédština     | `sv`          | ✔ \*     | ✔           |             |     |
| turečtina     | `tr`          | ✔ \*     |             |             |     |

\* Určuje jazykovou podporu ve verzi preview

## <a name="see-also"></a>Další informace najdete v tématech

[Cognitive Services dokumentaci stránky](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
