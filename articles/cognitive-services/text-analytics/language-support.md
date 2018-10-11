---
title: Podpora jazyků – Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'Seznam podporovaných rozhraní Text Analytics API přirozeného jazyka. Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání entit.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 63c8ebe8eda049a770cecfd445757e4b5a2de1ea
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067037"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Podpora jazyka a oblasti pro rozhraní Text Analytics API

Tento článek vysvětluje, jaké jazyky jsou podporovány pro každou operaci: analýzu mínění, extrakci klíčových frází a rozpoznání jazyka.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní Text Analytics API můžete zjišťovat až 120 jazyků. Rozpoznávání jazyka vrátí "skript" jazyk. Například pro frázi "Mám pes" vrátí `en` místo `en-US`. Pouze zvláštním případem je čínština, kde funkci zjišťování jazyka vrátí `zh_CHS` nebo `zh_CHT` pokud ji určit skript zadaného textu k dispozici. V situacích, kdy nelze identifikovat specifického skriptu pro čínské dokument, ho jednoduše vrátí `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Analýza mínění, extrakci klíčových frází a rozpoznávání entit

Seznam podporovaných jazyků je pro analýzu mínění, extrakci klíčových frází a rozpoznávání entit, zvažte, jak analyzátory jsou kontrast pro přizpůsobení jazykových pravidel další jazyky.

## <a name="language-list-and-status"></a>Seznam jazyků a stav

Podpora jazyků je zpočátku nasazeny ve verzi preview, přechod na všeobecně dostupná (GA) stav, nezávisle na sobě a celkové služby pro analýzu textu. Je možné pro jazyky, které zůstává ve verzi preview, i když přechody rozhraní Text Analytics API na obecně k dispozici.

| Jazyk    | Kód jazyka | Mínění | Klíčové fráze | Rozpoznávání entit |   Poznámky  |
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
| Španělština     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| švédština     | `sv`          | ✔ \*     | ✔           |             |     |
| turečtina     | `tr`          | ✔ \*     |             |             |  |

\* Určuje jazykovou podporu ve verzi preview

\*\* Extrakce entity pro španělštinu je k dispozici pouze [(verze 2.1 – ve verzi preview)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Další informace najdete v tématech

[Cognitive Services dokumentaci stránky](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
