---
title: Vynutilit zabezpečení pomocí zásad na virtuálních počítačích s Windows v Azure
description: Jak použít zásadu na virtuální počítač s Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: a2ee5f36b7dba03bea3cc219651804c5039115d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032977"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Použití zásad pro virtuální počítače s Windows pomocí Azure Resource Manager
Pomocí zásad může organizace vyhovět různým konvencím a pravidlům v celém podniku. Vynucování požadovaného chování může přispět k zmírnění rizika při přispívání na úspěch organizace. V tomto článku popisujeme, jak můžete pomocí zásad Azure Resource Manager definovat požadované chování pro Virtual Machines vaší organizace.

Úvod do zásad najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Povolené Virtual Machines
Aby bylo zajištěno, že virtuální počítače pro vaši organizaci budou kompatibilní s aplikací, můžete omezit povolený operační systém. V následujících příkladech zásad povolíte, aby se vytvořilo jenom Windows Server 2012 R2 Datacenter Virtual Machines:

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

Pomocí zástupné karty můžete změnit předchozí zásady, aby bylo možné použít jakoukoli bitovou kopii Windows serveru Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Pomocí anyOf upravte předchozí zásadu tak, aby povolovala Windows Server 2012 R2 Datacenter nebo vyšší obrázek:

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Pokud máte místní licenci, můžete licenční poplatek uložit na virtuální počítače. Pokud licenci nemáte, měli byste zakázat možnost. Následující zásady zakazují použití zvýhodněné hybridní využití Azure (AHUB):

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
* Po definování pravidla zásad (jak je znázorněno v předchozích příkladech) musíte vytvořit definici zásady a přiřadit ji k oboru. Oborem může být předplatné, skupina prostředků nebo prostředek. Pokud chcete přiřadit zásady, přečtěte si téma [použití Azure Portal k přiřazení a správě zásad prostředků](../../governance/policy/assign-policy-portal.md), [použití PowerShellu k přiřazování zásad](../../governance/policy/assign-policy-powershell.md)nebo použití rozhraní příkazového [řádku Azure k přiřazení zásad](../../governance/policy/assign-policy-azurecli.md).
* Úvod do zásad prostředků najdete v tématu [co je Azure Policy?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).