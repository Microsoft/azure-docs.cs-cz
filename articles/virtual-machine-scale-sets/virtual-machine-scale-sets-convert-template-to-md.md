---
title: Převod šablony škálovací sady pro použití spravovaného disku
description: Převeďte šablonu Azure Resource Manager sady škálování virtuálního počítače na spravovanou šablonu sady škálování disku.
keywords: škálovací sady virtuálních počítačů
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 03cbe4eb56f3b3b99f87048b699f76b30b7937c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85373960"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Převod šablony sady škálování na spravovanou šablonu sady škálování disku

Zákazníci, kteří mají Správce prostředků šablonu pro vytvoření sady škálování bez použití spravovaného disku, můžou chtít upravit ji tak, aby používala spravovaný disk. V tomto článku se dozvíte, jak používat spravované disky jako příklad žádosti o přijetí změn ze [šablon pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates), což je úložiště založené na komunitě pro ukázkové správce prostředků šablony. Úplný požadavek na získání dat najdete tady: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) a příslušné části rozdílu jsou uvedené níže a vysvětlení:

## <a name="making-the-os-disks-managed"></a>Správa disků s operačním systémem

V následujících rozdílech se odeberou některé proměnné týkající se účtu úložiště a vlastností disku. Typ účtu úložiště už není potřebný (výchozí nastavení je Standard_LRS), ale můžete ho zadat v případě potřeby. Se spravovaným diskem se podporují jenom Standard_LRS a Premium_LRS. Ve staré šabloně se použila nová přípona účtu úložiště, jedinečné pole řetězců a počet SA pro generování názvů účtů úložiště. Tyto proměnné už nejsou potřebné v nové šabloně, protože spravovaný disk automaticky vytváří účty úložiště v zastoupení zákazníka. Podobně název kontejneru VHD a název disku operačního systému už nejsou potřeba, protože spravovaný disk automaticky pojmenovává základní kontejnery a disky objektů BLOB úložiště.

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


V následujících rozdílech se rozhraní API COMPUTE aktualizuje na verzi 2016-04-30-Preview, což je nejstarší požadovaná verze pro podporu spravovaných disků se sadami škálování. V případě potřeby můžete použít nespravované disky v nové verzi rozhraní API se starou syntaxí. Pokud aktualizujete jenom výpočetní verzi rozhraní API a nezměníte nic jiného, Šablona by měla dál fungovat jako dřív.

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

V následujících rozdílech se prostředek účtu úložiště odebere z pole prostředků úplně. Prostředek se už nepotřebuje, protože se automaticky vytvoří spravovaný disk.

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

V následujících rozdílech jsme zjistili, že odebíráme klauzuli on klauzule on, která odkazuje z rozsahu nastaveného na smyčku, která vytvořila účty úložiště. Ve staré šabloně to mělo za následek vytvoření účtů úložiště před zahájením vytváření sady škálování, ale tato klauzule už není potřebná pro spravovaný disk. Vlastnost Containers Containers (kontejnery VHD) se odebere taky spolu s vlastností název disku operačního systému, protože tyto vlastnosti se automaticky zpracovávají pod správcem na základě spravovaného disku. `"managedDisk": { "storageAccountType": "Premium_LRS" }`Pokud jste potřebovali disky s operačním systémem Premium, můžete přidat do konfigurace "osDisk". Prémiové disky můžou využívat jenom virtuální počítače s velkým nebo malým písmenem v SKU virtuálního počítače.

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

V konfiguraci sady škálování neexistuje žádná explicitní vlastnost, která určuje, jestli se má použít spravovaný nebo nespravovaný disk. Sada škálování ví, která se má použít, na základě vlastností, které jsou k dispozici v profilu úložiště. Proto je důležité při úpravách šablony, aby se zajistilo, že jsou správné vlastnosti v profilu úložiště sady škálování.


## <a name="data-disks"></a>Datové disky

Pomocí výše uvedených změn používá sada škálování spravované disky pro disk s operačním systémem, ale informace o datových discích? Chcete-li přidat datové disky, přidejte do části "storageProfile" vlastnost datadisks na stejné úrovni jako "osDisk". Hodnota vlastnosti je seznam JSON objektů, z nichž každá má vlastnosti "LUN" (která musí být jedinečná na datový disk na virtuálním počítači), "createOption" ("Empty" je aktuálně jedinou podporovanou možností) a "diskSizeGB" (velikost disku v gigabajtech; musí být větší než 0 a menší než 1024), jako v následujícím příkladu:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Pokud `n` v tomto poli zadáte disky, každý virtuální počítač v sadě škálování získá `n` datové disky. Upozorňujeme však, že tyto datové disky jsou nezpracovaná zařízení. Nejsou naformátované. Aby bylo možné disky před použitím připojit, rozdělit na oddíly a naformátovat je. Volitelně můžete také zadat `"managedDisk": { "storageAccountType": "Premium_LRS" }` v každém objektu datového disku, který určuje, že by měl být datový disk Premium. Prémiové disky můžou využívat jenom virtuální počítače s velkým nebo malým písmenem v SKU virtuálního počítače.

Další informace o použití datových disků se sadami škálování najdete v [tomto článku](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Další kroky
Například Správce prostředků šablon pomocí sady škálování, vyhledejte "VMSS" v [úložišti GitHub šablon pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates).

Obecné informace najdete na [hlavní cílové stránce pro sady škálování](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

