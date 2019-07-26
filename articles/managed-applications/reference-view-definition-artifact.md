---
title: Referenční informace o artefaktu definice zobrazení spravované aplikace Azure
description: Tento článek je odkazem na artefakt definice zobrazení.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: e60f26fe0a7144d768bac020d62c61cb92594914
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336540"
---
# <a name="reference-view-definition-artifact"></a>Další informace: Zobrazení definičních artefaktů

Tento článek je odkazem na artefakt *položku galerie. JSON* v Azure Managed Applications. Další informace o konfiguraci zobrazení pro vytváření obsahu najdete v tématu [artefakt definice zobrazení](concepts-view-definition.md).

## <a name="view-definition"></a>Zobrazit definici

Následující JSON ukazuje příklad souboru *položku galerie. JSON* pro Azure Managed Applications:

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

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření spravované aplikace s vlastními akcemi a prostředky](tutorial-create-managed-app-with-custom-provider.md)
- [Odkaz Artefakt prvků uživatelského rozhraní](reference-createuidefinition-artifact.md)
- [Odkaz Artefakt šablony nasazení](reference-main-template-artifact.md)