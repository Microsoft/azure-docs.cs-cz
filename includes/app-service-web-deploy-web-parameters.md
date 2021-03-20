---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050201"
---
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem parametry, který obsahuje všechny hodnoty parametrů.
Měli byste definovat parametr pro tyto hodnoty, které se budou lišit v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které budou vždycky zůstat stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

Při definování parametrů použijte pole **allowedValues** k určení, které hodnoty může uživatel poskytnout během nasazování. Pokud není během nasazení zadaná žádná hodnota, použijte pole **DefaultValue** k přiřazení hodnoty k parametru.

Popíšeme každý parametr v šabloně.

### <a name="sitename"></a>Názvem
Název webové aplikace, kterou chcete vytvořit.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
Název App Serviceho plánu, který se má použít pro hostování webové aplikace.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>skladové
Cenová úroveň plánu hostování.

```config
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
```

Šablona definuje hodnoty, které jsou povoleny pro tento parametr, a přiřadí výchozí hodnotu (S1), pokud není zadána žádná hodnota.

### <a name="workersize"></a>workerSize
Velikost instance plánu hostování (malá, střední nebo velká).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1 nebo 2), a přiřadí výchozí hodnotu (0), pokud není zadána žádná hodnota. Hodnoty odpovídají malým, střednímu a velkému.

