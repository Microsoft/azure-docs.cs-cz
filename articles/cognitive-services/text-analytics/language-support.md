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
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 3e74098d368b6aef3a9f12d8b1369132b29e7a0f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608948"
---
# <a name="text-analytics-api-v3-language-support"></a>Podpora jazyka rozhraní API pro analýzu textu V3 

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]


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
| Hindština                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italština               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonština              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Korejština                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norština (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polština                |     `pl`      |     ✓      |            |                            |                    |
| Portugalština (Brazílie)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
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

Rozhraní API pro analýzu textu může detekovat široké spektrum jazyků, variant, dialektů a některých regionálních/kulturních jazyků a vracet zjištěné jazyky s názvem a kódem. Parametry kódu Rozpoznávání jazyka jazyka Analýza textu odpovídají standardu [BCP-47](https://tools.ietf.org/html/bcp47) , přičemž většina z nich vyhovuje identifikátorům [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které nelze zjistit, je `unknown` .

| Jazyk | Kód jazyka | Podpora V3 | K dispozici od verze V3 modelu: |
|:-|:-:|:-:|:-:|
| Afrikánština | `af` | ✓ |  |
| Albánština | `sq` | ✓ |  |
| Arabština | `ar` | ✓ |  |
| Arménština | `hy` | ✓ |  |
| Baskičtina | `eu` | ✓ |  |
| Běloruština | `be` | ✓ |  |
| Bengálština | `bn` | ✓ |  |
| Bosenština | `bs` | ✓ | 2020-09-01 |
| Bulharština | `bg` | ✓ |  |
| Barmština | `my` | ✓ |  |
| Katalánština, Valencijština | `ca` | ✓ |  |
| Středová šipka doprava | `km` | ✓ |  |
| Čínština | `zh` | ✓ |  |
| Čínština (zjednodušená) | `zh_chs` | ✓ |  |
| Čínština (tradiční) | `zh_cht` | ✓ |  |
| Chorvatština | `hr` | ✓ |  |
| Čeština | `cs` | ✓ |  |
| Dánština | `da` | ✓ |  |
| Dáríština | `prs` | ✓ | 2020-09-01 |
| Divehština, dhivehi, Maledivština | `dv` | ✓ |  |
| Holandština, Flemish | `nl` | ✓ |  |
| Angličtina | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Estonština | `et` | ✓ |  |
| Fijian | `fj` | ✓ | 2020-09-01 |
| Finština | `fi` | ✓ |  |
| Francouzština | `fr` | ✓ |  |
| Galicijština | `gl` | ✓ |  |
| Gruzínština | `ka` | ✓ |  |
| Němčina | `de` | ✓ |  |
| Řečtina | `el` | ✓ |  |
| Gudžarátština | `gu` | ✓ |  |
| Haitská, haitská kreolština | `ht` | ✓ |  |
| Hebrejština | `he` | ✓ |  |
| Hindština | `hi` | ✓ |  |
| Hmong Macek | `mww` | ✓ | 2020-09-01 |
| Maďarština | `hu` | ✓ |  |
| Islandština | `is` | ✓ |  |
| Indonéština | `id` | ✓ |  |
| Inuktitutština | `iu` | ✓ |  |
| Irština | `ga` | ✓ |  |
| Italština | `it` | ✓ |  |
| Japonština | `ja` | ✓ |  |
| Kannadština | `kn` | ✓ |  |
| Kazaština | `kk` | ✓ | 2020-09-01 |
| Korejština | `ko` | ✓ |  |
| Kurdština | `ku` | ✓ |  |
| Laoský | `lo` | ✓ |  |
| Znak | `la` | ✓ |  |
| Lotyština | `lv` | ✓ |  |
| Litevština | `lt` | ✓ |  |
| Makedonie | `mk` | ✓ |  |
| Malgašština | `mg` | ✓ | 2020-09-01 |
| Malajština | `ms` | ✓ |  |
| Malajalámština | `ml` | ✓ |  |
| Maltština | `mt` | ✓ |  |
| Maorština | `mi` | ✓ | 2020-09-01 |
| Maráthština | `mr` | ✓ | 2020-09-01 |
| Norština | `no` | ✓ |  |
| Norština (Nynorsk) | `nn` | ✓ |  |
| Krí | `or` | ✓ |  |
| Paštština, Paštština | `ps` | ✓ |  |
| Perština | `fa` | ✓ |  |
| Polština | `pl` | ✓ |  |
| Portugalština | `pt` | ✓ |  |
| Paňdžábština, Panjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Rumunština, Moldavian, moldavský | `ro` | ✓ |  |
| Ruština | `ru` | ✓ |  |
| Samoan | `sm` | ✓ | 2020-09-01 |
| Srbština | `sr` | ✓ |  |
| Sinhálské, Sinhalese | `si` | ✓ |  |
| Slovenština | `sk` | ✓ |  |
| Slovinština | `sl` | ✓ |  |
| Somálština | `so` | ✓ |  |
| Španělština, kastilština | `es` | ✓ |  |
| Svahilština | `sw` | ✓ |  |
| Švédština | `sv` | ✓ |  |
| Římské | `tl` | ✓ |  |
| Tahitian | `ty` | ✓ | 2020-09-01 |
| Tamilština | `ta` | ✓ |  |
| Telugština | `te` | ✓ |  |
| Thajština | `th` | ✓ |  |
| Tongánština | `to` | ✓ | 2020-09-01 |
| Turečtina | `tr` | ✓ |  |
| Ukrajinština | `uk` | ✓ |  |
| Urdština | `ur` | ✓ |  |
| Uzbečtina | `uz` | ✓ |  |
| Vietnamština | `vi` | ✓ |  |
| Velština | `cy` | ✓ |  |
| Jidiš | `yi` | ✓ |  |
| Yucatec Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu?](overview.md)   
