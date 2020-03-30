---
title: Rozšíření virtuálního počítače Závislost Azure Monitor pro Windows
description: Nasazení agenta závislostí monitoru Azure ve virtuálním počítači s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250670"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače Závislost Azure Monitor pro Windows

Funkce Azure Monitor for VMs Map získává svá data od agenta závislostí Microsoftu. Rozšíření virtuálního počítače agenta virtuálního počítače Azure pro Windows publikuje a podporuje Microsoft. Rozšíření nainstaluje agenta závislostí na virtuálních počítačích Azure. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače agenta závislostí virtuálního počítače Azure pro Windows.

## <a name="operating-system"></a>Operační systém

Rozšíření agenta závislostí virtuálních počítače Azure pro Windows lze spustit proti podporovaným operačním systémům uvedeným v části [Podporované operační systémy](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) v článku nasazení Azure Monitor for VM.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta závislostí virtuálních počítačích Azure na virtuálním počítači Azure Windows.

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

| Name (Název) | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| vydavatel | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části v šabloně Azure Resource Manager umítnete rozšíření agenta závislostí virtuálních počítače Azure během nasazení šablony Azure Resource Manager.

JSON pro rozšíření virtuálního počítače lze vnořit do prostředku virtuálního počítače. Nebo jej můžete umístit na kořenovou nebo nejvyšší úroveň šablony JSON správce prostředků. Umístění JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu [Nastavení názvu a typu podřízených prostředků](../../azure-resource-manager/templates/child-resource-name-type.md).

Následující příklad předpokládá, že rozšíření agenta závislostí je vnořené uvnitř prostředku virtuálního počítače. Při vnoření prostředku rozšíření JSON je `"resources": []` umístěn v objektu virtuálního počítače.


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

Když umístíte rozšíření JSON v kořenovém adresáři šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač. Typ odráží vnořenou konfiguraci.

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

Pomocí příkazu `Set-AzVMExtension` můžete nasadit rozšíření virtuálního počítače agenta závislostí do existujícího virtuálního počítače. Před spuštěním příkazu je třeba uložit veřejné a soukromé konfigurace v tabulce hash prostředí PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z webu Azure Portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Nebo můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**. Informace o tom, jak používat podporu Azure, načtete-li [časté dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
