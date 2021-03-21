---
title: Úrovně výkonu pro Azure Managed disks
description: Přečtěte si o úrovních výkonu pro spravované disky.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677315"
---
# <a name="performance-tiers-for-managed-disks"></a>Úrovně výkonu pro spravované disky

Výkon spravovaného disku Azure se nastaví při vytváření disku ve formě jeho úrovně výkonu. Úroveň výkonu určuje IOPS a propustnost vašeho spravovaného disku. Když nastavíte zřízenou velikost disku, automaticky se vybere úroveň výkonu. Úroveň výkonu lze změnit při nasazení nebo následně, aniž by došlo ke změně velikosti disku.

Změna úrovně výkonu vám umožní připravovat a vyhovět vyšší poptávce bez použití možnosti rozkladu vašeho disku. V závislosti na tom, jak dlouho je potřeba další výkon, může být cenově výhodnější změnit úroveň výkonu, ale nespoléhat na shluking. To je ideální pro události, které dočasně vyžadují trvale vyšší úroveň výkonu, jako je prázdninové nákupy, testování výkonu nebo spouštění školicích prostředí. Pro zpracování těchto událostí můžete použít vyšší úroveň výkonu, pokud ji potřebujete. Pak se můžete vrátit do původní úrovně, pokud už nebudete potřebovat další výkon.

## <a name="how-it-works"></a>Jak to funguje

Při prvním nasazení nebo zřízení disku je základní úroveň výkonu pro tento disk nastavena na základě zřízené velikosti disku. Úroveň výkonu vyšší než původní standardní hodnoty můžete použít pro splnění vyšší poptávky. Když už tuto úroveň výkonu nepotřebujete, můžete se vrátit na počáteční základní úroveň výkonu.

Vaše fakturace se mění při změně úrovně výkonu. Pokud například zřídíte disk P10 (128 GiB), úroveň výkonu základní úrovně se nastaví jako P10 (500 IOPS a 100 MB/s). Bude se vám účtovat sazba za P10. Vrstvu můžete upgradovat tak, aby odpovídala výkonu P50 (7 500 IOPS a 250 MB/s), aniž by se zvýšila velikost disku. Během upgradu se vám bude účtovat sazba za P50. Pokud už vyšší výkon nepotřebujete, můžete se vrátit na úroveň P10. Na disk se bude znovu účtovat P10 sazba.

| Velikost disku | Základní úroveň výkonu | Dá se upgradovat na |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Žádné |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TB | P80 | Žádné |

Informace o fakturaci najdete v tématu [ceny za spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak změnit úroveň výkonu, najdete v článcích [portál](disks-performance-tiers-portal.md) nebo [PowerShell/CLI](disks-performance-tiers.md) .

