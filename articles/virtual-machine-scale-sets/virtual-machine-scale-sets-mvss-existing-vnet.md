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
ms.date: 06/27/2017
ms.author: manayar
ms.openlocfilehash: 1dcb97a94bd5790edc2e40acf890bb47baec7a4b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740089"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Přidat odkaz na existující virtuální sítě v šabloně Azure škálovací sady

Tento článek popisuje, jak změnit [šablonu minimální přijatelné škálovací sady](./virtual-machine-scale-sets-mvss-start.md) k nasazení do existující virtuální sítě, místo vytvoření nového.

## <a name="change-the-template-definition"></a>Změna definice šablony

Šablonu minimální přijatelné škálovací sady můžete zobrazit [tady](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), a šablony pro nasazení škálovací sady do existující virtuální sítě uvidíte [tady](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Podívejme se na rozdíl použít k vytvoření této šablony (`git diff minimum-viable-scale-set existing-vnet`) jeden po druhém:

Nejprve přidejte `subnetId` parametru. Tento řetězec je předána do konfigurace škálovací sady, což škálovací sady pro identifikaci předem vytvořené podsítě k nasazení virtuálních počítačů do. Tento řetězec musí být ve tvaru: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Například k nasazení škálovací sady existující virtuální síť s názvem `myvnet`, podsítě `mysubnet`, skupina prostředků `myrg`a předplatné `00000000-0000-0000-0000-000000000000`, by subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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
-      "apiVersion": "2016-12-01",
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
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

A konečně, předejte `subnetId` parametru nastaveného uživatelem (namísto použití `resourceId` získat ID virtuální sítě v jednom nasazení, což je co minimální přijatelné škálovací sady šablona nemá).

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
