---
title: Odkazovat na existující virtuální sítě v šabloně Azure škálovací sada | Dokumentace Microsoftu
description: Zjistěte, jak přidat do stávající šablony Škálovací sady virtuálních počítačů Azure virtual network
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868955"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Přidat odkaz na existující virtuální sítě v šabloně Azure škálovací sady

Tento článek popisuje, jak změnit [šablonu základní škálovací sady](virtual-machine-scale-sets-mvss-start.md) k nasazení do existující virtuální sítě, místo vytvoření nového.

## <a name="change-the-template-definition"></a>Změna definice šablony

V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní škálovací sadu šablony. Nyní budeme používat tento starší šablony a upravit tak, aby vytvořit šablonu, která nasadí škálovací sady do existující virtuální sítě. 

Nejprve přidejte `subnetId` parametru. Tento řetězec je předána do konfigurace škálovací sady, což škálovací sady pro identifikaci předem vytvořené podsítě k nasazení virtuálních počítačů do. Tento řetězec musí být ve tvaru: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Například k nasazení škálovací sady existující virtuální síť s názvem `myvnet`, podsítě `mysubnet`, skupina prostředků `myrg`a předplatné `00000000-0000-0000-0000-000000000000`, by subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

V dalším kroku odstranit prostředek virtuální sítě z `resources` pole, jak použít existující virtuální síť a není potřeba nasazovat nové.

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

Virtuální síť už je před nasazením šablony proto není nutné k zadání klauzule dependsOn ze škálovací sady do virtuální sítě. Odstraňte tyto řádky:

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

A konečně, předejte `subnetId` parametru nastaveného uživatelem (namísto použití `resourceId` získat ID virtuální sítě v jednom nasazení, což je co základní přijatelné škálovací sady šablona nemá).

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




## <a name="next-steps"></a>Další postup

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
