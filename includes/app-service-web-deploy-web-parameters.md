---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67174815"
---
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem parametry, který obsahuje všechny hodnoty parametrů.
Měli byste definovat parametr pro tyto hodnoty, které se budou lišit v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které budou vždycky zůstat stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

Při definování parametrů použijte pole **allowedValues** k určení, které hodnoty může uživatel poskytnout během nasazování. Pokud není během nasazení zadaná žádná hodnota, použijte pole **DefaultValue** k přiřazení hodnoty k parametru.

Popíšeme každý parametr v šabloně.

### <a name="sitename"></a>Názvem
Název webové aplikace, kterou chcete vytvořit.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Název App Serviceho plánu, který se má použít pro hostování webové aplikace.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>skladové
Cenová úroveň plánu hostování.

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

Šablona definuje hodnoty, které jsou povoleny pro tento parametr, a přiřadí výchozí hodnotu (S1), pokud není zadána žádná hodnota.

### <a name="workersize"></a>workerSize
Velikost instance plánu hostování (malá, střední nebo velká).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Šablona definuje hodnoty, které jsou povoleny pro tento parametr (0, 1 nebo 2), a přiřadí výchozí hodnotu (0), pokud není zadána žádná hodnota. Hodnoty odpovídají malým, střednímu a velkému.

