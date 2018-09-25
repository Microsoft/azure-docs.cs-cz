---
title: Vynucení zabezpečení pomocí zásad na virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Tom, jak aplikovat zásady pro Azure Resource Manageru virtuálního počítače s Linuxem
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
ms.openlocfilehash: 04a172c40c24e4e0daaf17b495d2320f3a897a47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975250"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Použít zásady pro virtuální počítače s Linuxem pomocí Azure Resource Manageru
Pomocí zásad můžete vynutit organizace různých konvencí a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při podílení se na úspěch organizace. V tomto článku popisujeme, jak můžete zásady Azure Resource Manageru pro definování požadované chování pro virtuální počítače vaší organizace.

Úvod do zásad najdete v tématu [co je Azure Policy?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Povolené virtuální počítače
K zajištění, že jsou kompatibilní s aplikací virtuálních počítačů ve vaší organizaci, můžete omezit povolené operačních systémů. V následujícím příkladu zásady je povoleno pouze 14.04.2-LTS virtuálních počítačů s Ubuntu má být vytvořen.

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

Zástupný znak použijte k úpravě předchozí zásady a umožní žádné image Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Informace o polí zásad najdete v tématu [zásad aliasy](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Managed Disks

Vyžadovat použití spravovaných disků, použijte následující zásady:

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

## <a name="images-for-virtual-machines"></a>Image virtuálních počítačů

Z bezpečnostních důvodů můžete vyžadovat, že schválené vlastních imagí nasazených ve vašem prostředí. Můžete zadat buď skupinu prostředků, který obsahuje Image schválené nebo konkrétní schválení bitové kopie.

V následujícím příkladu vyžaduje bitové kopie ze schválených prostředek skupiny:

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

Následující příklad určuje ID schválené imagi:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálních počítačů

Můžete chtít zakázat použití určitých typů rozšíření. Například rozšíření nemusí být kompatibilní s určitým vlastní Image virtuálních počítačů. Následující příklad ukazuje, jak blokovat konkrétní rozšíření. Vydavatele a typ používá k určení, které rozšíření blokování.

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
* Po definování pravidla zásad (jak je znázorněno v předchozím příkladu), budete muset vytvořit definici zásady a přiřadit obor. Rozsah může být předplatné, skupinu prostředků nebo prostředek. Přiřazení zásad, najdete v článku [použijte Azure portal a přiřazení a Správa zásad prostředků](../../azure-policy/assign-policy-definition.md), [použití Powershellu k přiřazení zásady](../../azure-policy/assign-policy-definition-ps.md), nebo [pomocí Azure CLI pro přiřazení zásad](../../azure-policy/assign-policy-definition-cli.md).
* Úvod do zásad prostředků najdete v tématu [co je Azure Policy?](../../azure-policy/azure-policy-introduction.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
