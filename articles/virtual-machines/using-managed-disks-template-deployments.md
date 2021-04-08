---
title: Nasazení disků pomocí šablon Azure Resource Manager
description: Podrobnosti o použití spravovaných a nespravovaných disků v šablonách Azure Resource Manager pro virtuální počítače Azure.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 7c66a8b8483673a9d8fbdc9922b9cc377781bab3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91976656"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>Použití disků v šablonách Azure Resource Manager

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

V rámci objektu virtuálního počítače přidejte závislost na účet úložiště, abyste měli jistotu, že se vytvoří před virtuálním počítačem. V `storageProfile` části zadejte úplný identifikátor URI umístění virtuálního pevného disku, který odkazuje na účet úložiště a který je nutný pro disk s operačním systémem a všechny datové disky.

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

V případě Azure Managed Disks se disk stane prostředkem nejvyšší úrovně a již nepotřebuje, aby uživatel vytvořil účet úložiště. Spravované disky se nejdřív vystavily ve `2016-04-30-preview` verzi rozhraní API, jsou dostupné ve všech dalších verzích rozhraní API a teď jsou výchozí typ disku. V následujících částech se dozvíte o výchozím nastavení a podrobné informace o tom, jak tyto disky dále upravovat.

> [!NOTE]
> Doporučuje se použít verzi rozhraní API novější než `2016-04-30-preview` v případě, že došlo k narušení změn mezi `2016-04-30-preview` a `2017-03-30` .
>
>

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Pokud chcete vytvořit virtuální počítač se spravovanými disky, nebudete už muset vytvořit prostředek účtu úložiště. Odkazy na níže uvedený příklad šablony obsahuje několik rozdílů oproti předchozím příkladům nespravovaného disku, které byste si poznamenali:

- `apiVersion`Je verze, která podporuje spravované disky.
- `osDisk` a `dataDisks` už neodkazuje na konkrétní identifikátor URI pro virtuální pevný disk.
- Při nasazování bez zadání dalších vlastností disk použije typ úložiště na základě velikosti virtuálního počítače. Pokud například používáte velikost virtuálního počítače, která podporuje Premium Storage (velikosti s "s" v názvu, například Standard_D2s_v3), budou standardně nakonfigurované prémiové disky. Tuto změnu můžete změnit pomocí nastavení skladové položky (SKU) disku pro určení typu úložiště.
- Pokud není zadaný žádný název disku, bude mít formát `<VMName>_OsDisk_1_<randomstring>` pro disk s operačním systémem a `<VMName>_disk<#>_<randomstring>` pro každý datový disk.
  - Pokud se virtuální počítač vytváří z vlastní image, výchozí nastavení pro typ účtu úložiště a název disku se načtou z vlastností disku definovaných v prostředku vlastní image. Ty mohou být přepsány zadáním hodnot pro tyto hodnoty v šabloně.
- Ve výchozím nastavení je služba Azure Disk Encryption zakázaná.
- Ve výchozím nastavení je ukládání do mezipaměti disku pro disk s operačním systémem nastaveno na čtení a zápis a pro datové disky není k dispozici.
- V následujícím příkladu je stále závislá na účtu úložiště, ale je to jenom pro úložiště diagnostiky a není potřeba pro úložiště na disku.

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

V rámci objektu virtuálního počítače odkazujte na objekt disku, který se má připojit. Zadáním ID prostředku spravovaného disku vytvořeného ve `managedDisk` vlastnosti umožníte vytvoření přílohy disku jako virtuálního počítače. `apiVersion`Pro prostředek virtuálního počítače je nastavená na `2017-03-30` . Přidala se závislost na prostředku disku, aby se zajistilo jejich úspěšné vytvoření před vytvořením virtuálního počítače. 

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

Pokud chcete vytvořit spravované skupiny dostupnosti s virtuálními počítači pomocí spravovaných disků, přidejte `sku` objekt do prostředku skupiny dostupnosti a nastavte `name` vlastnost na `Aligned` . Tato vlastnost zajišťuje, aby byly disky pro každý virtuální počítač dostatečně izolované od sebe navzájem, aby se předešlo jednomu bodu selhání. Všimněte si také, že `apiVersion` u prostředku skupiny dostupnosti je nastavena na hodnotu `2018-10-01` .

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

* *apiVersion* pro Microsoft. COMPUTE musí být nastavené jako `2018-04-01` (nebo novější).
* Zadejte *managedDisk. storageAccountType* jako `StandardSSD_LRS`

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
* Další informace o spravovaných discích najdete v dokumentu [Přehled služby Azure Managed disks](managed-disks-overview.md) .
* Projděte si referenční dokumentaci k šabloně pro prostředky virtuálních počítačů, a to návštěvou [referenčního dokumentu šablony Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) .
* Projděte si referenční dokumentaci k šabloně pro diskové prostředky, která se nachází v [referenčním dokumentu šablony Microsoft. COMPUTE/disks](/azure/templates/microsoft.compute/disks) .
* Informace o tom, jak používat spravované disky ve službě Azure Virtual Machine Scale Sets, najdete v dokumentu [použití datových disků se sadami škálování](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) .