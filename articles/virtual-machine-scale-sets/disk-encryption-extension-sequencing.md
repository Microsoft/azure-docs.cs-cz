---
title: Azure Disk Encryption a škálování virtuálních počítačů Azure sestavuje sekvenování rozšíření
description: Tento článek obsahuje pokyny k povolení microsoft azure diskšifrování pro linuxové virtuální počítače IaaS.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279030"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Použití Azure Disk Encryption s řazením rozšíření škálovací sady virtuálních počítačů

Rozšíření, jako je šifrování disku Azure lze přidat do škálování virtuálních počítačů Azure v zadaném pořadí. Chcete-li tak učinit, použijte [sekvencování rozšíření](virtual-machine-scale-sets-extension-sequencing.md). 

Obecně platí, že šifrování by mělo být použito na disku:

- Po rozšíření nebo vlastní skripty, které připravují disky nebo svazky.
- Před rozšířeními nebo vlastními skripty, které přistupují k datům na šifrovaných discích nebo svazcích nebo je spotřebovávají.

V obou případech `provisionAfterExtensions` vlastnost určuje, které rozšíření by měly být přidány později v pořadí.

## <a name="sample-azure-templates"></a>Ukázkové šablony Azure

Pokud chcete, aby se po jiném rozšíření `provisionAfterExtensions` použilo azure diskové šifrování, vložte vlastnost do rozšíření AzureDiskEncryption. 

Zde je příklad pomocí "CustomScriptExtension", skriptu Powershellu, který inicializuje a formátuje disk systému Windows, následovaný "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Pokud chcete, aby bylo šifrování disku Azure `provisionAfterExtensions` použito před jiným rozšířením, vložte vlastnost do bloku rozšíření, který chcete sledovat.

Tady je příklad s použitím "AzureDiskEncryption" následovaný "VMDiagnosticsSettings", rozšíření, které poskytuje možnosti monitorování a diagnostiky na virtuálním počítači Azure založené na Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Podrobnější šablonu naleznete v tématu:
* Použití rozšíření Azure Disk Encryption po vlastním skriptu prostředí, který disk formátuje (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Další kroky
- Další informace o sekvenování rozšíření: [Zřizování rozšíření sekvence ve škálovacích sadách virtuálních strojů](virtual-machine-scale-sets-extension-sequencing.md).
- Další informace `provisionAfterExtensions` o vlastnosti: [Microsoft.Compute virtualMachineScaleSets/extensions odkaz na šablonu](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Šifrování disku Azure pro škálovací sady virtuálních počítačů](disk-encryption-overview.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí příkazového příkazového příkazu Azure](disk-encryption-cli.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí Azure PowerShellu](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure](disk-encryption-key-vault.md)
