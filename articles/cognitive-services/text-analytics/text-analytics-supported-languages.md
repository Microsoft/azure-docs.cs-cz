---
title: Podporované jazyky v rozhraní API Analytics Text - kognitivní služeb Azure | Microsoft Docs
description: Seznam všeobecně dostupná a jazyk preview podporu pro operace rozhraní API Analytics Text. Platí pro analýzy postojích, extrakce klíče frázi a zjišťování jazyka.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343632"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Podporované jazyky v rozhraní API Analýza textu

Tento článek vysvětluje podporované jazyky pro každou operaci: postojích analýzy, extrakce klíče frázi a zjišťování jazyka.

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozhraní API Analytics Text může rozpoznat až 120 různé jazyky. Zjišťování jazyka vrátí "skript" jazyka. Například pro frázi "Je nutné PSA" vrátí `en` místo `en-US`. Pouze zvláštním případem je čínština, kde vrátí funkce zjišťování jazyk `zh_CHS` nebo `zh_CHT` pokud ji můžete určit skript zadaný text šablony. V situacích, kde nelze identifikovat specifického skriptu pro čínštinu dokument, vrátí jednoduše `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Analýza postojích, extrakce klíče frázi a Entity propojení

Pro analýzu postojích, extrakce klíče frázi a propojování entity je více selektivní jsou přesnějších zohlednit lingvistické pravidla další jazyky analyzátory seznam podporovaných jazyků.

## <a name="language-list-and-status"></a>Seznam jazyků a stav

Jazyková podpora je původně nasazuje ve verzi preview, přechod na všeobecně dostupná (GA) stav, nezávisle na sobě navzájem a celkové Analýza textu služby. Je možné pro jazyky, zůstanou ve verzi preview, i když Text Analytics API přechodů k všeobecně dostupná.

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
| Norština (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| polština      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugalština (Portugalsko) | `pt-PT`| ✔        |  ✔          |       |`pt` také přijata.|
| Portugalština (Brazílie)   | `pt-BR`|          |  ✔   |         |     |
| ruština     | `ru`          | ✔ \*     | ✔           |             |     |
| Španělština     | `es`          | ✔        | ✔           |     |     |
| švédština     | `sv`          | ✔ \*     | ✔           |             |     |
| turečtina     | `tr`          | ✔ \*     |             |             |     |

\* Určuje jazyk podpora ve verzi preview

## <a name="see-also"></a>Další informace najdete v tématech

[Kognitivní stránky dokumentace služby](https://docs.microsoft.com/azure/cognitive-services/)   
[Kognitivní stránka služby produktu](https://azure.microsoft.com/services/cognitive-services/)
