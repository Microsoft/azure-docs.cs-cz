---
title: Odkazování na vlastní image v šabloně sady Azure Scale | Microsoft Docs
description: Přečtěte si, jak přidat vlastní image do existující šablony sady škálování virtuálních počítačů Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: 2ed75a72360253996471034b001e12e8190cf733
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935264"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Přidání vlastní image do šablony Azure Scale set

V tomto článku se dozvíte, jak upravit [šablonu základní sady škálování](virtual-machine-scale-sets-mvss-start.md) pro nasazení z vlastní image.

## <a name="change-the-template-definition"></a>Změna definice šablony
V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu sady škálování. Nyní použijeme tuto předchozí šablonu a upravíte ji k vytvoření šablony, která nasadí sadu škálování z vlastní image.  

### <a name="creating-a-managed-disk-image"></a>Vytvoření image spravovaného disku

Pokud již máte vlastní image spravovaného disku (prostředek typu `Microsoft.Compute/images`), můžete tuto část přeskočit.

Nejdřív přidejte `sourceImageVhdUri` parametr, což je identifikátor URI pro zobecněný objekt BLOB v Azure Storage, který obsahuje vlastní image, ze které se má nasadit.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Dále přidejte prostředek typu `Microsoft.Compute/images`, což je bitová kopie spravovaného disku založená na zobecněném objektu BLOB umístěném v identifikátoru URI. `sourceImageVhdUri` Tento obrázek musí být ve stejné oblasti jako sada škálování, která ho používá. Ve vlastnostech image zadejte typ operačního systému, umístění objektu BLOB (z `sourceImageVhdUri` parametru) a typ účtu úložiště:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

V prostředku sady škálování přidejte `dependsOn` klauzuli odkazující na vlastní image, abyste se ujistili, že se image vytvoří před tím, než se sada škálování pokusí o nasazení z této image:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Změna vlastností sady škálování na použití image spravovaného disku

V části sady `storageProfile`škálování místo určení vydavatele, nabídky, SKU a verze image `id` platformy zadejte `Microsoft.Compute/images` zdroj: `imageReference`

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

V tomto příkladu použijte `resourceId` funkci k získání ID prostředku obrázku vytvořeného ve stejné šabloně. Pokud jste vytvořili image spravovaného disku předem, měli byste místo toho zadat ID tohoto obrázku. Toto ID musí být ve tvaru: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
