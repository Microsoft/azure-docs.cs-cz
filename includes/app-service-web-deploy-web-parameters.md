---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174815"
---
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem Parametry, který obsahuje všechny hodnoty parametrů.
Měli byste definovat parametr pro tyto hodnoty, které se budou lišit v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které zůstanou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

Při definování parametrů použijte pole **allowedValues** k určení hodnot, které může uživatel poskytnout během nasazení. Pole **defaultValue** slouží k přiřazení hodnoty parametru, pokud během nasazení není k dispozici žádná hodnota.

Popíšeme každý parametr v šabloně.

### <a name="sitename"></a>Sitename
Název webové aplikace, kterou chcete vytvořit.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingNázev_plánu
Název plánu služby App Service, který se má použít pro hostování webové aplikace.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>Sku
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

Šablona definuje hodnoty, které jsou pro tento parametr povoleny, a přiřadí výchozí hodnotu (S1), pokud není zadána žádná hodnota.

### <a name="workersize"></a>workerSize
Velikost instance plánu hostování (malé, střední nebo velké).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1 nebo 2), a přiřadí výchozí hodnotu (0), pokud není zadána žádná hodnota. Hodnoty odpovídají malým, středním a velkým.

