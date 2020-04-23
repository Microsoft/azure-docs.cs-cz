---
title: Vynucení zabezpečení pomocí zásad na virtuálních počítačích s Windows v Azure
description: Jak použít zásadu pro virtuální počítač Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 407e5cefe9f7f60c86de6e80133ff1b3f8b9003d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086534"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Použití zásad pro virtuální počítače s Windows pomocí Správce prostředků Azure
Pomocí zásad může organizace vynucovat různé konvence a pravidla v celém podniku. Vynucení požadovaného chování může pomoci zmírnit riziko a zároveň přispět k úspěchu organizace. V tomto článku popisujeme, jak můžete pomocí zásad Azure Resource Manager definovat požadované chování pro virtuální počítače vaší organizace.

Úvod k zásadám najdete v tématu [Co je zásady Azure?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Povolené virtuální počítače
Chcete-li zajistit, aby virtuální počítače pro vaši organizaci byly kompatibilní s aplikací, můžete omezit povolené operační systémy. V následujícím příkladu zásad povolíte vytvoření pouze virtuálních počítačů datového centra Windows Server 2012 R2:

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
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Pomocí zástupné znakové karty upravte předchozí zásadu tak, aby byla povolena jakákoli bitová kopie datového centra systému Windows Server:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Pomocí programu anyOf upravte předchozí zásady tak, aby umožňovaly libovolné datové centrum Windows Server 2012 R2 nebo vyšší obrázek:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Informace o polích zásad naleznete v tématu [Policy aliases](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Spravované disky

Chcete-li vyžadovat použití spravovaných disků, použijte následující zásady:

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

## <a name="images-for-virtual-machines"></a>Obrázky pro virtuální počítače

Z bezpečnostních důvodů můžete vyžadovat, aby byly ve vašem prostředí nasazeny pouze schválené vlastní bitové kopie. Můžete určit skupinu prostředků, která obsahuje schválené obrazy, nebo konkrétní schválené obrazy.

Následující příklad vyžaduje obrázky ze schválené skupiny prostředků:

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

Následující příklad určuje ID schváleného obrázku:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálního počítače

Možná budete chtít zakázat používání určitých typů rozšíření. Rozšíření například nemusí být kompatibilní s určitými obrazy vlastních virtuálních strojů. Následující příklad ukazuje, jak blokovat konkrétní rozšíření. Používá vydavatele a typ k určení, které rozšíření blokovat.

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Pokud máte místní licenci, můžete uložit licenční poplatek na virtuálních počítačích. Pokud nemáte licenci, měli byste tuto možnost zakázat. Následující zásady zakazují využití výhod hybridního využití Azure (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Další kroky
* Po definování pravidla zásady (jak je znázorněno v předchozích příkladech) je třeba vytvořit definici zásady a přiřadit ji k oboru. Obor může být předplatné, skupina prostředků nebo prostředek. Pokud chcete přiřadit zásady, přečtěte [si část Použití portálu Azure k přiřazení a správě zásad prostředků](../../governance/policy/assign-policy-portal.md), použití [PowerShellu k přiřazení zásad](../../governance/policy/assign-policy-powershell.md)nebo Použití nastavení [příkazu k nastavení nastavení azure k přiřazení zásad](../../governance/policy/assign-policy-azurecli.md).
* Úvod k zásadám prostředků najdete v tématu [Co je zásady Azure?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
