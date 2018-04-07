---
title: Vynutit zabezpečení se zásadami na virtuální počítače s Linuxem v Azure | Microsoft Docs
description: Tom, jak používat zásady pro správce prostředků Linux virtuální počítač Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 12066fe622ec3ed2eded74ecf7b791689ed873d5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Zásady použít pro virtuální počítače s Linuxem pomocí Azure Resource Manageru
Pomocí zásad můžete vynutit organizaci různé konvence a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při přispívání do úspěch organizace. V tomto článku jsme popisují, jak lze pomocí Azure Resource Manager zásad můžete určit požadované chování pro virtuální počítače vaší organizace.

Úvod do zásady, najdete v části [co je Azure zásad?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Povolené virtuální počítače
Zajistit, že virtuální počítače ve vaší organizaci, jsou kompatibilní s aplikací, můžete omezit povolených operační systémy. V následujícím příkladu zásady Povolit pouze Ubuntu 14.04.2-LTS virtuální počítače, který se má vytvořit.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

K úpravě předchozí zásada umožnit žádný obrázek Ubuntu LTS použijte zástupný znak: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Informace o polí zásad najdete v tématu [zásad aliasy](../../azure-policy/policy-definition.md#aliases).

## <a name="managed-disks"></a>Managed Disks

Pokud chcete vyžadovat použití spravovaných disků, použijte tyto zásady:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Bitové kopie u virtuálních počítačů

Z bezpečnostních důvodů se může vyžadovat, aby byly nasazené schválené vlastní Image ve vašem prostředí. Můžete zadat buď skupinu prostředků, která obsahuje Image schválené nebo konkrétní schválených bitové kopie.

Následující příklad vyžaduje bitové kopie z skupiny schválené prostředků:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

Následující příklad určuje ID schválené bitové kopie:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálního počítače

Můžete chtít nezakazuje využití určitých typů rozšíření. Například rozšíření pravděpodobně není kompatibilní s obrázky určité vlastního virtuálního počítače. Následující příklad ukazuje, jak chcete blokovat konkrétní rozšíření. Pomocí vydavatele a typ určit, které rozšíření, které chcete blokovat.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Další postup
* Po definování zásad pravidlo (jak je znázorněno v předchozích ukázkách), musíte k vytvoření definice zásady a přiřadit obor. Obor může být předplatné, skupinu prostředků nebo prostředek. K přiřazení zásady, najdete v části [portálu Azure použijte přiřadit a spravovat zásady prostředků](../../azure-policy/assign-policy-definition.md), [prostředí PowerShell použít k přiřazení zásad](../../azure-policy/assign-policy-definition-ps.md), nebo [použití Azure CLI k přiřazení zásad](../../azure-policy/assign-policy-definition-cli.md).
* Úvod do zásad prostředků, najdete v části [co je Azure zásad?](../../azure-policy/azure-policy-introduction.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](../../azure-resource-manager/resource-manager-subscription-governance.md).
