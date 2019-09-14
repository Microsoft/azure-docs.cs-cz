---
title: Podporované jazyky – Translator Speech API
titlesuffix: Azure Cognitive Services
description: Zobrazení jazyků podporovaných Translator Speech API.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2f5b48f2dbc2d109c03613676c6a119fd971603b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965443"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Jazyky podporované Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Pro překlad řeči se podporují následující jazyky. Pokud je pro překlad řeči podporován oba jazyky, je k dispozici převod řeči na řeč nebo převod řeči na text. Pokud není cílový jazyk pro překlad řeči podporován, je k dispozici pouze převod řeči na text.

| Rozpoznávání řeči, jazyka    |
|:----------- |
| Arabština (moderní Standard)      |
| Brazilská portugalština     |
| Čínština (Mandarínština)      |
| Angličtina      |
| Francouzština      |
| Němčina      |
| italština      |
| Japonština      |
| ruština      |
| Španělština      |

Translator Speech API podporuje následující jazyky jako cílový jazyk pro překlad řeči na text.

| Jazyk textu    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Kantonština (tradiční)      | `yue`          |
| Katalánština      | `ca`          |
| Zjednodušená čínština      | `zh-Hans`          |
| Tradiční čínština      | `zh-Hant`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
| Angličtina      | `en`          |
| Estonština      | `et`          |
| Vládní      | `fj`          |
| Filipínština      | `fil`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Daw      | `mww`          |
| Maďarština      | `hu`          |
|Islandština|`is`          |
| Indonéština      | `id`          |
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašský      | `mg`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Samoan      | `sm`          |
| Srbština (cyrilice)      | `sr-Cyrl`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Tahitian      | `ty`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| Tonžská      | `to`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Přístup k seznamu prostřednictvím kódu programu

Seznam podporovaných jazyků můžete získat programově pomocí prostředku jazyky. V seznamu je uveden kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Tento seznam je automaticky aktualizován službou Translator Speech, protože nové jazyky budou k dispozici.

Prostředek jazyky vrátí seznam podporovaných jazyků pro řeč, text a převod textu na řeč. Prostředek jazyků nevyžaduje ověření.

[Pokud si chcete vyzkoušet metodu languages, přejděte na reference k rozhraní API.](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translator

Chcete-li se rychle podívat na jazyky, na webu Microsoft Translator se zobrazí všechny jazyky podporované rozhraním API Translator Text a Speech. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
