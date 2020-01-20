---
title: Odkazování na existující virtuální síť v šabloně Azure Scale set
description: Přečtěte si, jak přidat virtuální síť do existující šablony Azure Virtual Machine Scale set.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275535"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Přidání odkazu do existující virtuální sítě v šabloně Azure Scale set

V tomto článku se dozvíte, jak upravit [šablonu základní sady škálování](virtual-machine-scale-sets-mvss-start.md) tak, aby se nasadila do existující virtuální sítě, a ne vytvořit novou.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu sady škálování. Nyní použijeme tuto předchozí šablonu a upravíte ji k vytvoření šablony, která nasadí sadu škálování do existující virtuální sítě. 

Nejdřív přidejte parametr `subnetId`. Tento řetězec se předává do konfigurace sady škálování, což umožňuje, aby sada škálování identifikovala předem vytvořenou podsíť pro nasazení virtuálních počítačů. Tento řetězec musí být ve tvaru: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Pokud například chcete nasadit sadu škálování do existující virtuální sítě s názvem `myvnet`, `mysubnet`podsítě, skupiny prostředků `myrg`a `00000000-0000-0000-0000-000000000000`předplatného, bude subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Dále odstraňte prostředek virtuální sítě z `resources`ho pole, protože používáte existující virtuální síť a nemusíte nasazovat novou.

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

Nakonec předejte parametr `subnetId` nastaveného uživatelem (místo použití `resourceId` k získání ID virtuální sítě ve stejném nasazení, což je to, co je to šablona Základní životaschopné sady pro škálování).

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
