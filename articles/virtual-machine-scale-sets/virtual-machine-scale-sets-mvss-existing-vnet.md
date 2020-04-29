---
title: Odkazování na existující virtuální síť v šabloně Azure Scale set
description: Přečtěte si, jak přidat virtuální síť do existující šablony Azure Virtual Machine Scale set.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273660"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Přidání odkazu do existující virtuální sítě v šabloně Azure Scale set

V tomto článku se dozvíte, jak upravit [šablonu základní sady škálování](virtual-machine-scale-sets-mvss-start.md) tak, aby se nasadila do existující virtuální sítě, a ne vytvořit novou.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu sady škálování. Nyní použijeme tuto předchozí šablonu a upravíte ji k vytvoření šablony, která nasadí sadu škálování do existující virtuální sítě. 

Nejdřív přidejte `subnetId` parametr. Tento řetězec se předává do konfigurace sady škálování, což umožňuje, aby sada škálování identifikovala předem vytvořenou podsíť pro nasazení virtuálních počítačů. Tento řetězec musí být ve tvaru:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Pokud `myvnet`například chcete nasadit sadu škálování do existující virtuální sítě s názvem, podsítí `mysubnet`, skupinou `myrg`prostředků a předplatným `00000000-0000-0000-0000-000000000000`, bude subnetId:. `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`

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

Virtuální síť již existuje před nasazením šablony, takže není nutné zadávat klauzuli dependsOn ze sady škálování nastavenou na virtuální síť. Odstraňte následující řádky:

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

Nakonec předejte `subnetId` parametr nastavený uživatelem (místo použití `resourceId` k získání ID virtuální sítě ve stejném nasazení, což je to, co je to šablona Základní životaschopné sady škálování).

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
