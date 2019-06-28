---
title: Požádat o omezení – Translator Text API
titleSuffix: Azure Cognitive Services
description: Tento článek uvádí omezení požadavků pro rozhraní Translator Text API. Poplatky se účtují na základě počtu znaků, není požadavek četnost s maximálně 5 000 znaků na jednu žádost. Znak omezení platí pro odběr na základě s F0 omezená na 2 miliony znaků za hodinu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: a7621cc80a38d9a07872a94d8e5221dc04023b86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435035"
---
# <a name="request-limits-for-translator-text"></a>Omezení počtu požadavků pro Translator Text

Tento článek obsahuje omezení pro rozhraní Translator Text API. Služby zahrnují překladu, přepis, detekce délka věty, detekce jazyka a alternativní překlady.

## <a name="character-and-array-limits-per-request"></a>Omezení znaků a pole každý požadavek

Každý požadavek přeložit je omezen na 5 000 znaků. Vám budou účtovány na znak, nikoli podle počtu požadavků. Doporučuje k odesílání požadavků kratší.

Následující tabulky zobrazí pole elementu a znak omezení pro každou operaci rozhraní Translator Text API.

| Operace | Maximální velikost prvku pole |   Maximální počet elementů pole |  Maximální velikost žádostí (ve znacích) |
|:----|:----|:----|:----|
| Translate | 5 000 | 100   | 5 000 |
| Transliterace | 5 000 | 10    | 5 000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| Slovníkové vyhledávání| 100 |  10  | 1 000 |
| Příklady slovníku | 100 pro text a 100 pro překlad (celkový počet 200)| 10|   2 000 |

## <a name="character-limits-per-hour"></a>Omezení znak za hodinu

Váš limit počtu znaků za hodinu je podle vaší úrovně předplatného Translator Text. 

Hodinová kvóta by měl rovnoměrně spotřebovává během hodiny. Například na limit F0 vrstvy 2 miliony znaků za hodinu, znaky by měl být využity rychleji než zhruba 33,300 znaků na jednu minutu posuvné okno (2 miliony znaků dělený 60 minut).

Pokud jste nebo překročí limity nebo pošlete příliš velké části kvóta v krátké době, pravděpodobně dostanete out kvóty odpovědi. Neplatí žádné limity u souběžných požadavků.

| Úroveň | Limit počtu znaků |
|------|-----------------|
| F0 | 2 miliony znaků za hodinu |
| S1 | 40 milionů znaků za hodinu |
| S2 / C2 | 40 milionů znaků za hodinu |
| S3 / C3 | 120 milionů znaků za hodinu |
| S4 / C4 | 200 milionů znaků za hodinu |

Omezení [víc služeb předplatná](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) jsou stejné jako pro úroveň S1.

Tato omezení jsou omezeny na standardní překladové modely od Microsoftu. Vlastní překladové modely, které používají vlastní Translator jsou omezené na 1 800 znak za sekundu.

## <a name="latency"></a>Latence

Translator Text API má maximální latence 15 sekund pomocí standardní modelů. Překlad pomocí vlastních modelů má maximální latence 25 sekund. V tuto chvíli budete obdrželi jste výsledek nebo vypršení časového limitu odpovědi. Obvykle jsou odpovědi vrácené v milisekundách 150 do 300 milisekund. Doby odezvy bude lišit v závislosti na velikosti pár požadavků a jazyk. Pokud jste neobdrželi překlad nebo s [chybová odpověď](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) v tomto časovém rámci, by měl zkontrolujte síťové připojení a zkuste to znovu.

## <a name="sentence-length-limits"></a>Omezení délky větu

Při použití [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) funkci věty délka je omezená na 275 znaků. Existují výjimky pro tyto jazyky:

| Jazyk | Kód | Limit počtu znaků |
|----------|------|-----------------|
| Čínština | zh | 132 |
| Němčina | de | 290 |
| italština | it | 280 |
| Japonština | ja | 150 |
| Portugalština | PT | 290 |
| Španělština | es | 280 |
| italština | it | 280 |
| Thajština | TH | 258 |

> [!NOTE]
> Toto omezení neplatí pro překlady.

## <a name="next-steps"></a>Další postup

* [Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionální dostupnost](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referenční dokumentace rozhraní Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
