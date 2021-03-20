---
title: Artefakt CreateUiDefinition
description: Ukazuje, jak vytvořit artefakt createUiDefinition pro spravovanou aplikaci Azure. Soubor má název createUiDefinition.jsv.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651485"
---
# <a name="reference-user-interface-elements-artifact"></a>Referenční dokumentace: artefakt prvků uživatelského rozhraní

Tento článek je odkazem na *createUiDefinition.js* artefaktu v Azure Managed Applications. Další informace o vytváření prvků uživatelského rozhraní najdete v tématu [vytvoření prvků uživatelského rozhraní](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Prvky uživatelského rozhraní

Následující JSON ukazuje příklad *createUiDefinition.js* souboru pro Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření spravované aplikace s vlastními akcemi a prostředky](tutorial-create-managed-app-with-custom-provider.md)
- [Referenční dokumentace: artefakt šablony nasazení](reference-main-template-artifact.md)
- [Reference: artefakt definice zobrazení](reference-view-definition-artifact.md)