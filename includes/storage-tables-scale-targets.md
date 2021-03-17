---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78940956"
---
Následující tabulka popisuje kapacitu, škálovatelnost a cíle výkonnosti pro úložiště tabulek.

| Prostředek | Cíl |
|----------|---------------|
| Počet tabulek v účtu služby Azure Storage | Omezené jenom na kapacitu účtu úložiště |
| Počet oddílů v tabulce | Omezené jenom na kapacitu účtu úložiště |
| Počet entit v oddílu | Omezené jenom na kapacitu účtu úložiště |
| Maximální velikost jedné tabulky | 500 TiB |
| Maximální velikost jedné entity, včetně všech hodnot vlastností | 1 MiB |
| Maximální počet vlastností v entitě tabulky | 255 (včetně tří systémových vlastností, **PartitionKey**, **RowKey**a **časového razítka**) |
| Maximální celková velikost jednotlivé vlastnosti v entitě | Liší se podle typu vlastnosti. Další informace najdete v tématu **typy vlastností** v tématu [Principy datového modelu služby Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Velikost **PartitionKey** | Řetězec o velikosti až 1 KiB |
| Velikost **RowKey** | Řetězec o velikosti až 1 KiB |
| Velikost transakce skupiny entit | Transakce může zahrnovat maximálně 100 entit a datová část musí být menší než 4 velikost souboru MiB. Transakce skupiny entit může zahrnovat aktualizaci entity pouze jednou. |
| Maximální počet uložených zásad přístupu na tabulku | 5 |
| Maximální počet požadavků na účet úložiště | 20 000 transakcí za sekundu, což předpokládá 1 – KiB velikost entity |
| Cílová propustnost pro oddíl s jednou tabulkou (1 KiB-Entities) | Až 2 000 entit za sekundu |