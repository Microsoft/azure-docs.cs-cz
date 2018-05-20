# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Pomocí spravovaného disky v šablonách Azure Resource Manageru

Tento dokument vás provede rozdíly mezi spravovanými a nespravovanými disky při zřizování virtuálních počítačů pomocí šablony Azure Resource Manager. To vám pomůže aktualizovat existující šablony, které používají disků nespravované na spravované disky. Pro referenci používáme [101-vm jednoduché – windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) šablony jako vodítko. Můžete zobrazit šablonu pomocí obou [discích spravovaných](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) a předchozí verze pomocí [nespravované disky](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Pokud byste chtěli přímo jejich porovnání.

## <a name="unmanaged-disks-template-formatting"></a>Nespravované disky šablony, formátování

Pokud chcete začít, budeme se podívejte na tom, jak nespravované disky jsou nasazeny. Při vytváření nespravované disky, potřebujete účet úložiště pro soubory virtuálního pevného disku. Můžete vytvořit nový účet úložiště nebo použít jednu, která již existuje. Tento článek vám ukáže, jak vytvořit nový účet úložiště. K tomu musíte prostředek účet úložiště v bloku prostředků, jak je uvedeno níže.

```
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

V rámci objektu virtuálního počítače potřebujeme závislost na účtu úložiště a ověřte, že je vytvořen před virtuální počítač. V rámci `storageProfile` části, jsme pak zadejte úplný identifikátor URI umístění virtuálního pevného disku, který odkazuje na účet úložiště a je potřebná pro disk operačního systému a všech datových disků. 

```
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

S Azure spravované disky disk stane nejvyšší úrovně prostředků a už vyžaduje účet úložiště, který být vytvořený uživatelem. Spravované disky nejprve byly vystaveny v `2016-04-30-preview` verze rozhraní API, jsou k dispozici ve všech dalších verzích rozhraní API a se teď výchozí typ disku. Následující části provede výchozí nastavení a jsou upřesněny postupy k dalšímu přizpůsobení vaše disky.

> [!NOTE]
> Doporučujeme použít verzi rozhraní API pozdější než `2016-04-30-preview` jako došlo k narušující změny mezi `2016-04-30-preview` a `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Pokud chcete vytvořit virtuální počítač s spravované disky, už musíte vytvořit úložiště účtu prostředků a následujícím způsobem můžete aktualizovat prostředek virtuálního počítače. Specificky Upozorňujeme, že `apiVersion` odráží `2017-03-30` a `osDisk` a `dataDisks` nadále odkazovat na konkrétní identifikátoru URI virtuálního pevného disku. Při nasazování bez zadání dalších vlastností, bude disk používat [úložiště LRS standardní](../articles/storage/common/storage-redundancy.md). Pokud není zadán žádný název, jak dlouho trvá formát `<VMName>_OsDisk_1_<randomstring>` pro disk operačního systému a `<VMName>_disk<#>_<randomstring>` pro každý datový disk. Ve výchozím nastavení je Azure disk encryption zakázaný. ukládání do mezipaměti je pro čtení a zápis pro disk operačního systému a jeden pro datové disky. Můžete si povšimnout v následujícím příkladu, že je stále závislost účtu úložiště, i když toto je pouze pro úložiště diagnostiky a není potřeba ukládání na disk.

```
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

### <a name="using-a-top-level-managed-disk-resource"></a>Použití spravovaných disků na nejvyšší úrovni prostředků

Jako alternativu k určení konfiguraci disku v objektu virtuálního počítače můžete vytvořit nejvyšší úrovně diskový prostředek a připojte ji jako součást vytvoření virtuálního počítače. Například můžeme vytvořit prostředek disku takto chcete použít jako datový disk.

```
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

V rámci objekt virtuálního počítače jsme pak můžete odkazovat tento objekt disku má být přiřazena. Určení Identifikátoru zdroje spravovaného disku jsme vytvořili v `managedDisk` vlastnost umožňuje připojení disku při vytváření virtuálního počítače. Všimněte si, že `apiVersion` pro virtuální počítač zdroj je nastaven pro `2017-03-30`. Všimněte si také, že vytvořili jsme závislost na prostředku disku, ujistěte se, že je úspěšně vytvořen před vytvořením virtuálních počítačů. 

```
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Vytvoření skupiny dostupnosti spravované s virtuálními počítači pomocí spravovaných disků

K vytvoření spravovaného dostupnost sady s virtuálními počítači pomocí spravovaných disků, přidejte `sku` objektu na dostupnost prostředků a nastavte a `name` vlastnost `Aligned`. Tím se zajistí disky pro jednotlivé virtuální počítače dostatečně od sebe navzájem oddělené k Vyhýbejte se jediným bodů selhání. Všimněte si také, že `apiVersion` pro dostupnost nastavení prostředku je nastaven na `2017-03-30`.

```
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

### <a name="additional-scenarios-and-customizations"></a>Další scénáře a přizpůsobení

K vyhledání úplné informace o specifikacích REST API, přečtěte si [vytvoření spravovaného disku dokumentace k REST API](/rest/api/manageddisks/disks/disks-create-or-update). Zjistíte další scénáře, a také výchozí a přijatelných hodnot, které se dají odeslat do rozhraní API prostřednictvím šablony nasazení. 

## <a name="next-steps"></a>Další postup

* Pro úplnou šablony, které používají spravovaný disky získáte pomocí následujících odkazů v úložišti Azure rychlý start.
    * [Virtuální počítač s Windows spravované disku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Virtuální počítač s Linuxem pomocí spravovaného disku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Úplný seznam spravovaných disků na šablony](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Přejděte [přehled disky spravované Azure](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentu další informace o spravovaných disky.
* Zkontrolujte šablonu referenční dokumentaci pro prostředky virtuálního počítače navštivte stránky [odkaz na šablonu Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) dokumentu.
* Zkontrolujte šablonu referenční dokumentaci pro prostředky disku navštivte stránky [odkaz na šablonu Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks) dokumentu.
* Informace o tom, jak používat spravovaného disky v sadách škálování virtuálních počítačů Azure, najdete v článku [datových disků pomocí sady škálování](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) dokumentu.
