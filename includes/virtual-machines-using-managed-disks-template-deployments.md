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
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037944"
---
Tento dokument vás provede rozdíly mezi spravovanými a nespravovanými disky při použití šablon Azure Resource Manager u zřizování virtuálních počítačů. Příklady vám pomohou aktualizovat existující šablony, které používají nespravované disky na spravované disky. Pro referenci používáme jako vodítko šablonu [101-vm-simple-windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) Šablonu můžete zobrazit pomocí [spravovaných disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) i předchozí verze pomocí [nespravovaných disků,](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) pokud je chcete přímo porovnat.

## <a name="unmanaged-disks-template-formatting"></a>Formátování šablony nespravované disky

Chcete-li začít, podívejme se na to, jak jsou nasazeny nespravované disky. Při vytváření nespravovaných disků potřebujete účet úložiště pro uložení souborů v pevném disku. Můžete vytvořit nový účet úložiště nebo použít účet, který již existuje. V tomto článku se zobrazí, jak vytvořit nový účet úložiště. Vytvořte prostředek účtu úložiště v bloku prostředků, jak je znázorněno níže.

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

V rámci objektu virtuálního počítače přidejte závislost na účtu úložiště, abyste zajistili, že se vytvoří před virtuálním počítačem. V `storageProfile` části zadejte úplný identifikátor URI umístění virtuálního pevného disku, který odkazuje na účet úložiště a je potřebný pro disk operačního systému a všechny datové disky.

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

## <a name="managed-disks-template-formatting"></a>Formátování šablony spravovaných disků

Se spravovanými disky Azure se disk stane prostředkem nejvyšší úrovně a už nevyžaduje, aby uživatel vytvořil účet úložiště. Spravované disky byly nejprve vystaveny ve verzi `2016-04-30-preview` rozhraní API, jsou k dispozici ve všech následujících verzích rozhraní API a jsou nyní výchozím typem disku. Následující části procházejí výchozím nastavením a podrobně popisují, jak disky dále přizpůsobit.

> [!NOTE]
> Doporučujese použít verzi rozhraní API `2016-04-30-preview` později, než `2016-04-30-preview` `2017-03-30`jako došlo k přerušení změny mezi a .
>
>

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Chcete-li vytvořit virtuální virtuální počítače se spravovanými disky, už nemusíte vytvářet prostředek účtu úložiště. Odkazující na příklad šablony níže, existují určité rozdíly od předchozích nemanged disk příklady na vědomí:

- Jedná `apiVersion` se o verzi, která podporuje spravované disky.
- `osDisk`a `dataDisks` již neodkazují na konkrétní identifikátor URI pro virtuální pevný disk.
- Při nasazování bez zadání dalších vlastností bude disk používat typ úložiště na základě velikosti virtuálního počítače. Například pokud používáte velikost virtuálního počítače, který podporuje úložiště premium (velikosti s "s" v jejich názvu, jako je Standard_D2s_v3), pak premium disky budou nakonfigurovány ve výchozím nastavení. Toto nastavení můžete změnit pomocí nastavení sku disku a určit typ úložiště.
- Pokud není zadán žádný název disku, přebírá `<VMName>_OsDisk_1_<randomstring>` formát pro disk `<VMName>_disk<#>_<randomstring>` operačního systému a pro každý datový disk.
  - Pokud se virtuální modul vytváří z vlastní bitové kopie, načte se výchozí nastavení pro typ účtu úložiště a název disku z vlastností disku definovaných ve vlastním prostředku bitové kopie. Ty mohou být přepsány zadáním hodnot pro tyto v šabloně.
- Ve výchozím nastavení je šifrování disku Azure zakázáno.
- Ve výchozím nastavení je ukládání disku do mezipaměti pro disk operačního systému čtení a zápis a žádné pro datové disky.
- V níže uvedeném příkladu je stále závislost účtu úložiště, i když je to pouze pro ukládání diagnostiky a není potřeba pro diskové úložiště.

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

Jako alternativu k určení konfigurace disku v objektu virtuálního počítače můžete vytvořit prostředek disku nejvyšší úrovně a připojit jej jako součást vytváření virtuálního počítače. Můžete například vytvořit diskový prostředek následujícím způsobem, který se použije jako datový disk.

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

V rámci objektu virtuálního počítače odkazovat na objekt disku, který má být připojen. Určení ID prostředku spravovaného disku vytvořeného `managedDisk` ve vlastnosti umožňuje připojení disku při vytvoření virtuálního počítače. Pro `apiVersion` prostředek virtuálního virtuálního `2017-03-30`min. je nastavena na . Závislost na prostředku disku je přidána, aby bylo zajištěno, že je úspěšně vytvořen před vytvořením virtuálního počítače. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Vytváření spravovaných sad dostupnosti pomocí virtuálních počítačů pomocí spravovaných disků

Chcete-li vytvořit sady spravované dostupnosti pomocí virtuálních počítačů pomocí spravovaných disků, přidejte `sku` objekt do prostředku sady dostupnosti a nastavte `name` vlastnost na `Aligned`. Tato vlastnost zajišťuje, že disky pro každý virtuální počítače jsou dostatečně izolované od sebe navzájem, aby se zabránilo jednotlivé body selhání. Všimněte si `apiVersion` také, že pro `2018-10-01`zdroj pro dostupnost nastavit prostředek .

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

### <a name="standard-ssd-disks"></a>Standardní Disky SSD

Níže jsou uvedeny parametry potřebné v šabloně Správce prostředků k vytvoření standardních disků SSD:

* *apiVersion* pro Microsoft.Compute musí `2018-04-01` být nastavena jako (nebo novější)
* Zadejte *managedDisk.storageAccountType* jako`StandardSSD_LRS`

Následující příklad ukazuje oddíl *properties.storageProfile.osDisk* pro virtuální počítače, který používá standardní disky SSD:

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

Úplný příklad šablony, jak vytvořit standardní disk SSD se šablonou, najdete v [tématu Vytvoření virtuálního počítače z bitové kopie systému Windows se standardními datovými disky SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Další scénáře a vlastní nastavení

Úplné informace o specifikacích rozhraní REST API naleznete v [dokumentaci k rozhraní REST API spravovaného disku](/rest/api/manageddisks/disks/disks-create-or-update). Najdete další scénáře, stejně jako výchozí a přijatelné hodnoty, které lze odeslat do rozhraní API prostřednictvím nasazení šablony. 

## <a name="next-steps"></a>Další kroky

* Úplné šablony, které používají spravované disky, najdete na následujících odkazech azure quickstart repo.
    * [Virtuální modul Windows se spravovaným diskem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Virtuální počítač s Linuxem se spravovaným diskem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Další informace o spravovaných discích najdete v dokumentu [Přehled spravovaných disků Azure.](../articles/virtual-machines/windows/managed-disks-overview.md)
* Projděte si referenční dokumentaci k šabloně pro prostředky virtuálních počítačů na stránce referenčního dokumentu [šablony Microsoft.Compute/virtualMachines.](/azure/templates/microsoft.compute/virtualmachines)
* Zkontrolujte referenční dokumentaci k disku pomocí referenčního dokumentu [šablony Microsoft.Compute/disks.](/azure/templates/microsoft.compute/disks)
* Informace o tom, jak používat spravované disky ve škálovacích sadách virtuálních počítačů Azure, najdete v dokumentu [Use data disků with scale sets.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks)
