---
title: Rozšíření virtuálního počítače Azure monitoru závislostí pro Windows | Dokumentace Microsoftu
description: Nasazení agenta Azure monitoru závislosti na virtuálním počítači Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 34dd872db199a4c10e9f321457188b7f7642944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120226"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače Azure monitoru závislostí pro Windows

Azure Monitor pro funkci mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Rozšíření virtuálního počítače agenta závislosti virtuálních počítačů Azure pro Windows je publikována a podporované společností Microsoft. Instalaci agenta závislostí na virtuálních počítačích Azure. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače agenta závislosti virtuálních počítačů Azure pro Windows.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Operační systém

Rozšíření agenta závislosti virtuálních počítačů Azure pro Windows můžete spustit na podporovaných operačních systémů uvedených v [podporované operační systémy](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) části Azure Monitor pro článek nasazení virtuálních počítačů.

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma rozšíření Azure VM závislost agenta na virtuálním počítači Windows Azure.

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

| Název | Příklad hodnoty / |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure pomocí šablon Azure Resource Manageru můžete nasadit. Schéma JSON, které jsou podrobně popsané v předchozí části šablony Azure Resource Manageru můžete použít ke spuštění rozšíření agenta závislostí virtuálního počítače Azure při nasazení šablony Azure Resource Manageru.

JSON pro rozšíření virtuálního počítače může být vnořena v prostředku virtuálního počítače. Nebo je možné je umístit na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění ve formátu JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources).

V následujícím příkladu se předpokládá, že rozšíření agenta závislostí je vnořená do prostředku virtuálního počítače. Když je vnořovat rozšíření prostředků, ve formátu JSON najdete v `"resources": []` objekt virtuálního počítače.


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

Umístíte-li v kořenovém adresáři šablony rozšíření JSON, název prostředku obsahuje odkaz na nadřazený virtuální počítač. Typ odráží vnořené konfigurace.

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

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

Můžete použít `Set-AzVMExtension` příkaz pro nasazení do existujícího virtuálního počítače rozšíření virtuálního počítače agenta závislostí. Před spuštěním příkazu konfigurace veřejných a privátních muset být uložena v tabulce hash prostředí PowerShell.

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

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí modulu Azure Powershellu:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o tom, jak pomocí podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
