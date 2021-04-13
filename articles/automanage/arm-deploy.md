---
title: Připojení počítače k Azure automanage se šablonou ARM
description: Naučte se, jak pomocí šablony Azure Resource Manager připojit počítač k Azure automanage.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368592"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Připojte počítač k autosprávě pomocí šablony Azure Resource Manager (ARM).


## <a name="overview"></a>Přehled
Podle následujícího postupu připojte počítač k autospravovat osvědčené postupy. V níže uvedené šabloně ARM se vytvoří `configurationProfileAssignment` objekt, který je prostředkem Azure, který představuje počítač, který se připojil k autosprávě.

## <a name="prerequisites"></a>Požadavky
* Je nutné, abyste vytvořili existující účet pro správu. Další informace o účtu pro správu a jak ho vytvořit najdete v [tomto dokumentu](./automanage-account.md) .
* Pro skupinu prostředků, která obsahuje počítače, které chcete připojit k automatizované správě, musíte mít roli **Přispěvatel** .

## <a name="arm-template-overview"></a>Přehled šablon ARM
Následující šablona ARM zařadí zadaný počítač do Azure automanage osvědčené postupy. Podrobnosti o šabloně ARM a krocích k nasazení najdete v části nasazení šablony ARM [níže](#arm-template-deployment).
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Nasazení šablony ARM
Výše uvedená šablona ARM vytvoří přiřazení konfiguračního profilu pro zadaný počítač pomocí zadaného účtu pro správu. Pokud jste ještě nevytvořili účet pro správu, další informace najdete v [tomto dokumentu](./automanage-account.md).

`configurationProfileAssignment`Hodnota může být jedna z následujících hodnot:
* Produkční
* DevTest

Pomocí těchto kroků nasaďte šablonu ARM:
1. Uložit níže uvedenou šablonu ARM jako `azuredeploy.json`
1. Spustit nasazení šablony ARM s `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Po zobrazení výzvy zadejte hodnoty pro machineName, automanageAccountName a configurationProfileAssignment.
1. Jste hotovi!

Stejně jako u všech šablon ARM je možné rozložit parametry do samostatného `azuredeploy.parameters.json` souboru a použít je jako argument při nasazení.

## <a name="next-steps"></a>Další kroky
Další informace o autosprávě pro [Linux](./automanage-linux.md) a [Windows](./automanage-windows-server.md)