---
title: Pomocí Desired State Configuration se Škálovacími sadami virtuálních počítačů | Dokumentace Microsoftu
description: Použití virtuálního počítače škálovacích sad s Azure rozšíření DSC
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: 24a37d352413ff9ac55ce8e189691988383950f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728448"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Použití virtuálního počítače škálovacích sad s Azure rozšíření DSC
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) jde použít s [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) obslužné rutiny rozšíření. Škálovací sady virtuálních počítačů poskytují způsob, jak nasadit a spravovat velké počty virtuálních počítačů a dá se pružně škálovat a odhlašování v reakci na zatížení. DSC se používá ke konfiguraci virtuálních počítačů při jejich režimu online, takže jsou spuštěné provozního softwaru.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Rozdíly mezi nasazením pro virtuální počítače a Škálovací sady virtuálních počítačů
Podkladová struktura šablony pro škálovací sadu virtuálních počítačů se mírně liší od jednoho virtuálního počítače. Konkrétně jeden virtuální počítač nasadí rozšíření v uzlu "virtuálních počítačů". Existuje položka typu "rozšíření", kde DSC je přidána do šablony

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Uzel sady škálování virtuálního počítače nemá oddíl "properties" "VirtualMachineProfile", "extensionProfile" atribut. DSC se přidá do části "rozšíření"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Chování pro Škálovací sadu virtuálních počítačů
Chování pro škálovací sadu virtuálních počítačů je stejné jako chování pro jeden virtuální počítač. Když se vytvoří nový virtuální počítač, automaticky zřídí se s rozšíření DSC. Pokud rozšíření vyžaduje novější verzi WMF, před chystají restartování virtuálního počítače. Až bude online, stáhne ZIP konfigurace DSC a zřízení na virtuálním počítači. Další podrobnosti najdete v [přehled rozšíření DSC Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další postup
Zkontrolujte [šablony Azure Resource Manageru pro rozšíření DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zjistěte, jak [rozšíření DSC bezpečně zpracovává pověření](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o obslužné rutiny rozšíření DSC Azure najdete v tématu [Úvod do obslužné rutiny rozšíření Azure Desired State Configuration](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o prostředí PowerShell DSC [navštívit centrum dokumentace Powershellu](https://msdn.microsoft.com/powershell/dsc/overview). 

