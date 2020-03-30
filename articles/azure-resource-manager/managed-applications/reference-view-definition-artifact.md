---
title: Odkaz na artefakt definice zobrazení
description: Poskytuje příklad artefaktu definice zobrazení pro spravované aplikace Azure. Název souboru je viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651199"
---
# <a name="reference-view-definition-artifact"></a>Odkaz: Zobrazit artefakt definice

Tento článek je odkazem na artefakt *viewDefinition.json* ve spravovaných aplikacích Azure. Další informace o konfiguraci vytváření zobrazení naleznete v tématu [Zobrazení artefaktu definice](concepts-view-definition.md).

## <a name="view-definition"></a>Zobrazení definice

Následující JSON ukazuje příklad souboru *viewDefinition.json* pro spravované aplikace Azure:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření spravované aplikace s vlastními akcemi a prostředky](tutorial-create-managed-app-with-custom-provider.md)
- [Odkaz: Artefakt prvků uživatelského rozhraní](reference-createuidefinition-artifact.md)
- [Odkaz: Artefakt šablony nasazení](reference-main-template-artifact.md)