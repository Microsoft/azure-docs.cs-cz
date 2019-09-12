---
title: Jazyková podpora – Translator Text API
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, které Translator Text API podporuje.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb0552612d95500e01edd91aff36406d4c2282bd
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70909984"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Podpora jazyků a oblastí pro Translator Text API

Translator Text API podporuje překlady textu na text v následujících jazycích. Neuronové Machine Translation (NMT) je nový standard pro vysoce kvalitní překlady počítačů s podporou AI a je k dispozici jako výchozí hodnota V3 Translator Text API, pokud je k dispozici systém neuronové.

[Další informace o tom, jak strojový překlad funguje](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Překlad

**Rozhraní API pro překladatele v2**

> [!NOTE]
> Verze V2 byla zastaralá od 30. dubna 2018. Pokud chcete využívat nové funkce, které jsou k dispozici výhradně v v3, migrujte prosím své aplikace na v3.

* Pouze statistické: Pro tento jazyk není k dispozici žádný neuronové systém.
* Neuronové k dispozici: K dispozici je neuronové systém. Pro přístup k `category=generalnn` systému neuronové použijte parametr.
* Neuronové výchozí: Neuronové je výchozí systém překladu. Použijte parametr `category=smt` pro přístup ke statistickému systému pro použití se službou Microsoft Translator hub.
* Neuronové: K dispozici je pouze překlad neuronové.

**Rozhraní API pro překladatele V3** Rozhraní API pro překladatele v3 je ve výchozím nastavení neuronové a statistické systémy jsou k dispozici pouze v případě, že neexistuje žádný systém neuronové.

> [!NOTE]
> V současné době je k dispozici podmnožina jazyků neuronové ve vlastním překladateli a postupně přidáváme další. [Zobrazit jazyky, které jsou aktuálně k dispozici ve vlastním překladateli](#customization).

|Jazyk|  Kód jazyka|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikánština| `af`    |Pouze statistické|  Neurální|
|arabština|    `ar`    |Neuronové k dispozici|  Neurální|
|Bengálština|    `bn`    |Neuronové k dispozici|  Neurální|
|Bosenština (latinka)|   `bs`    |Neuronové k dispozici|  Neurální|
|Bulharština| `bg`    |Neuronové k dispozici|  Neurální|
|Kantonština (tradiční)|   `yue`   |Pouze statistické|  Ložený|
|Katalánština|   `ca`    |Pouze statistické|  Ložený|
|Zjednodušená čínština|    `zh-Hans`   |Neuronové výchozí |Neurální|
|Tradiční čínština|   `zh-Hant`   |Neuronové výchozí |Neurální|
|Chorvatština|  `hr`    |Neuronové k dispozici|  Neurální|
|Čeština| `cs`    |Neuronové k dispozici|  Neurální|
|dánština|    `da`    |Neuronové k dispozici   |Neurální|
|Holandština| `nl`    |Neuronové k dispozici|  Neurální|
|Angličtina|   `en`    |Neuronové k dispozici|  Neurální|
|Estonština|  `et`    |Neuronové k dispozici|  Neurální|
|Vládní|    `fj`    |Pouze statistické|  Ložený|
|Filipínština|  `fil`   |Pouze statistické|  Ložený|
|Finština|   `fi`    |Neuronové k dispozici|  Neurální|
|Francouzština|    `fr`    |Neuronové k dispozici|  Neurální|
|Němčina|    `de`    |Neuronové k dispozici|  Neurální|
|Řečtina| `el`    |Neuronové k dispozici|  Neurální|
|Haitská kreolština|    `ht`    |Pouze statistické   |Ložený|
|Hebrejština |`he`   |Neuronové k dispozici   |Neurální|
|Hindština| `hi`    |Neuronové výchozí|    Neurální|
|Hmong Daw| `mww`   |Pouze statistické|  Ložený|
|Maďarština| `hu`    |Neuronové k dispozici|  Neurální|
|Islandština| `is`    |Jenom neuronové|   Neurální|
|Indonéština|    `id`    |Pouze statistické|  Ložený|
|italština|   `it`    |Neuronové k dispozici|  Neurální|
|Japonština|  `ja`    |Neuronové k dispozici|  Neurální|
|Svahilština| `sw`    |Pouze statistické|  Ložený|
|Klingon|   `tlh`   |Pouze statistické|  Ložený|
|Klingon (plqaD)|   `tlh-Qaak`  |Pouze statistické|  Ložený|
|Korejština |`ko`   |Neuronové k dispozici|  Neurální|
|Lotyština|   `lv`    |Neuronové k dispozici|  Neurální|
|Litevština|    `lt`    |Neuronové k dispozici|  Neurální|
|Malgašský|  `mg`    |Pouze statistické|  Ložený|
|Malajština| `ms`    |Pouze statistické   |Ložený|
|Maltština|   `mt`    |Pouze statistické|  Ložený|
|norština| `nb`    |Neuronové k dispozici|  Neurální|
|Perština|   `fa`    |Neuronové k dispozici|  Neurální|
|polština|    `pl`    |Neuronové k dispozici|  Neurální|
|Portugalština|    `pt`    |Neuronové k dispozici|  Neurální|
|Queretaro Otomi|   `otq`   |Pouze statistické|  Ložený|
|Rumunština|  `ro`    |Neuronové k dispozici|  Neurální|
|ruština|   `ru`    |Neuronové k dispozici|  Neurální|
|Samoan|    `sm`    |Pouze statistické|  Ložený|
|Srbština (cyrilice)|    `sr-Cyrl`   |Pouze statistické|  Ložený|
|Srbština (latinka)|   `sr-Latn`   |Pouze statistické   |Ložený|
|Slovenština|    `sk`    |Neuronové k dispozici|  Neurální|
|Slovinština| `sl`    |Neuronové k dispozici|  Neurální|
|Španělština|   `es`    |Neuronové k dispozici|  Neurální|
|švédština|   `sv`    |Neuronové k dispozici   |Neurální|
|Tahitian|  `ty`    |Pouze statistické|  Ložený|
|Tamilština| `ta`    |Pouze statistické|  Ložený|
|Telugština|    `te`    |Jenom neuronové|   Neurální|
|Thajština|  `th`    |Neuronové k dispozici|  Neurální|
|Tonžská|    `to`    |Pouze statistické|  Ložený|
|turečtina|   `tr`    |Neuronové k dispozici   |Neurální|
|Ukrajinština| `uk`    |Neuronové k dispozici|  Neurální|
|Urdština|  `ur`    |Pouze statistické|  Ložený|
|Vietnamština|    `vi`    |Neuronové k dispozici|  Neurální|
|Velština| `cy`    |Neuronové k dispozici|  Neurální|
|Yucatec Maya|  `yua`   |Pouze statistické|  Ložený|

## <a name="transliteration"></a>Transkripce

Metoda přepisu podporuje následující jazyky. V "do/z", "<-->" označuje, že jazyk lze přepřepisovat z nebo na některý z uvedených skriptů. "-->" Označuje, že jazyk lze přepřepisovat pouze z jednoho skriptu na druhý.

| Jazyk    | Kód jazyka | Skript | Do/z | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arabština | `ar` | Arabština`Arab` | <--> | Znak`Latn` |
|Bengálština  | `bn` | Bengálština`Beng` | <--> | Znak`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Znak`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Tradiční čínština`Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Znak`Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Zjednodušená čínština`Hans` |
| Gudžarátština | `gu`  | Gudžarátština`Gujr` | --> | Znak`Latn` |
| Hebrejština | `he` | Hebrejština`Hebr` | <--> | Znak`Latn` |
| Hindština | `hi` | Koncový`Deva` | <--> | Znak`Latn` |
| Japonština | `ja` | Japonština`Jpan` | <--> | Znak`Latn` |
| Kannadština | `kn` | Kannada `Knda` | --> | Znak`Latn` |
| Malajálamština | `ml` | Malajalámština`Mlym` | --> | Znak`Latn` |
| Maráthština | `mr` | Koncový`Deva` | --> | Znak`Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Znak`Latn` |
| Paňdžábština | `pa` | Gurmukhi `Guru`  | <--> | Znak`Latn`  |
| Srbština (cyrilice) | `sr-Cyrl` | B`Cyrl`  | --> | Znak`Latn` |
| Srbština (latinka) | `sr-Latn` | Znak`Latn` | --> | B`Cyrl`|
| Tamilština | `ta` | Tamilština`Taml` | --> | Znak`Latn` |
| Telugština | `te` | Telugština`Telu` | --> | Znak`Latn` |
| Thajština | `th` | Thajština`Thai` | <--> | Znak`Latn` |

## <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky pro nebo z angličtiny pomocí vyhledávacích a ukázkových metod.

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Katalánština      | `ca`          |
| Zjednodušená čínština      | `zh-Hans`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Daw      | `mww`          |
| Maďarština      | `hu`          |
| Islandština    | `is`  |
| Indonéština      | `id`          |
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |

## <a name="detect"></a>Detect

Translator Text API detekuje všechny jazyky, které jsou k dispozici pro překlad a převod.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Přístup k seznamu jazyků Translator Text API programově

Seznam podporovaných jazyků pro Translator Text API v 3.0 můžete načíst pomocí metody languages. Můžete zobrazit seznam podle funkcí, kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Služba Microsoft Translator automaticky aktualizuje tento seznam, protože jsou k dispozici nové jazyky.

[Referenční dokumentace operací zobrazení jazyků](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro přizpůsobení nebo z angličtiny pomocí [vlastního překladatele](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Zjednodušená čínština      | `zh-Hans`          |
|Tradiční čínština|   `zh-Hant`   |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
| Angličtina    | `en`     |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Maďarština      | `hu`          |
| Islandština | `is` |
| Indonéština|   `id`    |
| Irština | `ga`  |
| italština      | `it`          |
| Japonština      | `ja`          |
|Svahilština| `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
|Malgašský|  `mg`    |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
|Samoan|    `sm`    |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Thajština      | `th`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Vietnamština      | `vi`          |
| Velština | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translator

Chcete-li se rychle podívat na jazyky, na webu Microsoft Translator se zobrazí všechny jazyky podporované rozhraním API Translator Text a Speech. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
