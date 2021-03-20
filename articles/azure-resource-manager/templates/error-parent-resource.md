---
title: Chyby nadřazeného prostředku
description: Popisuje, jak vyřešit chyby při práci s nadřazeným prostředkem v šabloně Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84673656"
---
# <a name="resolve-errors-for-parent-resources"></a>Řešení chyb pro nadřazené prostředky

Tento článek popisuje chyby, ke kterým může dojít při nasazování prostředku, který je závislý na nadřazeném prostředku.

## <a name="symptom"></a>Příznak

Při nasazování prostředku, který je podřízený jinému prostředku, se může zobrazit následující chyba:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Příčina

Pokud je jeden prostředek podřízený jinému prostředku, musí před vytvořením podřízeného prostředku existovat nadřazený prostředek. Název podřízeného prostředku definuje připojení s nadřazeným prostředkem. Název podřízeného prostředku je ve formátu `<parent-resource-name>/<child-resource-name>` . SQL Database například může být definován jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Pokud nasadíte Server i databázi do stejné šablony, ale nezadáte závislost na serveru, může se nasazení databáze spustit před nasazením serveru.

Pokud nadřazený prostředek už existuje a není nasazený ve stejné šabloně, zobrazí se tato chyba, když Správce prostředků nemůže přidružit podřízený prostředek k nadřazenému. K této chybě může dojít, když podřízený prostředek není ve správném formátu nebo je podřízený prostředek nasazený do skupiny prostředků, která se liší od skupiny prostředků pro nadřazený prostředek.

## <a name="solution"></a>Řešení

Chcete-li tuto chybu vyřešit při nasazení nadřazených a podřízených prostředků do stejné šablony, zahrňte závislost.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Chcete-li tuto chybu vyřešit, pokud byl nadřazený prostředek dříve nasazen v jiné šabloně, nenastavíte závislost. Místo toho nasaďte podřízenou položku do stejné skupiny prostředků a zadejte název nadřazeného prostředku.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Další informace najdete v tématu [Definování pořadí pro nasazení prostředků v šablonách Azure Resource Manager](define-resource-dependency.md).