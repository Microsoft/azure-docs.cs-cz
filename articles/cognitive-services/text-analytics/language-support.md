---
title: Jazyková podpora – rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, které rozhraní API pro analýzu textu podporuje. V tomto článku se dozvíte, které jazyky jsou pro každou operaci podporované.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2020
ms.author: aahi
ms.openlocfilehash: 185a6a26350f7803d109e26d8985510380b8cc16
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056587"
---
# <a name="text-analytics-api-v3-language-support"></a>Podpora jazyka rozhraní API pro analýzu textu V3 

> [!IMPORTANT]
> Verze 3. x rozhraní API pro analýzu textu není v tuto chvíli k dispozici v následujících oblastech: Střed Indie, Spojené arabské emiráty sever, Čína – sever 2, Čína – východ.


#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment-analysis)

| Jazyk              | Kód jazyka | Podpora v2 | Podpora V3 | Spouští se verze modelu V3: |              Poznámky |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Čínština (zjednodušená)    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` také přijato |
| Čínština (tradiční)   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dánština               |     `da`      |     ✓      |            |                            |                    |
| Nizozemština                 |     `nl`      |     ✓      |            |                            |                    |
| Angličtina               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finština               |     `fi`      |     ✓      |            |                            |                    |
| Francouzština                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Němčina                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Řečtina                 |     `el`      |     ✓      |            |                            |                    |
| Italština               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonština              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Korejština                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Polština                |     `pl`      |     ✓      |            |                            |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` také přijato |
| Ruština               |     `ru`      |     ✓      |            |                            |                    |
| Španělština               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Švédština               |     `sv`      |     ✓      |            |                            |                    |
| Turečtina               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Dolování stanovisek (verze 3.1 – Preview)

| Jazyk              | Kód jazyka | Od verze V3 model: |              Poznámky |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angličtina               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Rozpoznávání pojmenovaných entit (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER V3 aktuálně podporuje pouze anglické a španělské jazyky. Pokud voláte NER v3 s jiným jazykem, rozhraní API vrátí výsledky v 2.1 za předpokladu, že je jazyk podporován ve verzi 2,1.
> * v 2.1 vrátí jenom kompletní sadu dostupných entit pro anglické, zjednodušené, francouzské, německé a španělské jazyky.  Entity "Person", "Location" a "Organization" jsou vráceny pro ostatní podporované jazyky.

| Jazyk               | Kód jazyka | Podpora verze 2.1 | Podpora V3 | Od verze V3 model: |       Poznámky        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabština                |     `ar`      |     ✓      |            |                                 |                    |
| Čeština                 |     `cs`      |     ✓      |            |                                 |                    |
| Čínština (zjednodušená)     |   `zh-hans`   |     ✓      |            |                                 | `zh` také přijato |
| Čínština (tradiční)   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Dánština                |     `da`      |     ✓      |            |                                 |                    |
| Nizozemština                 |     `nl`      |     ✓      |            |                                 |                    |
| Angličtina                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finština               |     `fi`      |     ✓      |            |                                 |                    |
| Francouzština                 |     `fr`      |     ✓      |            |                                 |                    |
| Němčina                 |     `de`      |     ✓      |            |                                 |                    |
| Hebrejština                |     `he`      |     ✓      |            |                                 |                    |
| Maďarština             |     `hu`      |     ✓      |            |                                 |                    |
| Italština               |     `it`      |     ✓      |            |                                 |                    |
| Japonština              |     `ja`      |     ✓      |            |                                 |                    |
| Korejština                |     `ko`      |     ✓      |            |                                 |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` také přijato |
| Polština                |     `pl`      |     ✓      |            |                                 |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓      |            |                                 | `pt` také přijato |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Ruština              |     `ru`      |     ✓      |            |                                 |                    |
| Španělština               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Švédština               |     `sv`      |     ✓      |            |                                 |                    |
| Turečtina               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/key-phrase-extraction)

> [!NOTE]
> Verze modelu Extrakce klíčových frází před 2020-07-01 mají limit 64 znaků. Toto omezení není k dispozici v novějších verzích modelů.

| Jazyk              | Kód jazyka | Podpora v2 | Podpora V3 | K dispozici od verze V3 modelu: |       Poznámky        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Nizozemština                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Angličtina               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finština               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francouzština                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Němčina                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italština               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonština              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Korejština                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` také přijato |
| Polština                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` také přijato |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Ruština               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Španělština               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Švédština               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Propojení entit](#tab/entity-linking)

| Jazyk | Kód jazyka | Podpora v2 | Podpora V3 | K dispozici od verze V3 modelu: | Poznámky |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Angličtina  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Španělština  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language-detection)

Rozhraní API pro analýzu textu může detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků.  Rozpoznávání jazyka vrátí "skript" jazyka. Například pro frázi "Mám k dispozici pes" se vrátí  `en` místo  `en-US` . Jediným zvláštním případem je čínština, kde se vrátí funkce pro detekci jazyka, `zh_CHS` nebo `zh_CHT` Pokud se může určit skript, který daný text zadal. V situacích, kdy se konkrétní skript nedá identifikovat pro čínský dokument, vrátí se jednoduše `zh` .

Nezveřejňujeme vám přesný seznam jazyků pro tuto funkci, ale dokáže detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků. 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které nelze zjistit, je `unknown` .

---

## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu?](overview.md)   
