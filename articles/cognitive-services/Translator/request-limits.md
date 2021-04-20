---
title: Omezení požadavků – Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam omezení požadavků pro překladatele. Poplatky se účtují na základě počtu znaků, nikoli frekvence požadavků s omezením 5 000 znaků na požadavek. Omezení znaků jsou založená na předplatném s F0, která jsou omezená na 2 000 000 znaků za hodinu.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727937"
---
# <a name="request-limits-for-translator"></a>Omezení požadavků pro překladatele

Tento článek poskytuje omezení omezování pro překlad překladatelů, převádění, detekci délky vět, rozpoznávání jazyka a alternativní překlady.

## <a name="character-and-array-limits-per-request"></a>Omezení počtu znaků a polí na požadavek

Jednotlivé požadavky na překlad jsou omezeny na 10 000 znaků napříč všemi cílovými jazyky, na které překládáte. Například odeslání požadavku na překlad 3 000 znaků k převodu na tři různé jazyky má za následek velikost požadavku 3000x3 = 9 000 znaků, které odpovídají limitu požadavku. Účtují se vám poplatky za jednotlivé znaky, nikoli počet požadavků. Doporučuje se posílat kratší požadavky.

Následující tabulka uvádí prvky pole a omezení znaků pro každou operaci překladatele.

| Operace | Maximální velikost elementu pole |    Maximální počet prvků pole |    Maximální velikost požadavku (ve znacích) |
|:----|:----|:----|:----|
| Překlad | 10 000| 100| 10 000 |
| Transliterace | 5 000| 10| 5 000 |
| Zjišťování | 50,000 |100 |50,000 |
| BreakSentence | 50,000| 100 |50,000 |
| Slovníkové vyhledávání| 100 |10| 1 000 |
| Příklady slovníku | 100 pro text a 100 pro překlad (200 celkem)| 10|2 000 |

## <a name="character-limits-per-hour"></a>Omezení počtu znaků za hodinu

Limit počtu znaků za hodinu vychází z vaší úrovně předplatného překladatele.

Hodinová kvóta by se měla v průběhu hodiny spotřebovat rovnoměrně. Například na úrovni F0, která je 2 000 000 znaků za hodinu, by měly být znaky spotřebovány rychleji než přibližně 33 300 znaků za minutu posuvných oken (2 000 000 znaků dělených 60 minutami).

Pokud tato omezení dosáhnete nebo překročíte nebo v krátké době zadáte příliš velkou část kvóty, pravděpodobně obdržíte odpověď na neplatnou kvótu. Neexistují žádná omezení souběžných žádostí.

| Úroveň | Omezení počtu znaků |
|------|-----------------|
| F0 | 2 000 000 znaků za hodinu |
| S1 | 40 000 000 znaků za hodinu |
| S2/C2 | 40 000 000 znaků za hodinu |
| S3/C3 | 120 000 000 znaků za hodinu |
| S4/C4 | 200 000 000 znaků za hodinu |

Limity pro [předplatné s více službami](./reference/v3-0-reference.md#authentication) jsou stejné jako u úrovně S1.

Tato omezení se omezují na standardní modely překladu společnosti Microsoft. Vlastní modely překladu, které používají vlastní překladatele, jsou na jeden model omezeny na 1 800 znaků za sekundu.

## <a name="latency"></a>Latence

Při použití vlastních modelů má Překladatel maximální latenci 15 sekund s použitím standardních modelů a 120 sekund. Odpovědi *na text v rámci 100 znaků* jsou obvykle vraceny během 150 milisekund na 300 milisekund. Modely vlastního překladatele mají podobné vlastnosti latence u trvalých požadavků a můžou mít vyšší latenci, když je vaše frekvence požadavků přerušovaná. Doby odezvy se budou lišit v závislosti na velikosti páru požadavků a jazyků. Pokud neobdržíte překlad nebo odpověď na [chybu](./reference/v3-0-reference.md#errors) v rámci tohoto časového období, ověřte si kód, připojení k síti a zkuste to znovu.

## <a name="sentence-length-limits"></a>Omezení délky věty

Při použití funkce [BreakSentence](./reference/v3-0-break-sentence.md) je délka věty omezená na 275 znaků. Existují výjimky pro tyto jazyky:

| Jazyk | Kód | Omezení počtu znaků |
|----------|------|-----------------|
| Čínština | ZH | 166 |
| Němčina | & | 800 |
| Italština | její | 800 |
| Japonština | dža | 166 |
| Portugalština | pt | 800 |
| španělština | es | 800 |
| Thajština | Kolik | 180 |

> [!NOTE]
> Toto omezení se nevztahuje na překlady.

## <a name="next-steps"></a>Další kroky

* [Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionální dostupnost](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referenční informace ke službě Translator v3](./reference/v3-0-reference.md)