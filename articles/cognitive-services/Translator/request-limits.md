---
title: Limity požadavků - Překladač Text API
titleSuffix: Azure Cognitive Services
description: Tento článek uvádí omezení požadavků pro překladač text rozhraní API. Poplatky jsou účtovány na základě počtu znaků, nikoli četnosti požadavků s limitem 5 000 znaků na žádost. Omezení znaků jsou založeny na předplatném, s F0 omezena na 2 miliony znaků za hodinu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498931"
---
# <a name="request-limits-for-translator-text"></a>Omezení požadavků pro text překladatele

Tento článek obsahuje omezení omezení pro překladač text rozhraní API. Služby zahrnují překlad, přepis, detekci délky věty, detekci jazyka a alternativní překlady.

## <a name="character-and-array-limits-per-request"></a>Omezení počtu znaků a pole na požadavek

Každý požadavek na překlad je omezen na 5 000 znaků ve všech cílových jazycích, do kterých překládáte. Například odeslání požadavku na překlad 1 500 znaků přeložit do 3 různých jazyků má za následek velikost požadavku 1,500x3 = 4,500 znaků, který splňuje limit požadavku. Účtují se vám za znak, nikoli podle počtu žádostí. Doporučujeme odesílat kratší požadavky.

V následující tabulce jsou uvedena omezení prvků pole a znaků pro každou operaci rozhraní TRANSLATOR Text API.

| Operace | Maximální velikost prvku pole |   Maximální počet prvků pole |  Maximální velikost požadavku (znaky) |
|:----|:----|:----|:----|
| Překlad | 5 000 | 100   | 5 000 |
| Transliterace | 5 000 | 10    | 5 000 |
| Zjišťování | 10 000 | 100 |   50 000 |
| BreakSentence | 10 000    | 100 | 50 000 |
| Slovníkové vyhledávání| 100 |  10  | 1 000 |
| Příklady slovníku | 100 pro text a 100 pro překlad (200 celkem)| 10|   2 000 |

## <a name="character-limits-per-hour"></a>Omezení počtu znaků za hodinu

Limit počtu znaků za hodinu je založen na úrovni předplatného Translator Text. 

Hodinová kvóta by měla být spotřebována rovnoměrně po celou hodinu. Například při limitu f0 vrstvy 2 miliony znaků za hodinu by znaky neměly být spotřebovány rychleji než zhruba 33 300 znaků za minutu posuvné okno (2 miliony znaků děleno 60 minut).

Pokud tato omezení dosáhnete nebo překročíte nebo odešlete příliš velkou část kvóty v krátkém časovém období, pravděpodobně obdržíte odpověď mimo kvótu. Neexistují žádná omezení pro souběžné požadavky.

| Úroveň | Omezení znaků |
|------|-----------------|
| F0 | 2 miliony znaků za hodinu |
| S1 | 40 milionů znaků za hodinu |
| S2 / C2 | 40 milionů znaků za hodinu |
| S3 / C3 | 120 milionů znaků za hodinu |
| S4 / C4 | 200 milionů znaků za hodinu |

Limity pro [předplatná s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) jsou stejné jako úroveň S1.

Tato omezení jsou omezena na standardní modely překladů společnosti Microsoft. Vlastní modely překladu, které používají vlastní překladač jsou omezeny na 1 800 znaků za sekundu.

## <a name="latency"></a>Latence

Překladač text api má maximální latenci 15 sekund pomocí standardních modelů a 120 sekund při použití vlastních modelů. Obvykle odpovědi *na text do 100 znaků* jsou vráceny v 150 milisekund ách na 300 milisekund. Vlastní překladač modely mají podobné charakteristiky latence na trvalé rychlosti požadavku a může mít vyšší latenci, pokud je míra požadavků přerušovaná. Doba odezvy se bude lišit v závislosti na velikosti požadavku a dvojice jazyků. Pokud v tomto časovém rámci neobdržíte překlad nebo [odpověď na chybu,](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) zkontrolujte kód, síťové připojení a opakujte akci. 

## <a name="sentence-length-limits"></a>Omezení délky věty

Při použití funkce [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) je délka věty omezena na 275 znaků. Existují výjimky pro tyto jazyky:

| Jazyk | kód | Omezení znaků |
|----------|------|-----------------|
| Chinese | Zh | 132 |
| Němčina | De | 290 |
| Italština | je to | 280 |
| Japonština | ja | 150 |
| Portugalština | pt | 290 |
| Španělština | Ano | 280 |
| Italština | je to | 280 |
| Thajština | Th | 258 |

> [!NOTE]
> Toto omezení se nevztahuje na překlady.

## <a name="next-steps"></a>Další kroky

* [Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionální dostupnost](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referenční informace k rozhraní Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
