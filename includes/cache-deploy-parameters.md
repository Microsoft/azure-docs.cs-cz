---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
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

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (Basic, Standard nebo Premium), a přiřadí výchozí hodnotu (Basic), pokud není zadána žádná hodnota. Základní poskytuje jeden uzel s více velikostmi dostupnými až 53 GB. Standard nabízí primární nebo repliku dvou uzlů s více velikostmi, které jsou dostupné až do 53 GB a 99,9% smlouvy SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Rodina pro skladovou položku.

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

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Velikost nové mezipaměti Azure pro instanci Redis

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

Kapacita mezipaměti hodnoty Premium je definována stejně, s výjimkou povolených hodnot, které jsou spouštěny od 1 do 5 místo od 0 do 6.

Šablona definuje celočíselné hodnoty, které jsou povoleny pro tento parametr (0 až 6 pro rodiny Basic a Standard: 1 až 5 pro rodinu Premium). Pokud není zadána žádná hodnota, šablona přiřadí výchozí hodnotu 0 pro Basic a Standard, 1 pro prémii.

Hodnoty odpovídají následujícím velikostem mezipaměti:

| Hodnota | Basic a Standard<br>velikost mezipaměti | Premium<br>velikost mezipaměti |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (výchozí)                 | Není k dispozici                   |
| 1     | 1 GB                             | 6 GB (výchozí)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | Není k dispozici                   |
