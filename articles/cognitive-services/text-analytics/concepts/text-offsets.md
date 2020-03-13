---
title: Posuny textu v rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o posunech způsobených kódováním ve vícejazyčném a emoji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219233"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Posunutí textu ve výstupu rozhraní API pro analýzu textu

Podpora vícejazyčného a Emoji vedlo k kódování Unicode, které používá více než jeden [bod kódu](https://wikipedia.org/wiki/Code_point) k reprezentaci jednoho zobrazeného znaku, který se nazývá grapheme. Například Emoji jako 🌷 a 👍 mohou použít několik znaků k vytvoření tvaru s dalšími znaky pro vizuální atributy, jako je například tónový vzhled. Podobně wordová `अनुच्छेद` v hindštině je kódována jako pět písmen a tři kombinace značek.

Z důvodu různých délek možného kódování ve vícejazyčném a Emoji může rozhraní API pro analýzu textu v odpovědi vracet posuny.

## <a name="offsets-in-the-api-response"></a>Posuny v odpovědi rozhraní API. 

Vždy, když jsou vráceny posunutí odpovědi rozhraní API, například [rozpoznávání pojmenovaných entit](../how-tos/text-analytics-how-to-entity-linking.md) nebo [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md), pamatujte na následující:

* Prvky v odpovědi mohou být specifické pro koncový bod, který byl volán. 
* Datové části HTTP POST/GET jsou kódované v [kódování UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), což může nebo nemusí být výchozím kódováním znaků v kompilátoru na straně klienta nebo v operačním systému.
* Posuny odkazují na grapheme počty založené na standardu [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , ne na počtu znaků.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahování podřetězců z textu s posuny

Posuny mohou způsobovat problémy při použití metod podřetězců založených na znacích, například metody [substring](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) rozhraní .NET. Jedním z nich je, že posun může způsobit, že metoda podřetězce dokončí uprostřed grapheme kódování s více znaky namísto konce.

V rozhraní .NET zvažte použití třídy [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , která umožňuje pracovat s řetězcem jako řadou textových prvků, nikoli jednotlivými znakovými objekty. V upřednostňovaném softwarovém prostředí můžete také vyhledat rozgraphemeelné knihovny. 

Rozhraní API pro analýzu textu vrátí tyto textové prvky také pro usnadnění práce.

## <a name="see-also"></a>Viz také:

* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
