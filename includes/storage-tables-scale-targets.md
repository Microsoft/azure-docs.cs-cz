---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457282"
---
| Resource | Target |
|----------|---------------|
| Maximální velikost jedné tabulky | 500 TiB |
| Maximální velikost entity tabulky | 1 MiB |
| Maximální počet vlastností v entitě tabulky | 255, který zahrnuje tři systémové vlastnosti: PartitionKey, RowKey a timestamp |
| Maximální celková velikost hodnot vlastností v entitě | 1 MiB |
| Maximální celková velikost jednotlivé vlastnosti v entitě | Liší se podle typu vlastnosti. Další informace najdete v tématu **typy vlastností** v tématu [Principy datového modelu služby Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maximální počet uložených zásad přístupu na tabulku | 5 |
| Maximální počet požadavků na účet úložiště | 20 000 transakcí za sekundu, což předpokládá 1 – KiB velikost entity |
| Cílová propustnost pro oddíl s jednou tabulkou (1 KiB-Entities) | Až 2 000 entit za sekundu |