---
title: Odkaz na existující virtuální síť v šabloně škálovací sady Azure
description: Zjistěte, jak přidat virtuální síť do existující šablony sady škálování virtuálních strojů Azure
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273660"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Přidání odkazu na existující virtuální síť v šabloně škálovací sady Azure

Tento článek ukazuje, jak upravit [základní škálovací sadu šablony](virtual-machine-scale-sets-mvss-start.md) pro nasazení do existující virtuální sítě namísto vytvoření nové.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu škálovací sady. Nyní použijeme tuto starší šablonu a upravíme ji, abychom vytvořili šablonu, která nasazuje škálovací sadu do existující virtuální sítě. 

Nejprve přidejte `subnetId` parametr. Tento řetězec je předán do konfigurace škálovací sady, což umožňuje škálovací sadě k identifikaci předem vytvořené podsítě pro nasazení virtuálních počítačů. Tento řetězec musí být ve tvaru:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Chcete-li například nasadit škálovací sadu `myvnet`do existující `mysubnet`virtuální `myrg`sítě s `00000000-0000-0000-0000-000000000000`názvem , podsítí , `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`skupinou prostředků a odběrem , bude podsouborní ho dis: .

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

Dále odstraňte prostředek virtuální `resources` sítě z pole, protože používáte existující virtuální síť a nepotřebujete nasadit novou.

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

Virtuální síť již existuje před nasazením šablony, takže není nutné zadávat klauzuli dependsOn z škálovací sady do virtuální sítě. Odstraňte následující řádky:

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

Nakonec předajte `subnetId` parametr nastavený uživatelem `resourceId` (namísto použití k získání ID virtuální sítě ve stejném nasazení, což je to, co dělá základní životaschopná šablona škálovací sady).

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
