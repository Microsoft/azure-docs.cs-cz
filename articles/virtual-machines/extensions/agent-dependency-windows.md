---
title: Rozšíření virtuálního počítače závislosti Azure Monitor pro Windows
description: Nasaďte agenta závislostí Azure Monitor na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: windows
ms.date: 03/29/2019
ms.openlocfilehash: 429cc01f466c55283985729c3395bb2137e38fa6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566294"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače závislosti Azure Monitor pro Windows

Funkce mapa Azure Monitor pro virtuální počítače získá data od agenta závislostí společnosti Microsoft. Rozšíření virtuálního počítače Azure VM Dependency Agent pro Windows je publikované a podporované Microsoftem. Rozšíření nainstaluje agenta závislostí na virtuální počítače Azure. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače agenta závislostí virtuálních počítačů Azure pro Windows.

## <a name="operating-system"></a>Operační systém

Rozšíření agenta závislostí virtuálních počítačů Azure pro Windows je možné spustit s podporovanými operačními systémy uvedenými v části [podporované operační systémy](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) v článku věnovaném nasazení Azure monitor pro virtuální počítače.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta závislostí virtuálních počítačů Azure na virtuálním počítači Azure s Windows.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| vydavatel | Microsoft. Azure. Monitoring. DependencyAgent |
| typ | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablon Azure Resource Manager. Pomocí schématu JSON popsaného v předchozí části šablony Azure Resource Manager můžete během nasazování Azure Resource Manager šablony spustit rozšíření agenta závislostí virtuálních počítačů Azure.

JSON pro rozšíření virtuálního počítače může být vnořený do prostředku virtuálního počítače. Nebo ho můžete umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění formátu JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md).

Následující příklad předpokládá, že rozšíření agenta závislosti je vnořené uvnitř prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěn v `"resources": []` objektu virtuálního počítače.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Když umístíte JSON rozšíření do kořenového adresáře šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač. Typ odráží vnořenou konfiguraci.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Pomocí `Set-AzVMExtension` příkazu můžete nasadit rozšíření virtuálního počítače agenta závislosti na existující virtuální počítač. Před spuštěním příkazu musí být veřejné a privátní konfigurace uložené v zatřiďovací tabulce PowerShellu.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Nebo můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o tom, jak používat podporu Azure, najdete v části [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
