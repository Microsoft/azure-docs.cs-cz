---
title: Rozšíření virtuálního počítače o agenta Azure Network Watcher pro Windows
description: Nasazení agenta sledování sítě ve virtuálním počítači se systémem Windows pomocí rozšíření virtuálního počítače
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: f226e240a59b33c2913919495410b1a4923b4902
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261665"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače agenta sledování sítě pro Windows

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro monitorování, diagnostiku a analýzu výkonu sítě, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače agenta sledování sítě je požadavek pro zachytávání síťového provozu na vyžádání a další pokročilé funkce na virtuálních počítačích Azure.


Tento dokument podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače Network Watcher Agent pro Windows. Instalace agenta nenaruší nebo nevyžaduje restartování virtuálního počítače. Rozšíření můžete nasadit do virtuálních počítačů, které nasadíte. Pokud se virtuální počítač nasadí službou Azure, zkontrolujte dokumentaci pro službu a zjistěte, zda umožňuje instalaci rozšíření ve virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření Agent sledování sítě pro Windows lze spustit ve verzích Windows Server 2008 R2, 2012, 2012 R2 a 2016. Nano Server není v současné době podporován.

### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce agenta sledování sítě vyžadují připojení cílového virtuálního počítače k Internetu. Bez možnosti navázat odchozí připojení nebude agent sledovacího procesu sítě moci nahrát zachytávání paketů do vašeho účtu úložiště. Další podrobnosti naleznete v [dokumentaci k programu Sledování sítě](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma rozšíření agenta sledovacího modulu sítě. Rozšíření nevyžaduje, ani podporuje žádné uživatelem zadané nastavení a spoléhá na jeho výchozí konfiguraci.

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
        "type": "NetworkWatcherAgentWindows",
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
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části v šabloně Správce prostředků Azure můžete použít ke spuštění rozšíření agenta sledování sítě během nasazení šablony Azure Resource Manager.

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Pomocí `Set-AzVMExtension` příkazu nasaďte rozšíření virtuálního počítače agenta sledování sítě do existujícího virtuálního počítače:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Data o stavu nasazení rozšíření můžete načíst z portálu Azure a PowerShellu. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete odkazovat na dokumentaci network watcher user guide nebo se obraťte na odborníky Azure Azure [a zásobníku overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
