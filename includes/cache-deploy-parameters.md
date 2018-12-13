---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111707"
---
### <a name="cacheskuname"></a>cacheSKUName
Cenová úroveň nové mezipaměti Redis Azure.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (Basic nebo Standard) a přiřadí výchozí hodnotu (Basic), pokud není zadána žádná hodnota. Basic poskytuje jeden uzel s více velikostí až do 53 GB.
Standard poskytuje dva uzly primární/replika s více velikostí až do 53 GB a 99,9 % smlouva SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily
Řada pro danou skladovou jednotku.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Velikost nové mezipaměti Azure pro Redis instance. 

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


Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1, 2, 3, 4, 5 nebo 6) a přiřadí výchozí hodnotu (0), pokud není zadána žádná hodnota. Tato čísla odpovídají následující velikosti mezipaměti: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

