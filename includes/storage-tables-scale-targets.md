---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78940956"
---
Následující tabulka popisuje kapacitu, škálovatelnost a výkonnostní cíle pro úložiště tabulek.

| Prostředek | Cíl |
|----------|---------------|
| Počet tabulek v účtu úložiště Azure | Omezeno pouze kapacitou účtu úložiště |
| Počet oddílů v tabulce | Omezeno pouze kapacitou účtu úložiště |
| Počet entit v oddílu | Omezeno pouze kapacitou účtu úložiště |
| Maximální velikost jedné tabulky | 500 TiB |
| Maximální velikost jedné entity včetně všech hodnot vlastností | 1 MiB |
| Maximální počet vlastností v entitě tabulky | 255 (včetně tří vlastností systému **PartitionKey**, **RowKey**a **Timestamp)** |
| Maximální celková velikost jednotlivé nemovitosti v účetní jednotce | Liší se podle typu vlastnosti. Další informace naleznete v **tématu Typy vlastností** v [tématu Principy datového modelu služby Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Velikost **klíče PartitionKey** | Řetězec až 1 KiB ve velikosti |
| Velikost **řádku klíč** | Řetězec až 1 KiB ve velikosti |
| Velikost transakce skupiny účetní jednotky | Transakce může zahrnovat maximálně 100 entit a datová část musí být menší než 4 MiB ve velikosti. Transakce skupiny entit může zahrnovat aktualizaci entity pouze jednou. |
| Maximální počet uložených zásad přístupu na tabulku | 5 |
| Maximální sazba požadavku na účet úložiště | 20 000 transakcí za sekundu, což předpokládá velikost entity 1-KiB |
| Cílová propustnost pro jeden oddíl tabulky (1 entity KiB) | Až 2 000 entit za sekundu |