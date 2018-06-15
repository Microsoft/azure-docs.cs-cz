---
title: Převést šablonu Azure Resource Manager škálování sadu používat spravovaných disků na | Microsoft Docs
description: Převeďte na šablonu sady škálování spravovaných disků na šablonu sady škálování.
keywords: Sady škálování virtuálního počítače
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781067"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Převést na šablonu sady škálování spravovaných disků na šablonu sady škálování

Zákazníci pomocí šablony Resource Manageru pro vytváření škálovací sadu nepoužíváte spravovaných disků na chtít upravit tak, aby pomocí spravovaného disku. Tento článek ukazuje, jak používat spravovaného disků pomocí jako příklad žádost o přijetí změn z [šablon Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates), úložišti komunitou vytvářený pro ukázkové šablony Resource Manageru. Zde můžete zjistit úplné přijetí žádosti o: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), a jsou v příslušných částech rozdílové níže, společně s vysvětlení:

## <a name="making-the-os-disks-managed"></a>Provedení spravované disky operačního systému

V následujících rozdílů se odeberou několika proměnných související s vlastností účtu a disk úložiště. Typ účtu úložiště už není nutné (Standard_LRS je výchozí nastavení), ale můžete ji zadat v případě potřeby. Jsou podporovány pouze Standard_LRS a Premium_LRS s spravovaného disku. Nová přípona účet úložiště, pole jedinečné řetězce a počet sa byly použity v původní šabloně vygenerovat názvy účtů úložiště. Tyto proměnné již nejsou potřebné v nové šabloně protože spravovaných disků na účty úložiště automaticky vytvoří jménem zákazníka. Podobně název kontejneru virtuálního pevného disku a název disku operačního systému již nejsou potřebné protože spravovaných disků na názvy automaticky základní kontejnery úložiště objektů blob a disků.

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


V následující rozdílové výpočetní, že rozhraní API se aktualizuje na verzi 2016-04-30-preview, což je nejdříve požadovaná verze spravovaných disků na podporu pro sady škálování. Můžete použít nespravované disky v nové verzi rozhraní API pomocí staré syntaxe v případě potřeby. Pokud pouze aktualizovat na verzi rozhraní API výpočetní a neměnit cokoliv jiného, šablona by měly být nadále fungovat jako předtím.

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

V následujících rozdílů prostředků účtu úložiště je úplně odebrat z pole prostředky. Prostředek se už nepotřebuje jako spravovaných disků na vytvoří je automaticky.

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

V následujících rozdílů, uvidíme jsme odebrání závisí na klauzule odkazující od stupnice nastavena na smyčky, která byla vytváření účtů úložiště. V původní šabloně to byla zajistit, aby byly účty úložiště vytvořené před škálovací sadu začal vytvoření, ale tuto klauzuli už není nutné u spravovaných disků na. Vlastnost kontejnery vhd se taky odeberou, spolu s vlastnost název disku operačního systému jako tyto vlastnosti jsou automaticky zpracovávány pod pokličkou spravovaných disků na. Můžete přidat `"managedDisk": { "storageAccountType": "Premium_LRS" }` v konfiguraci "osDisk", pokud byste chtěli prémiové disky operačního systému. Pouze virtuální počítače s velkým nebo na malá ' ve virtuálním počítači můžete použít sku prémiové disky.

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

Neexistuje žádné explicitní vlastnost v konfiguraci sady škálování pro jestli se má používat disku spravované nebo nespravované. Sada škálování ví, který se použije na základě vlastností, které se nacházejí v profilu úložiště. Proto je důležité při úpravě šablonu, zajistěte, aby vlastnosti oprávnění v profilu úložiště škálovací sady.


## <a name="data-disks"></a>Datové disky

S výše změny škálování sady používá spravované disky pro operační systém na disku, ale co o datových disků? Chcete-li přidat datových disků, přidejte vlastnost "dataDisks" v části "storageProfile" na stejné úrovni jako "osDisk". Hodnota vlastnosti je JSON seznam objektů, z nichž každá má vlastnosti "lun" (které musí být jedinečný pro každé datový disk na virtuálním počítači), "createOption" ("prázdný" je aktuálně jedinou možností podporovaných) a "diskSizeGB" (velikost disku v gigabajtech; musí být větší než 0 a menší než 1024) jako v následujícím příkladu: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Pokud zadáte `n` disky v toto pole, jednotlivé virtuální počítače v měřítka nastavit získá `n` datových disků. Upozorňujeme však, že jsou tyto datové disky nezpracované zařízení. Jejich nejsou formátovány. Je zákazník připojit, oddíl a naformátovat disky před jejich používání. Volitelně můžete také zadat `"managedDisk": { "storageAccountType": "Premium_LRS" }` v každý datový disk objekt k určení, že by měl být premium datový disk. Pouze virtuální počítače s velkým nebo na malá ' ve virtuálním počítači můžete použít sku prémiové disky.

Další informace o datových disků pomocí sady škálování najdete v tématu [v tomto článku](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Další kroky
Například šablony Resource Manageru pomocí sad škálování, vyhledejte "vmss" v [úložiště github šablon Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates).

Obecné informace, podívejte se [hlavní cílová stránka pro sady škálování](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

