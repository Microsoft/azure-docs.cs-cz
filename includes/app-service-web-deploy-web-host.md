---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67174814"
---
### <a name="app-service-plan"></a>Plán služby App Service
Vytvoří plán služby pro hostování webové aplikace. Název plánu zadáte pomocí parametru **hostingPlanName** . Umístění plánu je stejné jako umístění, které se používá pro skupinu prostředků. Cenová úroveň a velikost pracovního procesu se zadává v parametrech **SKU** a **workerSize** .

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

