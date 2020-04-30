---
title: Omezení dat pro rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Omezení dat pro rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 4af2d060c11b804c5fa09bfdabbcb9753f7d5885
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204411"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Omezení dat a přenosové rychlosti rozhraní API pro analýzu textu
<a name="data-limits"></a>

V tomto článku najdete omezení velikosti a tarify, které můžete použít k odesílání dat rozhraní API pro analýzu textu. 

## <a name="data-limits"></a>Omezení dat

> [!NOTE]
> * Pokud potřebujete analyzovat větší dokumenty, než je povolený limit, můžete text před odesláním do rozhraní API rozdělit do menších bloků textu. 
> * Dokument je textový znak v jednom řetězci.  

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 120 znaků měřených pomocí [StringInfo. lengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximální velikost celého požadavku | 1 MB |

Maximální počet dokumentů, které můžete poslat v rámci jedné žádosti, bude záviset na verzi rozhraní API a funkci, kterou používáte.

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

> [!NOTE]
> Pokud vaše žádost rozhraní API V3 překročí tato omezení, ale spadá do omezení v2, vrátí se v odpovědi rozhraní API upozornění. Počínaje 7/15/2020 se místo toho vrátí kód chyby 400. 

V v3 rozhraní API se změnila následující omezení. Překročení níže uvedených omezení vygeneruje upozornění v odpovědi rozhraní API.


| Funkce | Maximální počet dokumentů na požadavek | 
|----------|-----------|
| Rozpoznávání jazyka | 1000 |
| Analýza mínění | 10 |
| Extrakce klíčových frází | 10 |
| Rozpoznávání pojmenovaných entit | 5 |
| Entity Linking | 5 |

#### <a name="version-2"></a>[Verze 2](#tab/version-2)

| Funkce | Maximální počet dokumentů na požadavek | 
|----------|-----------|
| Rozpoznávání jazyka | 1000 |
| Analýza mínění | 1000 |
| Extrakce klíčových frází | 1000 |
| Rozpoznávání pojmenovaných entit | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>Omezení rychlosti

Vaše omezení četnosti se bude lišit od [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Tato omezení jsou stejná pro obě verze rozhraní API.

| Úroveň          | Počet žádostí za sekundu | Žádosti za minutu |
|---------------|---------------------|---------------------|
| S/více službami | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Požadavky se měří pro každou funkci Analýza textu samostatně. Můžete například odeslat maximální počet požadavků pro vaši cenovou úroveň všem funkcím ve stejnou dobu.  


## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu](../overview.md)
* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
