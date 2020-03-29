---
title: Posuny textu v rozhraní API analýzy textu
titleSuffix: Azure Cognitive Services
description: Další informace o posuny způsobené vícejazyčné a emoji kódování.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219233"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Posuny textu ve výstupu rozhraní API pro analýzu textu

Podpora vícejazyčných a emoji vedla k kódování Unicode, které používají více než jeden [bod kódu](https://wikipedia.org/wiki/Code_point) k reprezentaci jednoho zobrazeného znaku, nazývaného grafém. Například emodži, jako 👍 je 🌷 a mohou používat několik znaků k komponovat tvar s dalšíznaky pro vizuální atributy, jako je například tón pleti. Podobně hindské slovo `अनुच्छेद` je kódováno jako pět písmen a tři diakritické znaménky.

Vzhledem k různým délkám možných vícejazyčných a emoji kódování, rozhraní API analýzy textu může vrátit posuny v odpovědi.

## <a name="offsets-in-the-api-response"></a>Posuny v odpovědi rozhraní API. 

Kdykoli jsou vráceny posuny odpověď rozhraní API, například [rozpoznávání pojmenovaných entit](../how-tos/text-analytics-how-to-entity-linking.md) nebo [analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md), pamatujte na následující:

* Prvky v odpovědi může být specifické pro koncový bod, který byl volán. 
* Datové části HTTP POST/GET jsou kódovány v [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), což může nebo nemusí být výchozí kódování znaků v kompilátoru nebo operačním systému na straně klienta.
* Posuny odkazují na počty grafeme na základě standardu [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) nikoli počtu znaků.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahování podřetězců z textu s odsazením

Posuny mohou způsobit problémy při použití metod podřetězce založených na znaku, například metody [podřetězce .NET.](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Jeden problém je, že posun může způsobit, že metoda podřetězce skončí uprostřed víceznakového grafeme kódování namísto konce.

V rozhraní .NET zvažte použití třídy [StringInfo,](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) která umožňuje pracovat s řetězcem jako řadou textových prvků, nikoli s jednotlivými objekty znaků. Můžete také hledat knihovny grafeme splitter ve vašem preferovaném softwarovém prostředí. 

Rozhraní API pro analýzu textu vrátí také tyto textové prvky pro usnadnění.

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjištění jazyka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
