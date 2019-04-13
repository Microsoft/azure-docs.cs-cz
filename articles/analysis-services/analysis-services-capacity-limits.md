---
title: Omezení nástroje prostředku a objekt služby Azure Analysis Services | Dokumentace Microsoftu
description: Popisuje omezení prostředků a objektu služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527808"
---
# <a name="analysis-services-resource-and-object-limits"></a>Omezení prostředků a objektu služby analýzy

Tento článek popisuje zdroje a model objektu omezení.

## <a name="tier-limits"></a>Úroveň omezení

### <a name="developer-tier"></a>Úroveň Developer

Tato úroveň se doporučuje pro scénáře testování, vývoje a vyhodnocení. Jeden plán zahrnuje stejné funkce jako úroveň Standard, ale s omezením výkonu, jednotek QPU a velikosti paměti. Škálování dotazů repliky není k dispozici pro tuto vrstvu. Tato úroveň nenabízí smlouvu SLA.

|Plánování  |Jednotky QPU  |Paměť (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Úroveň Basic

Tato úroveň se doporučuje pro produkční řešení s menšími tabulkovými modely, omezenou souběžností uživatelů a jednoduchými požadavky na obnovení dat. Horizontální navýšení kapacity replik dotazů *není k dispozici* pro tuto vrstvu. Perspektivy, více oddílů a DirectQuery tabulkový model funkce nejsou podporovány na této úrovni.  

|Plánování  |Jednotky QPU  |Paměť (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Úroveň Standard

Tato úroveň je pro důležité produkční aplikace, které vyžadují elastickou uživatelskou souběžnost a využívají datové modely s rychlým růstem. Podporuje pokročilou aktualizaci dat pro aktualizace datových modelů téměř v reálném čase a podporuje veškeré funkce tabulkového modelování.

|Plánování  |Jednotky QPU  |Paměť (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Není dostupné ve všech oblastech.  

## <a name="object-limits"></a>Omezení objektu

Tato omezení jsou teoretické. Výkon se snížila na nižší čísla.

|Objekt|Maximální velikosti/čísla|  
|------------|----------------------------|  
|Databáze v instanci|16,000|  
|Souhrnný počet tabulky a sloupce v databázi|16,000|  
|Řádky v tabulce|Unlimited<br /><br /> **Upozornění:** S omezením, že žádné jeden sloupec v tabulce můžete mít více než 1,999,999,997 jedinečné hodnoty.|  
|Hierarchie v tabulce|15,999|  
|Úrovně v hierarchii|15,999|  
|Relace|8 000|  
|Sloupce klíčů všechny tabulky|15,999|  
|Míry v tabulkách|2^31-1 = 2,147,483,647|  
|Buňky vrácených dotazem|2^31-1 = 2,147,483,647|  
|Velikost záznamu zdroj dotazu|64 K|  
|Délka názvů objektů|512 znaků|  


