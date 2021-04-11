---
title: Odkazování na existující virtuální síť v šabloně Azure Scale set
description: Přečtěte si, jak přidat virtuální síť do existující šablony Azure Virtual Machine Scale set.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076662"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Odkazování na existující virtuální síť v šabloně Azure Scale set

V tomto článku se dozvíte, jak upravit [šablonu základní sady škálování](virtual-machine-scale-sets-mvss-start.md) tak, aby se nasadila do existující virtuální sítě, a ne vytvořit novou.

## <a name="prerequisites"></a>Požadavky

V předchozím článku jsme vytvořili [základní šablonu sady škálování](virtual-machine-scale-sets-mvss-start.md). Budete potřebovat tuto předchozí šablonu, aby ji bylo možné upravit a vytvořit šablonu, která nasadí sadu škálování do existující virtuální sítě.

## <a name="identify-subnet"></a>Identifikace podsítě

Nejdřív přidejte `subnetId` parametr. Tento řetězec se předává do konfigurace sady škálování, což umožňuje, aby sada škálování identifikovala předem vytvořenou podsíť pro nasazení virtuálních počítačů. Tento řetězec musí být ve tvaru:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Například pokud chcete nasadit sadu škálování do existující virtuální sítě s názvem `myvnet` , podsítí `mysubnet` , skupinou prostředků `myrg` a předplatným `00000000-0000-0000-0000-000000000000` , subnetId bude: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Odstranit prostředek nadbytečné virtuální sítě

Dále odstraňte prostředek virtuální sítě z `resources` pole, když použijete existující virtuální síť a nepotřebujete nasadit novou.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Odebrat klauzuli závislosti

Virtuální síť již existuje před nasazením šablony, takže není nutné zadávat `dependsOn` klauzuli ze sady škálování nastavenou na virtuální síť. Odstraňte následující řádky:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Předat parametr podsítě

Nakonec předejte `subnetId` Parametr nastavený uživatelem (místo použití `resourceId` k získání ID virtuální sítě ve stejném nasazení, což je to, co je to šablona Základní životaschopné sady škálování).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
