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
ms.openlocfilehash: c7db8eaf57bf29e17b4543e99a44655030aa6172
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979390"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Použití spravovaných disků v šablonách Azure Resource Manageru

Tento dokument vás provede rozdíly mezi spravovanými a nespravovanými disky při zřizování virtuálních počítačů pomocí šablon Azure Resource Manageru. Příklady můžete aktualizace existujících šablon, které používají nespravované disky na managed disks. Pro srovnání se používá [101-vm jednoduché – windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) šablony jako vodítko. Můžete zobrazit pomocí obou [spravované disky](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) a předchozí verze pomocí [nespravované disky](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Pokud byste chtěli přímo jejich porovnání.

## <a name="unmanaged-disks-template-formatting"></a>Nespravované disky šablony formátování

Pokud chcete začít, můžeme vzít podívat, jak nespravované disky jsou nasazené. Při vytváření nespravované disky, budete potřebovat účet úložiště pro uložení souborů virtuálního pevného disku. Můžete vytvořit nový účet úložiště nebo použijte takový, který již existuje. Tento článek ukazuje, jak vytvořit nový účet úložiště. Vytvořte prostředek účtu úložiště v bloku prostředky, jak je znázorněno níže.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

V rámci objektu VM přidáte závislost na účet úložiště a ověřte, že je vytvořen před virtuální počítač. V rámci `storageProfile` části, zadejte úplný identifikátor URI umístění virtuálního pevného disku, který odkazuje na účet úložiště a je potřebná pro disk s operačním systémem a všechny datové disky.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
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

## <a name="managed-disks-template-formatting"></a>Spravované disky šablony formátování

Se službou Azure Managed Disks disku stane prostředek nejvyšší úrovně a už vyžaduje účet úložiště vytvořit uživatele. Spravované disky se nejprve odhalily v `2016-04-30-preview` verze rozhraní API, jsou k dispozici ve všech budoucích verzích rozhraní API a jsou teď výchozí typ disku. Následující části provede výchozí nastavení a podrobnosti o tom, jak dále přizpůsobit váš disky.

> [!NOTE]
> Doporučuje se použít verze rozhraní API novější než `2016-04-30-preview` jako došlo k narušující změny mezi `2016-04-30-preview` a `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Vytvoření virtuálního počítače se spravovanými disky, je už nebude potřeba vytvořit úložiště účtů prostředků a následujícím způsobem můžete aktualizovat prostředku vašeho virtuálního počítače. Konkrétně Všimněte si, že `apiVersion` odráží `2017-03-30` a `osDisk` a `dataDisks` už nebude odkazovat na konkrétní identifikátor URI virtuálního pevného disku. Při nasazení bez určení dalších vlastností, bude disk používat typ úložiště na základě velikosti virtuálního počítače. Například pokud používáte Premium podporuje velikost virtuálního počítače (velikosti s "s" v názvu, jako je například Standard_D2s_v3) pak systém použije úložiště Premium_LRS. Sku nastavení disku použijte k určení typu úložiště. Pokud není zadán žádný název, má formát `<VMName>_OsDisk_1_<randomstring>` pro disk s operačním systémem a `<VMName>_disk<#>_<randomstring>` pro každý datový disk. Ve výchozím nastavení je zakázaný Azure disk encryption. ukládání do mezipaměti je pro čtení a zápis disk s operačním systémem a datové disky. Můžete si všimnout v následujícím příkladu je stále závislost účtu úložiště, i když toto je pouze pro úložiště pro diagnostiku a pro diskové úložiště není potřeba.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="using-a-top-level-managed-disk-resource"></a>Pomocí prostředek nejvyšší úrovně spravovaného disku

Jako alternativu k určení konfigurace disku v objektu virtuálního počítače můžete vytvořit prostředek nejvyšší úrovně disk a připojit ho jako součást vytváření virtuálních počítačů. Můžete například vytvořit prostředek disku následujícím způsobem, který se použije jako datový disk.

```json
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
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

V rámci objektu VM odkazujete na objekt disk, který chcete připojit. Určení ID prostředku spravovaného disku v vytvoří `managedDisk` vlastnost umožňuje připojení disku při vytváření virtuálního počítače. `apiVersion` Pro virtuální počítač zdroj je nastaven pro `2017-03-30`. Ujistěte se, že je úspěšně vytvořen před vytvořením virtuálního počítače se přidá závislost na diskovém prostředku. 

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Vytvoření spravované skupiny dostupnosti s virtuálními počítači pomocí spravovaných disků

Vytvoření spravované skupiny dostupnosti s virtuálními počítači pomocí spravované disky, přidejte `sku` objektu na dostupnosti prostředků a nastavit `name` vlastnost `Aligned`. Tato vlastnost zajistí, že disky pro každý virtuální počítač jsou dostatečně izolované od sebe navzájem k Vyhýbejte se jediným bodů selhání. Všimněte si také, `apiVersion` pro dostupnosti prostředků je nastavena na `2017-03-30`.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
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

### <a name="standard-ssd-disks"></a>Standardní disky SSD

V následující tabulce jsou parametry potřebné v šabloně Resource Manageru k vytvoření standardní disky SSD:

* *verze rozhraní API* pro Microsoft.Compute musí být nastavena jako `2018-04-01` (nebo novější)
* Zadejte *managedDisk.storageAccountType* jako `StandardSSD_LRS`

Následující příklad ukazuje *properties.storageProfile.osDisk* oddílu pro virtuální počítač, který používá standardní disky SSD:

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

Úplnou šablonu příklad toho, jak vytvořit disk SSD na úrovni Standard se šablonou, naleznete v tématu [vytvoření virtuálního počítače z Image Windows pomocí standardních datových disků SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Přizpůsobení a další scénáře

K vyhledání úplné informace o specifikacích rozhraní REST API, podívejte se prosím [vytvoření spravovaného disku dokumentace k rozhraní REST API](/rest/api/manageddisks/disks/disks-create-or-update). Další scénáře, stejně jako výchozí a přijatelné hodnoty, které můžete odeslat do rozhraní API prostřednictvím nasazení šablon najdete. 

## <a name="next-steps"></a>Další postup

* Pro úplnou šablony, které používají spravované disky naleznete pod těmito odkazy úložiště Azure rychlý start.
    * [Virtuální počítač Windows při použití spravovaného disku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Virtuální počítač s Linuxem při použití spravovaného disku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Úplný seznam spravovaného disku šablony](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Přejděte [Přehled služby Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentu další informace o spravovaných discích.
* Zkontrolujte referenční dokumentace šablony pro prostředky virtuálních počítačů najdete [referenčními informacemi k šablonám Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) dokumentu.
* Zkontrolujte referenční dokumentace šablony pro diskové prostředky najdete [referenčními informacemi k šablonám Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks) dokumentu.
* Informace o tom, jak používat spravované disky ve škálovacích sadách virtuálních počítačů Azure najdete v článku [použití datových disků se škálovacími sadami](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) dokumentu.
