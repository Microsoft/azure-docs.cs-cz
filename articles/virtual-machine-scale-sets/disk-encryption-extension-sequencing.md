---
title: Sekvence rozšíření pro Azure Disk Encryption a Azure Virtual Machine Scale Sets
description: V tomto článku se dozvíte, jak povolit Microsoft Azure šifrování disku pro virtuální počítače s IaaS Linux.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90988031"
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
* Po vytvoření vlastního skriptu prostředí, který formátuje disk (Linux), použijte rozšíření Azure Disk Encryption: [deploy-extseq-linux-ADE-after-customscript.jsna](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Další kroky
- Další informace o sekvencování rozšíření: [zřizování rozšíření sekvence ve službě Virtual Machine Scale Sets](virtual-machine-scale-sets-extension-sequencing.md).
- Přečtěte si další informace o `provisionAfterExtensions` vlastnosti: [Microsoft. COMPUTE virtualMachineScaleSets/Extension reference Template](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption pro Virtual Machine Scale Sets](disk-encryption-overview.md)
- [Šifrování sady škálování virtuálních počítačů pomocí Azure CLI](disk-encryption-cli.md)
- [Šifrování virtuálních počítačů pomocí Azure PowerShell](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
