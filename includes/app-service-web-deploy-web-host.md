---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050207"
---
### <a name="app-service-plan"></a>Plán služby App Service
Vytvoří plán služby pro hostování webové aplikace. Název plánu zadáte pomocí parametru **hostingPlanName** . Umístění plánu je stejné jako umístění, které se používá pro skupinu prostředků. Cenová úroveň a velikost pracovního procesu se zadává v parametrech **SKU** a **workerSize** .

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
