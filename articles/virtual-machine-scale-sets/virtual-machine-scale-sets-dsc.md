---
title: Použití konfigurace požadovaného stavu s Virtual Machine Scale Sets
description: Konfigurace virtuálních počítačů pomocí Virtual Machine Scale Sets s rozšířením konfigurace požadovaného stavu Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 6939339ce8d0f324fbd9e14ba31d23a6febcfcbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373893"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Použití Virtual Machine Scale Sets s rozšířením Azure DSC
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) lze použít s obslužnou rutinou rozšíření [(DSC) konfigurace požadovaného stavu Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Virtual Machine Scale Sets nabízí způsob, jak nasadit a spravovat velký počet virtuálních počítačů, a v reakci na zatížení se můžou elasticky škálovat a vystavit. DSC se používá ke konfiguraci virtuálních počítačů tak, jak se dostanou do online režimu, aby používala produkční software.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Rozdíly mezi nasazením na Virtual Machines a Virtual Machine Scale Sets
Podkladová struktura šablony pro sadu škálování virtuálního počítače se mírně liší od jednoho virtuálního počítače. Konkrétně jeden virtuální počítač nasadí rozšíření pod uzlem "virtualMachines". Existuje položka typu "rozšíření", do které se přidá DSC do šablony.

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

Uzel sady škálování virtuálního počítače má oddíl Properties (vlastnosti) s atributem "VirtualMachineProfile", "extensionProfile". Do části rozšíření se přidá DSC.

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Chování pro sadu škálování virtuálního počítače
Chování pro sadu škálování virtuálního počítače se shoduje s chováním pro jeden virtuální počítač. Když se vytvoří nový virtuální počítač, automaticky se zřídí s rozšířením DSC. Pokud rozšíření vyžaduje novější verzi WMF, před pokračováním online se virtuální počítač restartuje. Jakmile je online, stáhne konfiguraci DSC. zip a zřídí ji na virtuálním počítači. Další podrobnosti najdete v článku [Přehled rozšíření Azure DSC](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další kroky
Projděte si [šablonu Azure Resource Manager pro rozšíření DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zjistěte, jak [rozšíření DSC zabezpečeně zpracovává přihlašovací údaje](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o obslužné rutině rozšíření Azure DSC najdete v tématu [Úvod do obslužné rutiny rozšíření konfigurace požadovaného stavu Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o prostředí PowerShell DSC najdete [v centru dokumentace PowerShellu](/powershell/scripting/dsc/overview/overview). 

