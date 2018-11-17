---
title: Požádat o omezení – Translator Text API
titleSuffix: Azure Cognitive Services
description: Tento článek uvádí omezení požadavků pro rozhraní Translator Text API. Poplatky se účtují na základě počtu znaků, není požadavek četnost s maximálně 5 000 znaků na jednu žádost. Znak omezení platí pro odběr na základě s F0 omezená na 2 miliony znaků za hodinu.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: 29ef4cc594a3335d37bd813c0b682b248f96cf22
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51858343"
---
# <a name="request-limits-for-translator-text"></a>Omezení počtu požadavků pro Translator Text

Tento článek obsahuje omezení pro rozhraní Translator Text API. Služby zahrnují překladu, přepis, detekce délka věty, detekce jazyka a alternativní překlady.

## <a name="character-limits-per-request"></a>Znak omezení požadavku

Každý požadavek je omezený na 5 000 znaků. Vám budou účtovány na znak, nikoli podle počtu požadavků. Doporučujeme odesílat požadavky na kratší a mají některé požadavky zbývajících v daném okamžiku.

Neexistuje žádné omezení počtu nevyřízených požadavků na Translator Text API.

## <a name="character-limits-per-hour"></a>Omezení znak za hodinu

Váš limit počtu znaků za hodinu je podle vaší úrovně předplatného Translator Text. Pokud přístup nebo tato omezení překročí, pravděpodobně dostanete out kvóty odpovědi:

| Úroveň | Limit počtu znaků |
|------|-----------------|
| F0 | 2 miliony znaků za hodinu |
| S1 | 40 milionů znaků za hodinu |
| S2 | 40 milionů znaků za hodinu |
| S3 | 120 milionů znaků za hodinu |
| S4 | 200 milionů znaků za hodinu |

Tato omezení jsou omezeny na obecná systémy od Microsoftu. Vlastní překladových systémů, které používají centra společnosti Microsoft Translator jsou omezené na 1 800 znak za sekundu.

## <a name="latency"></a>Latence

Translator Text má maximální latence 13 sekund. V tuto chvíli budete obdrželi jste výsledek nebo vypršení časového limitu odpovědi. Obvykle jsou odpovědi vrácené v milisekundách 150 do 300 milisekund. Doba odezvy bude lišit v závislosti na velikosti nebo dvojici žádost a jazyk.

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
| Thajština | . | 258 |

> [!NOTE]
> Toto omezení neplatí pro překlady.

## <a name="next-steps"></a>Další postup

* [Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionální dostupnost](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referenční dokumentace rozhraní Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
