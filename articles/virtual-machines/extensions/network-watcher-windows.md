---
title: Rozšíření virtuálního počítače o agenta Azure Network Watcher pro Windows
description: Nasaďte agenta Network Watcher na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 02/14/2017
ms.openlocfilehash: d336a39714712e5436086e22ad24fc942a7d850a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563536"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače Network Watcher agenta pro Windows

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače Network Watcher agentem je požadavek na zachytávání síťového provozu na vyžádání a další pokročilé funkce na virtuálních počítačích Azure.


Tento dokument podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače Network Watcher agenta pro systém Windows. Instalace agenta neruší nebo vyžaduje restartování virtuálního počítače. Rozšíření můžete nasadit do virtuálních počítačů, které nasadíte. Pokud je virtuální počítač nasazený službou Azure, Projděte si dokumentaci ke službě, kde zjistíte, jestli umožňuje nainstalovat rozšíření na virtuálním počítači.

## <a name="prerequisites"></a>Předpoklady

### <a name="operating-system"></a>Operační systém

Rozšíření agenta Network Watcher pro systém Windows lze spustit pro systémy Windows Server 2008 R2, 2012, 2012 R2, 2016 a 2019. Nano Server se v tuto chvíli nepodporuje.

### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce agenta Network Watcher vyžadují, aby byl cílový virtuální počítač připojený k Internetu. Bez možnosti navázání odchozích připojení agent Network Watcher nebude moci nahrávat zachytávání paketů do svého účtu úložiště. Další podrobnosti najdete v [dokumentaci k Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Network Watcher. Přípona nevyžaduje ani žádná nastavení zadaná uživatelem a spoléhá na její výchozí konfiguraci.

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

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatel | Microsoft. Azure. NetworkWatcher |
| typ | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablon Azure Resource Manager. Můžete použít schéma JSON popsané v předchozí části šablony Azure Resource Manager ke spuštění rozšíření agenta Network Watcher během nasazování šablony Azure Resource Manager.

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Pomocí `Set-AzVMExtension` příkazu nasaďte rozšíření Network Watcher agenta virtuálního počítače do existujícího virtuálního počítače:

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

### <a name="troubleshooting"></a>Poradce při potížích

Data o stavu nasazení rozšíření můžete načíst z Azure Portal a PowerShellu. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v jakémkoli bodě tohoto článku, můžete se přečtěte v dokumentaci k Network Watcher uživatelské příručce nebo se obraťte na odborníky na Azure na [fórech MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
