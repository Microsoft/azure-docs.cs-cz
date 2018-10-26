---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132887"
---
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem parametry, které obsahuje všechny hodnoty parametrů.
Byste měli definovat parametr pro tyto hodnoty, které se liší podle projektu, které nasazujete nebo podle prostředí, které nasazujete. Nedefinujte parametry pro hodnoty, které zůstane vždy stejný. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

Při definování parametrů, použijte **allowedValues** pole k určení, jaké hodnoty uživatele můžete během nasazení zadat. Použití **defaultValue** pole pro přiřazení hodnoty na parametr, pokud se nezadá žádná hodnota během nasazení.

Popíšeme každý parametr v šabloně.

### <a name="sitename"></a>Název webu
Název webové aplikace, kterou chcete vytvořit.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Název plánu služby App Service pro hostování webové aplikace.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>Skladová položka
Cenová úroveň pro plán hostování.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Šablona definuje hodnoty, které jsou povoleny pro tento parametr a přiřadí výchozí hodnotu (S1), pokud není zadána žádná hodnota.

### <a name="workersize"></a>workerSize
Velikost instance plánu hostování (malá, střední nebo velké).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1 nebo 2) a přiřadí výchozí hodnotu (0), pokud není zadána žádná hodnota. Hodnoty odpovídají malé, střední a velké.

