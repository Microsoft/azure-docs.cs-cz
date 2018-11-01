---
title: Odkazovat na vlastní image v šabloně Azure škálovací sada | Dokumentace Microsoftu
description: Zjistěte, jak přidat vlastní image do stávající šablony Škálovací sady virtuálních počítačů Azure
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
ms.date: 5/10/2017
ms.author: manayar
ms.openlocfilehash: 2e3c8177a32082c251be74e597a18730ae1c9d37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739634"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Přidání vlastní image šablony Azure škálovací sady

Tento článek popisuje, jak změnit [šablonu minimální přijatelné škálovací sady](./virtual-machine-scale-sets-mvss-start.md) nasadit z vlastní image.

## <a name="change-the-template-definition"></a>Změna definice šablony

Šablonu minimální přijatelné škálovací sady můžete zobrazit [tady](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), a šablony pro nasazení škálovací sady z vlastní image můžete vidět [tady](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Podívejme se na rozdíl použít k vytvoření této šablony (`git diff minimum-viable-scale-set custom-image`) jeden po druhém:

### <a name="creating-a-managed-disk-image"></a>Vytvoření image spravovaného disku

Pokud už máte spravovaného disku vlastní image (prostředek typu `Microsoft.Compute/images`), pak můžete tuto část přeskočit.

Nejprve přidejte `sourceImageVhdUri` parametr, což je identifikátor URI pro generalizovaný objektů blob v Azure Storage, který obsahuje vlastní image k nasazení z.


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

Dále přidejte prostředek typu `Microsoft.Compute/images`, který je spravovaný disk image založenou na zobecněný objektů blob v identifikátoru URI `sourceImageVhdUri`. Tento obrázek musí být ve stejné oblasti jako škálovací sada, která ji používá. V okně Vlastnosti obrázku, zadejte typ operačního systému, umístění objektu blob (z `sourceImageVhdUri` parametr) a typ účtu úložiště:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
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

V škálovací sady prostředků, přidat `dependsOn` klauzule odkazující na vlastní image, ujistěte se, že image se vytvoří před škálovací sady se pokusí nasazení z této image:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Změna měřítka nastavit vlastnosti, které chcete použít image spravovaného disku

V `imageReference` stupnice nastavit `storageProfile`, místo zadávání vydavatele, nabídky, sku, a zadejte verzi image platformy, `id` z `Microsoft.Compute/images` prostředků:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

V tomto příkladu použijte `resourceId` funkce získáte ID prostředku bitové kopie vytvořené ve stejné šabloně. Pokud jste vytvořili image spravovaného disku předem, byste měli poskytnout ID této bitové kopie. Toto ID musí být ve tvaru: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
