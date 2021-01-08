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
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 2adca03a820d02731bca252dee99c76debc85e2e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028127"
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
| Maximální velikost jednoho dokumentu | 5 120 znaků měřených pomocí [StringInfo. lengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Platí také pro Analýza textu pro stav. |
| Maximální velikost jednoho dokumentu ( `/analyze` koncový bod)  | 125K znaky měřené [StringInfo. lengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Neplatí pro Analýza textu pro stav. |
| Maximální velikost celého požadavku | 1 MB. Platí také pro Analýza textu pro stav. |


Pokud dokument překročí limit znaků, rozhraní API se bude chovat různě v závislosti na koncovém bodu, který používáte:

* `/analyze` Služba
  * Rozhraní API zamítne celý požadavek a vrátí chybu, `400 bad request` Pokud v něm dojde k překročení maximální velikosti dokumentu.
* Všechny ostatní koncové body:  
  * Rozhraní API nezpracuje dokument, který překračuje maximální velikost, a vrátí pro něj neplatnou chybu dokumentu. Pokud má požadavek rozhraní API více dokumentů, rozhraní API bude pokračovat ve zpracování, pokud jsou v rámci limitu znaků.

Maximální počet dokumentů, které můžete poslat v rámci jedné žádosti, bude záviset na verzi rozhraní API a funkci, kterou používáte, což je popsáno v následující tabulce.

#### <a name="version-3"></a>[Verze 3](#tab/version-3)

Následující omezení jsou pro aktuální rozhraní API v3. Překročení následujících omezení vygeneruje kód chyby HTTP 400.


| Příznak | Maximální počet dokumentů na požadavek | 
|----------|-----------|
| Rozpoznávání jazyka | 1000 |
| Analýza mínění | 10 |
| Dolování názoru | 10 |
| Extrakce klíčových frází | 10 |
| Rozpoznávání pojmenovaných entit | 5 |
| Entity Linking | 5 |
| Analýza textu pro zdravotnictví  | 10 pro webové rozhraní API, 1000 pro kontejner. |
| Analyzovat koncový bod | 25 pro všechny operace. |

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

Frekvence požadavků se měří pro každou funkci Analýza textu samostatně. Do každé funkce můžete současně poslat maximální počet požadavků pro vaši cenovou úroveň. Pokud jste například v rámci `S` vrstvy a odesílali požadavky 1000 najednou, nebudete moci poslat další žádost na 59 sekund.


## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu](../overview.md)
* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
