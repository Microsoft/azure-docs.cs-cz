---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174816"
---
### <a name="cacheskuname"></a>cacheSKUName

Cenová úroveň nové mezipaměti Azure pro Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (Základní, Standardní nebo Premium), a přiřadí výchozí hodnotu (Basic), pokud není zadána žádná hodnota. Basic poskytuje jeden uzel s více velikostmi, které jsou k dispozici až do velikosti 53 GB. Standard poskytuje primární nebo repliku se dvěma uzly s více velikostmi, které jsou k dispozici až do velikosti 53 GB a 99,9% s la.

### <a name="cacheskufamily"></a>cacheSKURodina

Rodina pro sku.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUKapacita

Velikost nové instance Azure Cache for Redis.

Pro základní a standardní rodiny:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Kapacita mezipaměti hodnot Premium je definována stejně, s výjimkou povolených hodnot spuštěných od 1 do 5 namísto od 0 do 6.

Šablona definuje celé hodnoty, které jsou povoleny pro tento parametr (0 až 6 pro základní a standardní rodiny; 1 až 5 pro premium rodiny). Pokud není zadána žádná hodnota, šablona přiřadí výchozí hodnotu 0 pro základní a standardní 1 pro Premium.

Hodnoty odpovídají následujícím velikostem mezipaměti:

| Hodnota | Základní a standardní<br>velikost mezipaměti | Premium<br>velikost mezipaměti |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (výchozí)                 | neuvedeno                   |
| 1     | 1 GB                             | 6 GB (výchozí)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | neuvedeno                   |
