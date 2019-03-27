---
title: Azure Disk Encryption a Azure škálovacích sad virtuálních počítačů nastavení posloupnosti rozšíření
description: Tento článek obsahuje pokyny týkající se povolení Microsoft Azure Disk Encryption pro virtuální počítače IaaS s Linuxem.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498141"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Použití Azure Disk Encryption pomocí škálování virtuálního počítače nastavte nastavení posloupnosti rozšíření

Rozšíření, jako je Azure disk encryption je přidat k Azure virtual machines škálovací sadu v zadané pořadí. Chcete-li tak učinit, použijte [nastavení posloupnosti rozšíření](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Obecně platí šifrování bude použito na disk:

- Po rozšíření nebo vlastní skripty, které připravují disky nebo svazky.
- Před nebo rozšíření vlastních skriptů, které přístup nebo zpracování dat na šifrovanými disky nebo svazky.

V obou případech `provisionAfterExtensions` vlastnost označuje rozšíření, které by se měl přidat později v sekvenci.

## <a name="sample-azure-templates"></a>Ukázkové šablony Azure

Pokud chcete mít Azure Disk Encryption byla aplikována za jinou příponu, umístit `provisionAfterExtensions` vlastnost v bloku AzureDiskEncryption rozšíření. 

Tady je příklad použití "CustomScriptExtension", který inicializuje a naformátuje disk Windows, za nímž následuje "AzureDiskEncryption" skript prostředí Powershell:

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

Pokud chcete mít použít Azure Disk Encryption před jinou příponu, umístit `provisionAfterExtensions` vlastnost v bloku rozšíření dodržovat.

Tady je příklad použití "AzureDiskEncryption" následované "VMDiagnosticsSettings", rozšíření, která poskytuje monitorování a Diagnostika možnosti virtuálního počítače založené na Windows Azure:


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

Další podrobné šablony najdete tady:
* Použití rozšíření Azure Disk Encryption po vlastní prostředí skript, který se naformátuje disk (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Použití rozšíření Azure Disk Encryption po vlastní skript prostředí Powershell, který inicializuje a naformátuje disk (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Použití rozšíření Azure Disk Encryption před vlastní skript prostředí Powershell, který inicializuje a naformátuje disk (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Další postup
- Další informace o nastavení posloupnosti rozšíření: [Pořadí zřizování rozšíření ve škálovacích sadách virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Další informace o `provisionAfterExtensions` vlastnost: [Referenční informace k šablonám virtualMachineScaleSets/rozšíření Microsoft.Compute](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
