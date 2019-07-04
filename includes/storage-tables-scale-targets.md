---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457282"
---
| Resource | Target |
|----------|---------------|
| Maximální velikost jedné tabulky | 500 TiB |
| Maximální velikost entitu tabulky | 1 MiB |
| Maximální počet vlastností v entitě tabulky | 255, která zahrnuje tři systémové vlastnosti: PartitionKey, RowKey a Timestamp |
| Maximální celková velikost hodnot vlastností v entitě. | 1 MiB |
| Maximální celková velikost jednotlivých vlastností v entitě. | Se liší podle typu vlastnosti. Další informace najdete v tématu **typy vlastností** v [Principy datového modelu služby Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maximální počet uložené zásady přístupu na tabulku | 5 |
| Maximální frekvence požadavků na účet úložiště | 20 000 transakcí za sekundu, což předpokládá, že velikost entity 1 KiB |
| Nastavte propustnost pro oddíl jedné tabulky (1-KiB entity) | Až 2 000 entity za sekundu |