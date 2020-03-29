---
title: Odkaz na vlastní bitovou kopii v šabloně škálovací sady Azure
description: Přečtěte si, jak přidat vlastní image do existující šablony škálování virtuálních strojů Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275594"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Přidání vlastní image do šablony škálovací sady Azure

Tento článek ukazuje, jak upravit [základní šablonu škálovací sady](virtual-machine-scale-sets-mvss-start.md) pro nasazení z vlastní image.

## <a name="change-the-template-definition"></a>Změna definice šablony
V [předchozím článku](virtual-machine-scale-sets-mvss-start.md) jsme vytvořili základní šablonu škálovací sady. Nyní použijeme tuto starší šablonu a upravíme ji, abychom vytvořili šablonu, která nasazuje škálovací sadu z vlastní bitové kopie.  

### <a name="creating-a-managed-disk-image"></a>Vytvoření spravované bitové kopie disku

Pokud již máte vlastní spravovanou `Microsoft.Compute/images`bitovou kopii disku (prostředek typu), můžete tuto část přeskočit.

Nejprve přidejte `sourceImageVhdUri` parametr, což je identifikátor URI do generalizovaného objektu blob ve službě Azure Storage, který obsahuje vlastní image, ze které se má nasadit.


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

Dále přidejte prostředek `Microsoft.Compute/images`typu , což je bitová kopie spravovaného disku `sourceImageVhdUri`založená na generalizovaném objektu blob umístěném na adrese URI . Tento obrázek musí být ve stejné oblasti jako škálovací sada, která jej používá. Ve vlastnostech obrázku zadejte typ operačního systému, umístění `sourceImageVhdUri` objektu blob (z parametru) a typ účtu úložiště:

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

Do prostředku škálovací sady `dependsOn` přidejte klauzuli odkazující na vlastní bitovou kopii, abyste se ujistili, že se bitová kopie vytvoří dříve, než se škálovací sada pokusí nasadit z této bitové kopie:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Změna vlastností škálovací sady pro použití spravované bitové kopie disku

V škálovací sadě `storageProfile`určete místo určení vydavatele, nabídky, sku a verze `id` bitové `Microsoft.Compute/images` kopie platformy prostředek: `imageReference`

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

V tomto příkladu `resourceId` použijte funkci k získání ID prostředku obrazu vytvořeného ve stejné šabloně. Pokud jste vytvořeno uspravované ho disku předem, měli byste místo toho zadat ID této bitové kopie. Toto ID musí mít `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`formu: .


## <a name="next-steps"></a>Další kroky

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
