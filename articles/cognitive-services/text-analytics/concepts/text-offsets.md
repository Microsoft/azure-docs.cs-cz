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
ms.openlocfilehash: 14fd7c2b034077d818d1a1224d3c4c12a7fc07bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855650"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Posunutí textu ve výstupu rozhraní API pro analýzu textu

Podpora vícejazyčného a Emoji vedlo k kódování Unicode, které používá více než jeden [bod kódu](https://wikipedia.org/wiki/Code_point) k reprezentaci jednoho zobrazeného znaku, který se nazývá grapheme. Například Emoji jako 🌷 a 👍 může použít několik znaků k vytvoření tvaru s dalšími znaky pro vizuální atributy, jako je například tónový vzhled. Podobně je v hindštině Word `अनुच्छेद` kódovaný jako pět písmen a tři kombinace značek.

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

## <a name="offsets-in-api-version-31-preview"></a>Posuny v rozhraní API verze 3,1-Preview

Počínaje rozhraním API verze 3,1-Preview. 1 všechny koncové body rozhraní API pro analýzu textu, které vracejí posun, budou podporovat `stringIndexType` parametr. Tento parametr upravuje `offset` `length` atributy a ve výstupu rozhraní API tak, aby odpovídaly požadovanému schématu iterace řetězce. V současné době podporujeme tři typy:

1. `textElement_v8` (výchozí): Iteruje přes graphemes, jak je definované standardem [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) .
2. `unicodeCodePoint`: iterace přes [body kódu Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), výchozí schéma pro Python 3
3. `utf16CodeUnit`: iterace přes [jednotky kódu UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), výchozí schéma pro JavaScript, Java a .NET.

Pokud `stringIndexType` požadované odpovídá programovacímu prostředí volby, extrakce dílčích řetězců lze provést pomocí standardních metod substring nebo Slice. 

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
