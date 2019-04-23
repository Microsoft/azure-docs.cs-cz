---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118482"
---
### <a name="cacheskuname"></a>cacheSKUName

Cenová úroveň nové mezipaměti Redis Azure.

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

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (Basic, Standard nebo Premium) a přiřadí výchozí hodnotu (Basic), pokud není zadána žádná hodnota. Basic poskytuje jeden uzel s více velikostí až do 53 GB. Standard poskytuje dva uzly primární/replika s více velikostí až do 53 GB a 99,9 % smlouva SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Řada pro danou skladovou jednotku.

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

Velikost nové mezipaměti Azure pro Redis instance.

Basic a Standard rodin:

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

Kapacity Premium hodnoty mezipaměti je definovaná stejná, s výjimkou povolené hodnoty spuštění od 1 do 5 místo od 0 do 6.

Šablona definuje celočíselných hodnot, které jsou povoleny pro tento parametr (0 až 6 pro řady Basic a Standard; 1 až 5 pro řadu Premium). Pokud není zadána žádná hodnota, šablona přiřadí výchozí hodnotu 0 u úrovní Basic a Standard, Premium 1.

Hodnoty odpovídají následující velikosti mezipaměti:

| Value | Basic a Standard<br>Velikost mezipaměti | Premium<br>Velikost mezipaměti |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (výchozí)                 | neuvedeno                   |
| 1     | 1 GB                             | 6 GB (výchozí)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | neuvedeno                   |
