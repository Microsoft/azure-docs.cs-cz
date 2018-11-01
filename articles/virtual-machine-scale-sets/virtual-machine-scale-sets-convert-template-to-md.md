---
title: Převést šablony Azure Resource Manageru škálovací sady pro použití spravovaného disku | Dokumentace Microsoftu
description: Převod šablony škálovací sady na spravovaný disk šablony škálovací sady.
keywords: škálovací sady virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: be56fd80229010090216413a7c1833d94e8bac25
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739562"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Převod šablony škálovací sady na spravovaný disk šablony škálovací sady

Zákazníci pomocí šablony Resource Manageru pro vytvoření škálovací sady bez použití spravovaného disku chtít upravit pro použití spravovaného disku. Tento článek popisuje, jak používat spravované disky jako příklad použijeme žádost o přijetí změn z [šablony pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates), komunitou vyvíjených úložiště pro ukázkové šablony Resource Manageru. Žádost o úplné o přijetí změn můžete zobrazit tady: [ https://github.com/Azure/azure-quickstart-templates/pull/2998 ](https://github.com/Azure/azure-quickstart-templates/pull/2998), a příslušné části rozdíly jsou níže, spolu s vysvětlení:

## <a name="making-the-os-disks-managed"></a>Vytváření spravovaných disků operačního systému

V následující rozdíly odeberou se několik proměnných související s vlastností účtu a disku úložiště. Typ účtu úložiště už není nutné (Standard_LRS je výchozí hodnota), ale můžete ho zadat v případě potřeby. Při použití spravovaného disku jsou podporovány pouze Standard_LRS a Premium_LRS. Nová přípona účtu úložiště jedinečný řetězec pole a počet sa byly použity v původní šabloně vygenerovat názvy účtů úložiště. Tyto proměnné nějaké už nejsou potřebné v novou šablonu, protože spravovaného disku automaticky vytvoří účty storage jménem zákazníka. Podobně název kontejneru virtuálního pevného disku a název disku operačního systému už nejsou potřebné protože spravovaný disk automaticky pojmenuje základní kontejnery úložiště objektů blob a disků.

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


V následující rozdíly výpočetních, že rozhraní API je aktualizována na verzi 2016-04-30-preview, což je nejbližší požadovaná verze pro podporu spravovaných disků se škálovacími sadami. Můžete použít nespravované disky v nové verzi rozhraní API pomocí staré syntaxe v případě potřeby. Pokud pouze aktualizovat na verzi rozhraní API výpočetní a neměnit cokoli jiného, šablona by měla fungovat stejně jako předtím.

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

V následující rozdíly prostředek účtu úložiště je zcela odebrán z pole prostředky. Prostředek se už nepotřebuje jako spravovaný disk vytvoří je automaticky.

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

V následující rozdíly, vidíme, že odebíráme závisí na klauzule odkazující ze škálovací sady do smyčky, která byla vytváření účtů úložiště. V původní šabloně to byla zajistit, že účty úložiště byly vytvořeny před zahájení vytváření škálovací sady, ale tuto klauzuli již není nezbytné při použití spravovaného disku. Kontejnery vlastností virtuálního pevného disku odebrán také, společně s vlastností název disku operačního systému jako tyto vlastnosti jsou automaticky zpracovány pod pokličkou spravovaného disku. Můžete přidat `"managedDisk": { "storageAccountType": "Premium_LRS" }` v konfiguraci "osDisk", pokud byste chtěli disky OS úrovně premium. Pouze virtuální počítače s velkým nebo malá písmena od "ve virtuálním počítači, můžete použít sku disky úrovně premium.

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

Neexistuje žádná explicitní vlastnost do konfigurace škálovací sady pro, jestli se má použít spravovaný nebo nespravovaný disk. Škálovací sady ví, která se použije na základě vlastností, které se nacházejí v profilu úložiště. Proto je důležité při úpravě šablony, která má správné vlastnosti musí být v profilu úložiště škálovací sady.


## <a name="data-disks"></a>Datové disky

Výše uvedené změny škálovací sada používá spravované disky pro operační systém na disku, ale co datové disky? Pokud chcete přidat datové disky, přidejte vlastnost "dataDisks" v části "storageProfile" na stejné úrovni jako "osDisk". Hodnota vlastnosti je JSON seznam objektů, z nichž každý má vlastnosti "logické jednotky" (který musí být jedinečný na datový disk na virtuálním počítači), "createOption" ("prázdná" je aktuálně jedinou podporovanou možností) a "diskSizeGB" (velikost disku v gigabajtech; musí být větší než 0 a menší než 1024) jako v následujícím příkladu: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Pokud zadáte `n` disků v tomto poli, každý virtuální počítač ve škálovací nastavit získá `n` datové disky. Upozorňujeme, že tyto datové disky jsou nezpracovaných zařízeních. Nejsou ve formátu. Záleží zákazníkovi připojení, oddíl a naformátovat disky před jejich použitím. Volitelně můžete také zadat `"managedDisk": { "storageAccountType": "Premium_LRS" }` jednotlivých objektů datového disku k určení, že by měl být datový disk úrovně premium. Pouze virtuální počítače s velkým nebo malá písmena od "ve virtuálním počítači, můžete použít sku disky úrovně premium.

Další informace o použití datových disků se škálovacími sadami najdete v tématu [v tomto článku](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Další postup
Příklad šablony Resource Manageru pomocí škálovací sady, vyhledejte "vmss" v [úložišti github šablon pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates).

Obecné informace, podívejte se [hlavní cílové stránce pro škálovací sady](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

