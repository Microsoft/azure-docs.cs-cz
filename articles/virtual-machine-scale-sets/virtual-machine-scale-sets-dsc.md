---
title: Pomocí konfigurace požadovaného stavu s sady škálování virtuálního počítače | Microsoft Docs
description: Použití škálování virtuálních počítačů sad s Azure rozšíření DSC
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
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Použití škálování virtuálních počítačů sad s Azure rozšíření DSC
[Sady škálování virtuálního počítače](virtual-machine-scale-sets-overview.md) lze použít s [Azure požadovaného stavu konfigurace (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) rozšíření obslužné rutiny. Sady škálování virtuálního počítače zadejte způsob, jak nasadit a spravovat velké počty virtuálních počítačů a můžete Elasticky škálovat a odhlašování v reakci na zatížení. DSC slouží ke konfiguraci virtuálních počítačů jako jejich uvést do režimu online, používají produkční softwaru.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Rozdíly mezi nasazením do virtuálních počítačů a sady škálování virtuálního počítače
Podkladová struktura šablony pro sadu škálování virtuálního počítače se mírně liší od jeden virtuální počítač. Konkrétně jeden virtuální počítač nasadí rozšíření pod uzlem "virtualMachines". Existuje položka typu "rozšíření", kde je DSC přidat do šablony

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

Oddíl "vlastnosti" "VirtualMachineProfile", "extensionProfile" atribut má uzel sady škálování virtuálního počítače. V části "rozšíření" je přidána DSC

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
Chování pro sadu škálování virtuálního počítače je stejný jako u chování pro jeden virtuální počítač. Když je vytvořen nový virtuální počítač, je automaticky zajištěna s příponou DSC. Pokud na novější verzi WMF je požadován pro rozšíření, virtuální počítač se restartuje před uveden do režimu online. Jakmile je online, stáhne .zip konfigurace DSC a zřídit ve virtuálním počítači. Další podrobnosti naleznete v [přehled rozšíření DSC Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Další postup
Zkontrolujte [šablony Azure Resource Manageru pro rozšíření DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zjistěte, jak [rozšíření DSC bezpečně zpracovává pověření](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o obslužná rutina rozšíření Azure DSC najdete v tématu [Úvod do rozšíření obslužné rutiny konfigurace požadovaného stavu Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Další informace o DSC Powershellu [přejděte do centra dokumentace k prostředí PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

