---
title: Vynutilit zabezpečení pomocí zásad pro virtuální počítače se systémem Linux v Azure
description: Jak použít zásadu na virtuální počítač s Azure Resource Manager Linux
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549817"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Použití zásad pro virtuální počítače se systémem Linux pomocí Azure Resource Manager
Pomocí zásad může organizace vyhovět různým konvencím a pravidlům v celém podniku. Vynucování požadovaného chování může přispět k zmírnění rizika při přispívání na úspěch organizace. V tomto článku popisujeme, jak můžete pomocí zásad Azure Resource Manager definovat požadované chování pro Virtual Machines vaší organizace.

Úvod do zásad najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Povolené Virtual Machines
Aby bylo zajištěno, že virtuální počítače pro vaši organizaci budou kompatibilní s aplikací, můžete omezit povolený operační systém. V následujících příkladech zásad povolíte vytváření jenom Ubuntu 14.04.2-LTS Virtual Machines.

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

Pomocí zástupné karty upravte předchozí zásadu tak, aby umožňovala Ubuntu image LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Informace o polích zásad najdete v tématu [aliasy zásad](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Spravované disky

Pokud chcete vyžadovat použití spravovaných disků, použijte následující zásady:

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

## <a name="images-for-virtual-machines"></a>Obrázky pro Virtual Machines

Z bezpečnostních důvodů můžete vyžadovat, aby ve vašem prostředí byly nasazené jenom schválené vlastní image. Můžete zadat buď skupinu prostředků, která obsahuje schválené bitové kopie, nebo konkrétní schválené bitové kopie.

Následující příklad vyžaduje image ze schválené skupiny prostředků:

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

Následující příklad určuje schválená ID imagí:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálních počítačů

Možná budete chtít zakázat používání určitých typů rozšíření. Například rozšíření nemusí být kompatibilní s některými vlastními imagemi virtuálních počítačů. Následující příklad ukazuje, jak zablokovat konkrétní rozšíření. Používá vydavatele a typ k určení, které rozšíření má blokovat.

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


## <a name="next-steps"></a>Další kroky
* Po definování pravidla zásad (jak je znázorněno v předchozích příkladech) musíte vytvořit definici zásady a přiřadit ji k oboru. Oborem může být předplatné, skupina prostředků nebo prostředek. Pokud chcete přiřadit zásady, přečtěte si téma [použití Azure Portal k přiřazení a správě zásad prostředků](../../governance/policy/assign-policy-portal.md), [použití PowerShellu k přiřazování zásad](../../governance/policy/assign-policy-powershell.md)nebo použití rozhraní příkazového [řádku Azure k přiřazení zásad](../../governance/policy/assign-policy-azurecli.md).
* Úvod do zásad prostředků najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
