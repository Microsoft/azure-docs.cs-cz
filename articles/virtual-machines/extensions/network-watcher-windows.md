---
title: Rozšíření Azure VM Network Watcher Agent pro Windows | Dokumentace Microsoftu
description: Nasazení agenta sledovací proces sítě na virtuálním počítači Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976911"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Síť rozšíření virtuálních počítačů sledovacích procesů agenta pro Windows

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je síť výkonu monitorování, Diagnostika a analýza služby, který umožňuje sledování sítě Azure. Rozšíření virtuálního počítače Network Watcher Agent představuje požadavek pro zachycení síťového provozu na vyžádání a další pokročilé funkce na virtuálních počítačích Azure.


Tento dokument podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače Network Watcher Agent pro Windows. Instalace agenta není narušit nebo vyžadují restartování virtuálního počítače. Nasazení rozšíření do virtuálních počítačů, které nasadíte. Pokud je nasazený virtuální počítač pomocí služby Azure, naleznete v dokumentaci k service k určení, zda it specialistovi instalace rozšíření ve virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Network Watcher Agent rozšíření pro Windows můžete spustit na Windows Server 2008 R2, 2012, 2012 R2 a 2016 uvolní. Nano Server není v tuto chvíli nepodporuje.

### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce Network Watcher Agent vyžaduje, aby cílový virtuální počítač připojen k Internetu. Bez možnosti navázat odchozí připojení nebude možné nahrávat zachytávání paketů na svůj účet úložiště Network Watcher Agent. Další podrobnosti najdete [dokumentace ke službě Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření Network Watcher Agent. Rozšíření ani vyžaduje, ani podporuje uživatelem zadané nastavení a spoléhá na její výchozí konfiguraci.

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

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatele | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru můžete nasadit. Schéma JSON, které jsou podrobně popsané v předchozí části šablony Azure Resource Manageru můžete použít ke spuštění rozšíření Network Watcher Agent při nasazení šablony Azure Resource Manageru.

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

Použití `Set-AzVMExtension` příkaz pro nasazení do existujícího virtuálního počítače rozšíření virtuálního počítače Network Watcher Agent:

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

Data o stavu nasazení rozšíření můžete načíst z webu Azure portal a PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete naleznete v dokumentaci Network Watcher uživatelská příručka nebo se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
