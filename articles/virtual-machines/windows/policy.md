---
title: Vynutit zabezpečení se zásadami na virtuálních počítačích Windows v Azure | Microsoft Docs
description: Tom, jak používat zásady pro správce prostředků Windows virtuální počítač Azure
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
ms.openlocfilehash: b6a42e1a0b0256a6b19220958f98940764273a2d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114380"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Použití zásad u virtuálních počítačů s Windows pomocí Azure Resource Manageru
Pomocí zásad můžete vynutit organizaci různé konvence a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při přispívání do úspěch organizace. V tomto článku jsme popisují, jak lze pomocí Azure Resource Manager zásad můžete určit požadované chování pro virtuální počítače vaší organizace.

Úvod do zásady, najdete v části [co je Azure zásad?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Povolené virtuální počítače
Zajistit, že virtuální počítače ve vaší organizaci, jsou kompatibilní s aplikací, můžete omezit povolených operační systémy. V následujícím příkladu zásad můžete povolit jenom Windows serveru 2012 R2 Datacenter virtuálních počítačů, který se má vytvořit:

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

K úpravě předchozí zásada umožnit všechny bitové kopie systému Windows Server Datacenter použijte zástupný znak:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

K úpravě předchozí zásada umožnit žádné Windows Server 2012 R2 Datacenter nebo vyšší bitové kopie použijte anyOf:

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Pokud máte licenci na místě, můžete uložit poplatek licence na virtuálních počítačích. Pokud nemáte licenci, by měl nezakazuje možnost. Tyto zásady zakazuje použití Azure hybridní použití zvýhodnění (AHUB):

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
* Po definování zásad pravidlo (jak je znázorněno v předchozích ukázkách), musíte k vytvoření definice zásady a přiřadit obor. Obor může být předplatné, skupinu prostředků nebo prostředek. K přiřazení zásady, najdete v části [portálu Azure použijte přiřadit a spravovat zásady prostředků](../../azure-policy/assign-policy-definition.md), [prostředí PowerShell použít k přiřazení zásad](../../azure-policy/assign-policy-definition-ps.md), nebo [použití Azure CLI k přiřazení zásad](../../azure-policy/assign-policy-definition-cli.md).
* Úvod do zásad prostředků, najdete v části [co je Azure zásad?](../../azure-policy/azure-policy-introduction.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).