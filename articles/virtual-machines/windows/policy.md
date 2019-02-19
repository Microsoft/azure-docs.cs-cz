---
title: Vynucení zabezpečení se zásadami na virtuálních počítačích s Windows v Azure | Dokumentace Microsoftu
description: Jak použít zásady na Azure Resource Manageru Windows virtuální počítač
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 654c23474ebc96a6873f82f51f969da5c7d2d20c
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343251"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Použití zásad k virtuálním počítačům s Windows pomocí Azure Resource Manageru
Pomocí zásad můžete vynutit organizace různých konvencí a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při podílení se na úspěch organizace. V tomto článku popisujeme, jak můžete zásady Azure Resource Manageru pro definování požadované chování pro virtuální počítače vaší organizace.

Úvod do zásad najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Povolené virtuální počítače
K zajištění, že jsou kompatibilní s aplikací virtuálních počítačů ve vaší organizaci, můžete omezit povolené operačních systémů. V následujícím příkladu zásady Povolit pouze Windows Server 2012 R2 Datacenter Virtual Machines vytvořit:

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

Zástupný znak použijte k úpravě předchozí zásady a umožní žádné image Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Použijte anyOf upravit předchozí zásady a umožní vyšší image ani Windows Server 2012 R2 Datacenter:

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Pokud máte licenci v místním, můžete uložit licenční poplatek za na virtuálních počítačích. Pokud nemáte licenci, měli zakázat možnost. Tuto zásadu zakazuje využití Azure pro hybridní použití výhody (AHUB):

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

## <a name="next-steps"></a>Další postup
* Po definování pravidla zásad (jak je znázorněno v předchozím příkladu), budete muset vytvořit definici zásady a přiřadit obor. Rozsah může být předplatné, skupinu prostředků nebo prostředek. Přiřazení zásad, najdete v článku [použijte Azure portal a přiřazení a Správa zásad prostředků](../../governance/policy/assign-policy-portal.md), [použití Powershellu k přiřazení zásady](../../governance/policy/assign-policy-powershell.md), nebo [pomocí Azure CLI pro přiřazení zásad](../../governance/policy/assign-policy-azurecli.md).
* Úvod do zásad prostředků najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).