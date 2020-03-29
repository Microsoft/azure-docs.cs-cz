---
title: Převod šablony škálovací sady pro použití spravovaného disku
description: Převeďte šablonu škálovací sady virtuálních počítačů Azure Resource Manager u spravované ho disku na šablonu škálovací sady spravovaných disků.
keywords: škálovací sady virtuálních počítačů
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278127"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Převedení šablony škálovací sady na šablonu škálovací sady spravovaného disku

Zákazníci se šablonou Správce prostředků pro vytvoření škálovací sady, která nepoužívá spravovaný disk, ji mohou chtít upravit tak, aby používala spravovaný disk. Tento článek ukazuje, jak používat spravované disky, jako příklad žádost o přijetí informací ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates), repo založené na komunitě pro ukázkové šablony Správce prostředků. Úplný požadavek na přijetí [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)k tomu je vidět zde: a příslušné části rozdílu jsou níže, spolu s vysvětlením:

## <a name="making-the-os-disks-managed"></a>Spravit disky operačního systému

V následujícím rozdílu jsou odebrány několik proměnných souvisejících s účtem úložiště a vlastnostmi disku. Typ účtu úložiště již není nutné (Standard_LRS je výchozí), ale můžete jej zadat v případě potřeby. Spravovaný disk jsou podporovány pouze Standard_LRS a Premium_LRS. Nová přípona účtu úložiště, jedinečné pole řetězců a počet sa byly použity ve staré šabloně ke generování názvů účtů úložiště. Tyto proměnné již nejsou v nové šabloně nutné, protože spravovaný disk automaticky vytvoří účty úložiště jménem zákazníka. Podobně název kontejneru vhd a název disku operačního systému již nejsou nutné, protože spravovaný disk automaticky pojmenuje základní kontejnery objektů blob úložiště a disky.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


V následujícím rozdílu vypočítáte rozhraní API je aktualizován na verzi 2016-04-30-preview, což je nejstarší požadovaná verze pro podporu spravovaného disku s škálovacími sadami. V nové verzi rozhraní API můžete v případě potřeby použít nespravované disky v nové verzi rozhraní API se starou syntaxí. Pokud aktualizujete jenom verzi rozhraní API pro výpočetní prostředky a nic jiného nezměníte, šablona by měla pokračovat v práci jako předtím.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

V následujícím rozdílu je prostředek účtu úložiště odebrán z pole prostředků úplně. Prostředek již není potřeba, protože spravovaný disk je vytváří automaticky.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

V následujícím rozdílu vidíme, že odstraňujeme závisí na klauzuli odkazující z škálovací sady na smyčku, která vytvářela účty úložiště. Ve staré šabloně to bylo zajištění, že účty úložiště byly vytvořeny před vytvořením škálovací sady, ale tato klauzule již není nutné se spravovaným diskem. Vlastnost kontejnery vhd je také odebrána spolu s vlastností název disku operačního systému, protože tyto vlastnosti jsou automaticky zpracovány pod kapotou spravovaným diskem. Můžete přidat `"managedDisk": { "storageAccountType": "Premium_LRS" }` do konfigurace "osDisk", pokud jste chtěli prémiové disky operačního systému. Pouze virtuální počítače s velkými nebo malými písmeny "s" ve sku virtuálního počítače můžete použít disky premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

V konfiguraci škálovací sady neexistuje žádná explicitní vlastnost, zda chcete použít spravovaný nebo nespravovaný disk. Škálovací sada ví, které použít na základě vlastností, které jsou k dispozici v profilu úložiště. Proto je důležité při úpravě šablony zajistit, že správné vlastnosti jsou v profilu úložiště škálovací sady.


## <a name="data-disks"></a>Datové disky

S výše uvedenými změnami používá škálovací sada spravované disky pro disk operačního systému, ale co datové disky? Chcete-li přidat datové disky, přidejte vlastnost "dataDisks" pod "storageProfile" na stejné úrovni jako "osDisk". Hodnota vlastnosti je json seznam objektů, z nichž každý má vlastnosti "lun" (které musí být jedinečné pro datový disk na virtuálním počítači), "createOption" ("prázdný" je aktuálně jedinou podporovanou možností) a "diskSizeGB" (velikost disku v gigabajtů; musí být větší než větší než větší než 0 a menší než 1024) jako v následujícím příkladu:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Pokud zadáte `n` disky v tomto poli, každý virtuální `n` počítače ve škálovací sadě získá datové disky. Všimněte si však, že tyto datové disky jsou nezpracovaná zařízení. Nejsou formátovány. Je na zákazníkovi, aby disky před použitím připojil, rozdělil a naformátovat. Volitelně můžete také `"managedDisk": { "storageAccountType": "Premium_LRS" }` zadat v každém objektu datového disku určit, že by měl být premium datový disk. Pouze virtuální počítače s velkými nebo malými písmeny "s" ve sku virtuálního počítače můžete použít disky premium.

Další informace o používání datových disků se sadami škálování naleznete v [tomto článku](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Další kroky
Například šablony Správce prostředků pomocí škálovacích sad vyhledejte "vmss" v [úložišti GitHub šablony Azure QuickStart](https://github.com/Azure/azure-quickstart-templates).

Obecné informace najdete na [hlavní vstupní stránce pro škálovací sady](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

