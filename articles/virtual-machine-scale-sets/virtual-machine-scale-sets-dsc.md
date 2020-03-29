---
title: Použití konfigurace požadovaného stavu se sadami škálovacích zařízení virtuálních počítačů
description: Použití škálovacísady virtuálních počítačů s rozšířením konfigurace požadovaného stavu Azure ke konfiguraci virtuálních počítačů.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278075"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Použití škálovacích sad virtuálních strojů s rozšířením Azure DSC
[Škálovací sady virtuálních počítačů](virtual-machine-scale-sets-overview.md) lze použít s obslužnou rutinou rozšíření [Konfigurace požadovaného stavu Azure (DSC).](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Škálovací sady virtuálních počítačů poskytují způsob nasazení a správy velkého počtu virtuálních počítačů a mohou být elasticky škálovat a zmenšovat v reakci na zatížení. DSC se používá ke konfiguraci virtuálních počítače, jakmile jsou online, takže jsou spuštěny produkční software.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Rozdíly mezi nasazením do virtuálních počítačů a škálovacích sad virtuálních počítačů
Základní struktura šablony pro škálovací sadu virtuálních strojů se mírně liší od jednoho virtuálního počítače. Konkrétně jeden virtuální počítač nasazuje rozšíření pod uzlem "virtualMachines". K dispozici je položka typu "rozšíření", kde DSC je přidán do šablony

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

Uzel škálovací sady virtuálního počítače má oddíl "vlastnosti" s atributem "VirtualMachineProfile", "extensionProfile". DSC je přidán pod "rozšíření"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Chování pro škálovací sadu virtuálních strojů
Chování škálovací sady virtuálních strojů je shodné s chováním pro jeden virtuální počítač. Když se vytvoří nový virtuální jenový, automaticky zřídí s rozšířením DSC. Pokud rozšíření vyžaduje novější verzi WMF, virtuální počítač se před online přepne do režimu restartování. Jakmile je online, stáhne konfiguraci DSC .zip a zřídí ji na virtuálním počítači. Další podrobnosti najdete v [přehledu rozšíření Azure DSC](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další kroky
Zkontrolujte [šablonu Správce prostředků Azure pro rozšíření DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zjistěte, jak [rozšíření DSC bezpečně zpracovává přihlašovací údaje](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o obslužné rutině rozšíření Azure DSC najdete [v tématu Úvod do obslužné rutiny rozšíření konfigurace požadovaného stavu Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o prostředí PowerShell DSC [naleznete v dokumentačním centru prostředí PowerShell](/powershell/scripting/dsc/overview/overview). 

