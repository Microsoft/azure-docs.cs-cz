---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 59c888b1f18b1c9f700e1b79c4786a466f2c55fb
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821962"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Použití Managed Disks v šablonách Azure Resource Manager

Tento dokument vás provede rozdíly mezi spravovanými a nespravovanými disky při použití šablon Azure Resource Manager k zřizování virtuálních počítačů. Příklady vám pomůžou aktualizovat existující šablony, které používají nespravované disky na spravované disky. Pro referenci používáme jako vodítko šablonu [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) . Můžete zobrazit šablonu pomocí [spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) i předchozí verze s použitím [nespravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) , pokud byste je chtěli přímo porovnat.

## <a name="unmanaged-disks-template-formatting"></a>Formátování šablony nespravovaných disků

Začněte podíváme se na to, jak jsou nasazené nespravované disky. Při vytváření nespravovaných disků potřebujete účet úložiště pro uchovávání souborů VHD. Můžete vytvořit nový účet úložiště, nebo použít jiný, který už existuje. V tomto článku se dozvíte, jak vytvořit nový účet úložiště. Vytvořte prostředek účtu úložiště v bloku Resources, jak je znázorněno níže.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

V rámci objektu virtuálního počítače přidejte závislost na účet úložiště, abyste měli jistotu, že se vytvoří před virtuálním počítačem. V části `storageProfile` zadejte úplný identifikátor URI umístění virtuálního pevného disku (VHD), který odkazuje na účet úložiště a je nutný pro disk s operačním systémem a všechny datové disky.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formátování šablon spravovaných disků

V případě Azure Managed Disks se disk stane prostředkem nejvyšší úrovně a již nepotřebuje, aby uživatel vytvořil účet úložiště. Spravované disky se nejdřív vystavily ve verzi rozhraní `2016-04-30-preview` API, jsou dostupné ve všech dalších verzích rozhraní API a teď se jedná o výchozí typ disku. V následujících částech se dozvíte o výchozím nastavení a podrobné informace o tom, jak tyto disky dále upravovat.

> [!NOTE]
> Doporučuje se použít verzi rozhraní API, která je novější než `2016-04-30-preview`, protože došlo k narušení změn mezi `2016-04-30-preview` a `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Pokud chcete vytvořit virtuální počítač se spravovanými disky, nebudete už muset vytvořit prostředek účtu úložiště a tento prostředek můžete aktualizovat následujícím způsobem. Konkrétně Všimněte si, že `apiVersion` odráží `2017-03-30` a `osDisk` a `dataDisks` již neodkazují na konkrétní identifikátor URI pro virtuální pevný disk. Při nasazování bez zadání dalších vlastností disk použije typ úložiště na základě velikosti virtuálního počítače. Pokud například používáte velikost virtuálního počítače podporujícího prémii (velikosti s "s" v názvu, například Standard_D2s_v3), systém použije úložiště Premium_LRS. Pomocí nastavení SKU disku zadejte typ úložiště. Pokud není zadán žádný název, formát `<VMName>_OsDisk_1_<randomstring>` pro disk s operačním systémem a `<VMName>_disk<#>_<randomstring>` pro každý datový disk. Ve výchozím nastavení je služba Azure Disk Encryption zakázaná; ukládání do mezipaměti je pro disk s operačním systémem pro čtení a zápis a pro datové disky žádné. Můžete si všimnout, že následující příklad je stále závislá na účtu úložiště, ale je k dispozici pouze pro úložiště diagnostiky a není pro diskové úložiště potřeba.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Použití prostředku spravovaného disku nejvyšší úrovně

Jako alternativu k zadání konfigurace disku v objektu virtuálního počítače můžete vytvořit diskový prostředek na nejvyšší úrovni a připojit ho jako součást vytváření virtuálního počítače. Například můžete vytvořit prostředek disku následujícím způsobem, který se použije jako datový disk.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

V rámci objektu virtuálního počítače odkazujte na objekt disku, který se má připojit. Zadáním ID prostředku spravovaného disku vytvořeného ve vlastnosti `managedDisk` povolíte vytvoření přílohy disku jako virtuálního počítače. `apiVersion` pro prostředek virtuálního počítače je nastavená na `2017-03-30`. Přidala se závislost na prostředku disku, aby se zajistilo jejich úspěšné vytvoření před vytvořením virtuálního počítače. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Vytvoření spravovaných skupin dostupnosti pomocí virtuálních počítačů pomocí spravovaných disků

Pokud chcete vytvořit spravované skupiny dostupnosti s virtuálními počítači pomocí spravovaných disků, přidejte objekt `sku` do prostředku skupiny dostupnosti a nastavte vlastnost `name` na `Aligned`. Tato vlastnost zajišťuje, aby byly disky pro každý virtuální počítač dostatečně izolované od sebe navzájem, aby se předešlo jednomu bodu selhání. Všimněte si také, že `apiVersion` prostředku skupiny dostupnosti je nastavena na hodnotu `2018-10-01`.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>SSD úrovně Standard disky

Níže jsou uvedeny parametry, které jsou potřeba v šabloně Správce prostředků k vytvoření SSD úrovně Standard disků:

* *apiVersion* pro Microsoft. COMPUTE musí být nastavená na `2018-04-01` (nebo novější).
* Jako `StandardSSD_LRS` zadejte *managedDisk. storageAccountType.*

Následující příklad ukazuje oddíl *Properties. storageProfile. osDisk* pro virtuální počítač, který používá SSD úrovně Standard disky:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Kompletní příklad vytvoření SSD úrovně Standard disku pomocí šablony najdete v tématu [Vytvoření virtuálního počítače z image Windows s datovými disky SSD úrovně Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Další scénáře a přizpůsobení

Úplné informace o specifikacích REST API najdete v [dokumentaci k vytvoření spravovaného disku REST API](/rest/api/manageddisks/disks/disks-create-or-update). Najdete v nich další scénáře a také výchozí a přijatelné hodnoty, které je možné odeslat do rozhraní API prostřednictvím nasazení šablon. 

## <a name="next-steps"></a>Další kroky

* U úplných šablon, které používají spravované disky, navštivte následující odkazy na úložiště Azure pro rychlý Start.
    * [Virtuální počítač s Windows se spravovaným diskem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Virtuální počítač se systémem Linux se spravovaným diskem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Další informace o spravovaných discích najdete v dokumentu [Přehled služby Azure Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md) .
* Projděte si referenční dokumentaci k šabloně pro prostředky virtuálních počítačů, a to návštěvou [referenčního dokumentu šablony Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) .
* Projděte si referenční dokumentaci k šabloně pro diskové prostředky, která se nachází v [referenčním dokumentu šablony Microsoft. COMPUTE/disks](/azure/templates/microsoft.compute/disks) .
* Informace o tom, jak používat spravované disky ve službě Azure Virtual Machine Scale Sets, najdete v dokumentu [použití datových disků se sadami škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) .
