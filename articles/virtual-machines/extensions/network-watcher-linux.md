---
title: Rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux
description: Nasazení agenta sledování sítě na virtuálním počítači s Operačním systémem Linux pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 476241ad3d4077f2da0c513e3c989218701232ba
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255749"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozšíření virtuálního počítače o agenta Network Watcher pro Linux

## <a name="overview"></a>Přehled

[Azure Network Watcher](/azure/network-watcher/) je služba monitorování výkonu sítě, diagnostika a analýza, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače (VM) agenta sledovacího systému sítě je požadavek pro některé funkce sledování sítě na virtuálních počítačích Azure, jako je zachycení síťového provozu na vyžádání a další pokročilé funkce.

Tento článek podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače Agent network watcher pro Linux. Instalace agenta nenaruší nebo nevyžaduje restartování virtuálního počítače. Rozšíření můžete nasadit do virtuálních počítačů, které nasadíte. Pokud se virtuální počítač nasadí službou Azure, zkontrolujte dokumentaci pro službu a zjistěte, zda umožňuje instalaci rozšíření ve virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření Agent sledování sítě lze nakonfigurovat pro následující distribuce Linuxu:

| Distribuce | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 a 8 |
| Red Hat | 6 a 7 |
| Oracle Linux | 6,8+ a 7 |
| SUSE Linux Enterprise Server | 11 a 12 |
| Skok OpenSUSE | 42.3+ |
| CentOS | 6,5+ a 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce agenta sledování sítě vyžaduje, aby byl virtuální modul připojený k Internetu. Bez možnosti navázat odchozí připojení mohou některé funkce agenta sledování sítě selhat nebo se stát nedostupnými. Další informace o funkci Sledování sítě, která vyžaduje agenta, naleznete v[dokumentaci sledování sítě](/azure/network-watcher/).

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma rozšíření agenta sledovacího modulu sítě. Rozšíření nevyžaduje ani nepodporuje žádné uživatelem zadané nastavení. Rozšíření závisí na jeho výchozí konfiguraci.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatel | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablony Azure Resource Manager. Chcete-li nasadit rozšíření agenta sledovacího modulu sítě, použijte předchozí schéma json v šabloně.

## <a name="azure-classic-cli-deployment"></a>Nasazení klasického příkazového příkazového příkazu Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Následující příklad nasazuje rozšíření virtuálního počítače agenta sledování sítě do existujícího virtuálního počítače nasazeného prostřednictvím klasického modelu nasazení:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Následující příklad nasazuje rozšíření virtuálního aplikace Agent sledování sítě do existujícího virtuálního aplikace nasazeného prostřednictvím Správce prostředků:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Data o stavu nasazení rozšíření můžete načíst pomocí portálu Azure nebo příkazového příkazového příkazu Azure.

Následující příklad ukazuje stav nasazení rozšíření NetworkWatcherAgentLinux pro virtuální počítač nasazený prostřednictvím Správce prostředků pomocí azure cli:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete odkazovat na [dokumentaci Network Watcher](/azure/network-watcher/)nebo se obraťte na odborníky Azure Azure [a zásobníku overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**. Informace o používání podpory Azure najdete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
