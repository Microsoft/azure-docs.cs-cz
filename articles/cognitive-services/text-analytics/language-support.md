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
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: f6a109c10491ad2eabb12069157e9e6f394bc1f4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736606"
---
# <a name="text-analytics-api-v3-language-support"></a>Podpora jazyka rozhraní API pro analýzu textu V3 

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment-analysis)

| Jazyk              | Kód jazyka | Podpora V3 | Spouští se verze modelu V3: |              Poznámky |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Čínština (zjednodušená)    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` také přijato |
| Čínština (tradiční)   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Angličtina               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francouzština                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Němčina                |     `de`      |     ✓      |         2019-10-01         |                    |
| Italština               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japonština              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Korejština                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` také přijato |
| Španělština               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turečtina               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Dolování stanovisek (verze 3.1 – Preview)

| Jazyk              | Kód jazyka | Od verze V3 model: |              Poznámky |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angličtina               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Rozpoznávání pojmenovaných entit (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Pro jazyky označené znakem * jsou vraceny pouze entity "Person", "Location" a "Organization".

| Jazyk               | Kód jazyka | Podpora V3 | Od verze V3 model: |       Poznámky        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabština                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Čínština (zjednodušená)     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` také přijato |
| Čínština (tradiční)   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Čeština                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Dánština                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Nizozemština                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Angličtina                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finština               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francouzština                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Němčina                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebrejština                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Maďarština             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italština               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonština              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korejština                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norština (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` také přijato |
| Polština                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` také přijato |
| Ruština              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Španělština               |     `es`      |     ✓       |               2020-04-01        |                    |
| Švédština               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turečtina               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/key-phrase-extraction)

| Jazyk              | Kód jazyka |  Podpora V3 | K dispozici od verze V3 modelu: |       Poznámky        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Dánština                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Nizozemština                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Angličtina               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Finština               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Francouzština                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Němčina                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Italština               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japonština              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Korejština                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` také přijato |
| Polština                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| portugalština (Portugalsko) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` také přijato |
| Ruština               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Španělština               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Švédština               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Propojení entit](#tab/entity-linking)

| Jazyk | Kód jazyka |  Podpora V3 | K dispozici od verze V3 modelu: | Poznámky |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Angličtina  |     `en`      |     ✓      |                2019-10-01                 |       |
| Španělština  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Identifikovatelné osobní údaje (PII)](#tab/pii)

| Jazyk               | Kód jazyka | Podpora V3 | Od verze V3 model: |       Poznámky        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Čínština (zjednodušená)     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` také přijato |
| Angličtina                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francouzština                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Němčina                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italština               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonština              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korejština                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugalština (Brazílie)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| portugalština (Portugalsko) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` také přijato |
| Španělština               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language-detection)

Rozhraní API pro analýzu textu může detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků a vracet zjištěné jazyky s názvem a kódem. Parametry kódu Rozpoznávání jazyka jazyka Analýza textu odpovídají standardu [BCP-47](https://tools.ietf.org/html/bcp47) , přičemž většina z nich vyhovuje identifikátorům [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které nelze zjistit, je `unknown` .

| Jazyk | Kód jazyka | Podpora V3 | K dispozici od verze V3 modelu: |
|:-|:-:|:-:|:-:|
|Afrikánština|`af`|✓|    |
|Albánština|`sq`|✓|    |
|Amharšina|`am`|✓|2021-01-05|
|Arabština|`ar`|✓|    |
|Arménština|`hy`|✓|    |
|Ásámština|`as`|✓|2021-01-05|
|Ázerbájdžánština|`az`|✓|2021-01-05|
|Baskičtina|`eu`|✓|    |
|Běloruština|`be`|✓|    |
|Bengálština|`bn`|✓|    |
|Bosenština|`bs`|✓|2020-09-01|
|Bulharština|`bg`|✓|    |
|Barmština|`my`|✓|    |
|Katalánština|`ca`|✓|    |
|Středová šipka doprava|`km`|✓|    |
|Čínština|`zh`|✓|    |
|Čínština (zjednodušená)|`zh_chs`|✓|    |
|Čínština (tradiční)|`zh_cht`|✓|    |
|Korsičtina|`co`|✓|2021-01-05|
|Chorvatština|`hr`|✓|    |
|Čeština|`cs`|✓|    |
|Dánština|`da`|✓|    |
|Dáríština|`prs`|✓|2020-09-01|
|Divehština|`dv`|✓|    |
|Nizozemština|`nl`|✓|    |
|Angličtina|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estonština|`et`|✓|    |
|Fijian|`fj`|✓|2020-09-01|
|Finština|`fi`|✓|    |
|Francouzština|`fr`|✓|    |
|Galicijština|`gl`|✓|    |
|Gruzínština|`ka`|✓|    |
|Němčina|`de`|✓|    |
|Řečtina|`el`|✓|    |
|Gudžarátština|`gu`|✓|    |
|Haitská|`ht`|✓|    |
|Hauština|`ha`|✓|2021-01-05|
|Hebrejština|`he`|✓|    |
|Hindština|`hi`|✓|    |
|Hmong Macek|`mww`|✓|2020-09-01|
|Maďarština|`hu`|✓|    |
|Islandština|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonéština|`id`|✓|    |
|Inuktitutština|`iu`|✓|    |
|Irština|`ga`|✓|    |
|Italština|`it`|✓|    |
|Japonština|`ja`|✓|    |
|Písmo|`jv`|✓|2021-01-05|
|Kannadština|`kn`|✓|    |
|Kazaština|`kk`|✓|2020-09-01|
|Kinyarwandština|`rw`|✓|2021-01-05|
|Kirgizské|`ky`|✓|2021-01-05|
|Korejština|`ko`|✓|    |
|Kurdština|`ku`|✓|    |
|Laoský|`lo`|✓|    |
|Znak|`la`|✓|    |
|Lotyština|`lv`|✓|    |
|Litevština|`lt`|✓|    |
|Lucemburština|`lb`|✓|2021-01-05|
|Makedonie|`mk`|✓|    |
|Malgašština|`mg`|✓|2020-09-01|
|Malajština|`ms`|✓|    |
|Malajalámština|`ml`|✓|    |
|Maltština|`mt`|✓|    |
|Maorština|`mi`|✓|2020-09-01|
|Maráthština|`mr`|✓|2020-09-01|
|Mongolština|`mn`|✓|2021-01-05|
|Nepálština|`ne`|✓|2021-01-05|
|Norština|`no`|✓|    |
|Norština (Nynorsk)|`nn`|✓|    |
|Krí|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Perština|`fa`|✓|    |
|Polština|`pl`|✓|    |
|Portugalština|`pt`|✓|    |
|Paňdžábština|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Rumunština|`ro`|✓|    |
|Ruština|`ru`|✓|    |
|Samoan|`sm`|✓|2020-09-01|
|Srbština|`sr`|✓|    |
|Šonština|`sn`|✓|2021-01-05|
|Sindhština|`sd`|✓|2021-01-05|
|Sinhálské|`si`|✓|    |
|Slovenština|`sk`|✓|    |
|Slovinština|`sl`|✓|    |
|Somálština|`so`|✓|    |
|Španělština|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Svahilština|`sw`|✓|    |
|Švédština|`sv`|✓|    |
|Římské|`tl`|✓|    |
|Tahitian|`ty`|✓|2020-09-01|
|Tádžičtina|`tg`|✓|2021-01-05|
|Tamilština|`ta`|✓|    |
|Tatarština|`tt`|✓|2021-01-05|
|Telugština|`te`|✓|    |
|Thajština|`th`|✓|    |
|Písmeno|`bo`|✓|2021-01-05|
|Tigriňňa|`ti`|✓|2021-01-05|
|Tongánština|`to`|✓|2020-09-01|
|Turečtina|`tr`|✓|2021-01-05|
|Turkmenština|`tk`|✓|2021-01-05|
|Xhoština|`xh`|✓|2021-01-05|
|Jorubština|`yo`|✓|2021-01-05|
|Zulština|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu?](overview.md)   
