---
title: Azure nadřazený prostředek chyby | Dokumentace Microsoftu
description: Popisuje, jak řešení chyb při práci se nadřazený prostředek.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 3042ea1a523f12ae0311545a1b9bc67306f266dd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447298"
---
# <a name="resolve-errors-for-parent-resources"></a>Řešení chyb pro nadřazené prostředky

Tento článek popisuje, chyby, které může zobrazit při nasazování prostředek, který je závislý na nadřazený prostředek.

## <a name="symptom"></a>Příznak

Při nasazování prostředků, který je podřízený jinému zdroji, zobrazí se následující chybová zpráva:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Příčina

Pokud jeden prostředek je podřízený jinému prostředku, musí existovat nadřazený prostředek před vytvořením podřízený prostředek. Název prostředku podřízené definuje připojení s nadřazeným prostředkem. Název podřízeného prostředku je ve formátu `<parent-resource-name>/<child-resource-name>`. SQL Database může například definovat jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Pokud nasazení serveru a databáze ve stejné šabloně, ale nezadávejte závislost na serveru, může spustit nasazení databáze předtím, než je nasazen na serveru. 

Pokud se nadřazený prostředek už existuje a není nasazený ve stejné šabloně, dostanete, když správce prostředků nelze propojit s nadřazenou podřízený prostředek k této chybě. K této chybě může dojít, pokud podřízený prostředek není ve správném formátu, nebo podřízený prostředek je nasazený pro skupinu prostředků, která se liší od skupiny prostředků pro nadřazený prostředek.

## <a name="solution"></a>Řešení

Chcete-li vyřešit tuto chybu, když nadřazené a podřízené prostředky se nasadí do stejné šablony, zahrnují závislost.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Chcete-li vyřešit tuto chybu, pokud se nadřazený prostředek byl dříve nasazen do jiné šablony, nenastavíte závislost. Místo toho nasadit podřízené do stejné skupiny prostředků a zadejte název nadřazený prostředek.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Další informace najdete v tématu [definovat pořadí pro nasazení prostředků do šablon Azure Resource Manageru](resource-group-define-dependencies.md).