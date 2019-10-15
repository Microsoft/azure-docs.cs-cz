---
title: Referenční informace o artefaktu definice zobrazení spravované aplikace Azure
description: Poskytuje příklad artefaktu definice zobrazení pro Azure Managed Applications. Název souboru je položku galerie. JSON.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 1f56f6c9c519bd29423d92a8dc8b8ce5904b523c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332634"
---
# <a name="reference-view-definition-artifact"></a>Reference: artefakt definice zobrazení

Tento článek je odkazem na artefakt *položku galerie. JSON* v Azure Managed Applications. Další informace o konfiguraci zobrazení pro vytváření obsahu najdete v tématu [artefakt definice zobrazení](concepts-view-definition.md).

## <a name="view-definition"></a>Zobrazení definice

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

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření spravované aplikace s vlastními akcemi a prostředky](tutorial-create-managed-app-with-custom-provider.md)
- [Referenční dokumentace: artefakt prvků uživatelského rozhraní](reference-createuidefinition-artifact.md)
- [Referenční dokumentace: artefakt šablony nasazení](reference-main-template-artifact.md)