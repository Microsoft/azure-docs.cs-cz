---
title: Sekvence rozšíření pro Azure Disk Encryption a Azure Virtual Machine Scale Sets
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s IaaS Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279030"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů

Rozšíření, jako je Azure Disk Encryption, se dají přidat do sady škálování virtuálních počítačů Azure v zadaném pořadí. Provedete to tak, že použijete [sekvencování rozšíření](virtual-machine-scale-sets-extension-sequencing.md). 

Obecně platí, že by se mělo použít šifrování na disk:

- Po rozšíření nebo vlastních skriptech, které připravují disky nebo svazky.
- Než rozšíření nebo vlastní skripty, které přistupují k datům na šifrovaných discích nebo svazcích, nebo je z nich spotřebují.

V obou případech `provisionAfterExtensions` vlastnost určuje, které rozšíření by mělo být přidáno později v sekvenci.

## <a name="sample-azure-templates"></a>Ukázkové šablony Azure

Pokud chcete použít Azure Disk Encryption po jiné příponě, vložte `provisionAfterExtensions` vlastnost do bloku rozšíření AzureDiskEncryption. 

Tady je příklad pomocí "CustomScriptExtension", skriptu prostředí PowerShell, který inicializuje a formátuje disk s Windows a následovaný "AzureDiskEncryption":

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

Pokud chcete použít Azure Disk Encryption před jinou příponou, vložte `provisionAfterExtensions` vlastnost do bloku, aby následovala.

Tady je příklad s použitím "AzureDiskEncryption" následovaného "VMDiagnosticsSettings", rozšíření, které poskytuje možnosti monitorování a diagnostiky na virtuálním počítači Azure se systémem Windows:


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

Podrobnější šablonu najdete v těchto tématech:
* Použít rozšíření Azure Disk Encryption po vlastním skriptu prostředí, který formátuje disk (Linux): [Deploy-extseq-Linux-ADE-After-CustomScript. JSON](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Další kroky
- Další informace o sekvencování rozšíření: [zřizování rozšíření sekvence ve službě Virtual Machine Scale Sets](virtual-machine-scale-sets-extension-sequencing.md).
- Přečtěte si další `provisionAfterExtensions` informace o vlastnosti: [Microsoft. COMPUTE virtualMachineScaleSets/Extension reference Template](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption pro Virtual Machine Scale Sets](disk-encryption-overview.md)
- [Šifrování sady škálování virtuálních počítačů pomocí Azure CLI](disk-encryption-cli.md)
- [Šifrování virtuálních počítačů pomocí Azure PowerShell](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
