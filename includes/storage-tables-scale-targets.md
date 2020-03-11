---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78940956"
---
Následující tabulka popisuje kapacitu, škálovatelnost a cíle výkonnosti pro Table Storage.

| Prostředek | Cíl |
|----------|---------------|
| Počet tabulek v účtu Azure Storage | Omezené jenom kapacitou účtu úložiště |
| Počet oddílů v tabulce | Omezené jenom kapacitou účtu úložiště |
| Počet entit v oddílu | Omezené jenom kapacitou účtu úložiště |
| Maximální velikost jedné tabulky | 500 TiB |
| Maximální velikost jedné entity, včetně všech hodnot vlastností | 1 MiB |
| Maximální počet vlastností v entitě tabulky | 255 (včetně tří systémových vlastností – **PartitionKey**, **RowKey** a **Timestamp**) |
| Maximální celková velikost jednotlivé vlastnosti v entitě | Liší se podle typu vlastnosti. Další informace najdete v tématu **Typy vlastností** v článku [Vysvětlení datového modelu služby Table Storage](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Velikost **PartitionKey** | Řetězec o velikosti až 1 KiB |
| Velikost **RowKey** | Řetězec o velikosti až 1 KiB |
| Velikost transakce skupiny entit | Transakce může zahrnovat maximálně 100 entit a velikost datové části musí být menší než 4 MiB. Transakce skupiny entit může aktualizaci entity zahrnovat pouze jednou. |
| Maximální počet uložených zásad přístupu na tabulku | 5 |
| Maximální frekvence požadavků na účet úložiště | 20 000 transakcí za sekundu (předpokládá se velikost entit 1 KiB) |
| Cílová propustnost pro oddíl s jednou tabulkou (entity 1 KiB) | Až 2 000 entit za sekundu |