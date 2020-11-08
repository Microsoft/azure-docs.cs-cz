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
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 905dde6932afb440c34bcccb563bfda98f23eb7c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363829"
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
| Maximální velikost jednoho dokumentu | 5 120 znaků měřených pomocí [StringInfo. lengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Vztahuje se také na Analýza textu pro kontejner Health. |
| Maximální velikost celého požadavku | 1 MB. Vztahuje se také na Analýza textu pro kontejner Health. |

Maximální počet dokumentů, které můžete poslat v rámci jedné žádosti, bude záviset na verzi rozhraní API a funkci, kterou používáte.

#### <a name="version-3"></a>[Verze 3](#tab/version-3)

V v3 rozhraní API se změnila následující omezení. Překročení následujících omezení vygeneruje kód chyby HTTP 400.


| Příznak | Maximální počet dokumentů na požadavek | 
|----------|-----------|
| Rozpoznávání jazyka | 1000 |
| Analýza mínění | 10 |
| Extrakce klíčových frází | 10 |
| Rozpoznávání pojmenovaných entit | 5 |
| Entity Linking | 5 |
| Analýza textu pro kontejner stavu | 1000 |
#### <a name="version-2"></a>[Verze 2](#tab/version-2)

| Příznak | Maximální počet dokumentů na požadavek | 
|----------|-----------|
| Rozpoznávání jazyka | 1000 |
| Analýza mínění | 1000 |
| Extrakce klíčových frází | 1000 |
| Rozpoznávání pojmenovaných entit | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>Omezení přenosové rychlosti

Vaše omezení četnosti se bude lišit od [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Tato omezení jsou stejná pro obě verze rozhraní API. Tyto limity přenosové rychlosti se nevztahují na Analýza textu pro kontejner stavů, které nemají nastavenou omezení četnosti.

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